# Einwilligung & Double-Opt-In für Marketing-Mail

> **Wohnsitz** für Art. 6 I a + Art. 7 DSGVO **im Marketing-Kontext**. Norm-Wohnsitz Art. 6 / 7 generell und Art. 28 AVV bleibt `dsgvo-third-country-transfer/ROLES.md`. Diese Datei zeigt, was Einwilligung für werbliche E-Mail in DACH praktisch heißt: Beweisbarkeit, Confirm-Mail-Inhalt, Re-Subscribe-Falle, Kopplungsverbot, Kinder-Newsletter, Co-Sponsor.

## Was hier anders ist als in `ROLES.md`

`ROLES.md` erklärt Art. 6 / 7 DSGVO als Norm allgemein — wann Einwilligung Rechtsgrundlage ist, was Bedingungen sind. Diese Datei behandelt drei Marketing-spezifische Fragen:

1. **Beweisbarkeit:** Werbe-Einwilligung muss vor Gericht beweisbar sein (Art. 7 I DSGVO; bei Telefonwerbung gilt zusätzlich BGH I ZR 164/09 — rein elektronisches DOI ungeeignet als Telefon-Einwilligungsbeweis). Welche Felder muss der DOI-Datensatz enthalten?
2. **Confirm-Mail-Inhalt:** Was darf und was darf nicht in der Bestätigungs-Mail stehen (BGH VI ZR 134/15 — „No-Reply": Auto-Reply mit Werbezusatz = Eingriff Persönlichkeitsrecht)?
3. **Lead-Magnet, Kinder, Shared Lists, Re-Subscribe:** Sonderkonstellationen, die im Marketing-Code regelmäßig auftauchen.

UWG-Lauterkeitsrecht (§ 7 UWG, AT TKG, CH UWG) wohnt in `UWG-7.md` — diese Datei behandelt nur die DSGVO-Einwilligung.

## Bedingungen einer wirksamen Marketing-Einwilligung (Art. 7 DSGVO)

Vier Bedingungen kumulativ:

1. **Freiwillig (Art. 7 II + IV)** — kein Kopplungsverbots-Verstoß: die Einwilligung darf nicht Bedingung für einen Vertrag sein, der ohne sie auskommt. „Newsletter-Anmeldung erforderlich für PDF-Download" verletzt Art. 7 IV.
2. **Informiert (Art. 13)** — Empfänger weiß, wer der Verantwortliche ist, welche Daten verarbeitet werden, zu welchem Zweck, an welche Empfänger, wie lange, mit welchen Rechten. Der Hinweis auf die Datenschutzerklärung muss am Anmeldeformular sichtbar sein, nicht im AGB-Footer versteckt.
3. **Spezifisch (EDPB Guidelines 05/2020)** — pro Zweck eine Einwilligung. Newsletter-Anmeldung + Personalisierungs-Tracking + Drittland-Übertragung sind drei verschiedene Zwecke und brauchen drei separate Häkchen. Pauschale „Ich stimme allem zu"-Klauseln sind unwirksam.
4. **Eindeutig (Art. 4 Nr. 11)** — aktive Handlung. Default-aktivierte Checkboxes sind keine Einwilligung (EuGH C-673/17 Planet49). Die Box muss leer sein, der Empfänger setzt das Häkchen aktiv.

## Double-Opt-In-Flow als Code-Skizze

Das DOI-Verfahren teilt die Einwilligung in zwei Schritte: Anmeldung (mit Eingabe der E-Mail) und Bestätigung (Klick auf Confirm-Link). Erst der Klick macht die Einwilligung beweissicher.

### DB-Schema (PostgreSQL)

```sql
CREATE TABLE subscription_consents (
  id BIGSERIAL PRIMARY KEY,
  email TEXT NOT NULL,                          -- Klartext bis Widerruf, danach löschen
  email_hash CHAR(64) NOT NULL,                 -- HMAC-SHA-256 mit serverseitigem Pepper, dauerhaft (für Suppression-Pflicht); plain SHA-256 ist unsicher (E-Mail-Adressen low-entropy → Rainbow-Tables trivial)
  ip_address INET,                              -- siehe IP-ADDRESSES.md (Kürzung nach Zweckende)
  user_agent TEXT,
  form_url TEXT NOT NULL,                       -- Seite, auf der die Anmeldung erfolgte
  purposes JSONB NOT NULL,                      -- {"newsletter":true,"tracking":false,"sharing":false}
  consented_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  confirmation_token_hash CHAR(64),             -- SHA-256 des Tokens (Token selbst nie speichern)
  confirmation_token_expires_at TIMESTAMPTZ,
  confirmed_at TIMESTAMPTZ,                     -- NULL = noch nicht bestätigt
  withdrawn_at TIMESTAMPTZ,                     -- NULL = aktiv
  retention_until TIMESTAMPTZ                   -- bei Widerruf: confirmed_at + 3 J UWG-Verjährung
);

CREATE INDEX ON subscription_consents (email_hash);
CREATE INDEX ON subscription_consents (confirmation_token_hash) WHERE confirmation_token_hash IS NOT NULL;
```

### Subscribe-Endpoint (TypeScript / Node)

```ts
import crypto from 'node:crypto';

export async function POST(req: Request) {
  const { email, formUrl, purposes } = await req.json();

  // Token kryptographisch erzeugen, NIE den Klartext-Token speichern.
  // Token: 256 bit Zufall → plain SHA-256 reicht (volle Entropie, Brute-Force unmöglich).
  // Email-Hash: HMAC-SHA-256 mit serverseitigem Pepper (Pepper aus KMS) — plain SHA-256 wäre über Rainbow-Tables trivial reversibel.
  const tokenBytes = crypto.randomBytes(32);
  const token = tokenBytes.toString('hex');
  const tokenHash = crypto.createHash('sha256').update(tokenBytes).digest('hex');
  const emailNorm = email.toLowerCase().trim();
  const emailHash = crypto
    .createHmac('sha256', process.env.SUPPRESSION_PEPPER!) // server-seitig, NICHT mit Pepper-Rotation, sonst Suppression-Match bricht
    .update(emailNorm)
    .digest('hex');

  // Suppression-Check vor Insert (siehe UNSUBSCRIBE-AND-RETENTION.md)
  if (await isSuppressed(emailHash)) {
    return new Response('Bereits abgemeldet — neue Anmeldung erfordert Bestätigung Ihres Wunschs.', { status: 200 });
  }

  await db.query(
    `INSERT INTO subscription_consents
     (email, email_hash, ip_address, user_agent, form_url, purposes,
      confirmation_token_hash, confirmation_token_expires_at)
     VALUES ($1, $2, $3, $4, $5, $6, $7, NOW() + INTERVAL '7 days')`, // 7d für klassischen Newsletter; siehe Token-TTL-Sektion unten — bei Lead-Magnet/High-Risk auf 48h kürzen
    [email, emailHash, maskIp(req.headers.get('x-forwarded-for') ?? ''), req.headers.get('user-agent'),
     formUrl, JSON.stringify(purposes), tokenHash]
  );

  await sendConfirmationMail(email, token);
  return Response.json({ ok: true, message: 'Bitte E-Mail-Postfach für Bestätigungs-Mail prüfen.' });
}
```

### Confirm-Endpoint

```ts
export async function GET(req: Request) {
  const url = new URL(req.url);
  const token = url.searchParams.get('t');
  if (!token) return new Response('Bad request', { status: 400 });

  const tokenHash = crypto.createHash('sha256').update(Buffer.from(token, 'hex')).digest('hex');

  const result = await db.query(
    `UPDATE subscription_consents
     SET confirmed_at = NOW(),
         confirmation_token_hash = NULL,
         confirmation_token_expires_at = NULL
     WHERE confirmation_token_hash = $1
       AND confirmation_token_expires_at > NOW()
       AND confirmed_at IS NULL
       AND withdrawn_at IS NULL
     RETURNING id`,
    [tokenHash]
  );

  if (result.rowCount === 0) {
    return new Response('Token ungültig oder abgelaufen. Neue Anmeldung notwendig.', { status: 400 });
  }
  return new Response('Anmeldung bestätigt. Vielen Dank.', { status: 200 });
}
```

### Token-TTL — gestaffelt nach Risiko

| Use-Case | Empfohlene TTL | Begründung |
|---|---|---|
| Klassischer Newsletter / Service-Mail-Opt-In | **7 Tage** | deckt Urlaub/Wochenende; akzeptables Friction-/Sicherheitsverhältnis |
| Lead-Magnet / Cold-DOI / B2B-Outreach-Confirm | **48 Stunden** | erhöhtes Phishing-Risiko bei kompromittiertem Postfach; Lead-Magnet wird sofort eingelöst oder gar nicht |
| Hochsensibles (Gesundheit, Finanz, Kinder) | **24 Stunden** | Art. 9 / Art. 8 DSGVO erfordern restriktive Schutzmaßnahmen |

Abgelaufene `pending`-Datensätze (alle TTL-Klassen) per Cron-Job physisch löschen — Datenminimierung Art. 5 I c.

### URL-Token-Härtung

Das Confirm-Token wandert per `GET /confirm?t=<hex>` in die Empfänger-Mail. Drei Risiko-Vektoren, die du auf der Confirm-Page entschärfen musst:

- **E-Mail-Sicherheits-Gateways (Microsoft Defender for Office 365 Safe Links, Cloudflare Email Routing, Proofpoint URL Defense)** rufen Links **automatisch per HEAD oder GET vorab auf**, um Phishing zu erkennen. Das konsumiert das Token vor dem User-Klick. Lösung: das atomare `UPDATE` (oben) ist idempotent gegen Mehrfach-Klicks, aber die Confirm-Page sollte **side-effect-frei** sein bei Pre-Fetches.
- **Browser-Historie + HTTP-Referer + Server-Access-Logs** speichern Query-Parameter im Klartext. Lösung: `Referrer-Policy: no-referrer` als Response-Header der Confirm-Page; **keine 3rd-party Skripte / Pixel / Analytics** auf der Confirm-Page laden, bevor das Token validiert ist; Token niemals in Logging-/Analytics-Events einbauen.
- **UTM-Parameter / Marketing-Tools** dürfen das Token nicht weiterreichen. Lösung: nach erfolgreichem Confirm per 302 auf eine Token-freie Erfolgsseite redirecten.

## Confirm-Mail-Inhalt — was darf rein, was nicht (BGH VI ZR 134/15 — „No-Reply")

Der BGH hat am 15.12.2015 entschieden (VI ZR 134/15 — „No-Reply"): automatisch generierte Bestätigungs-E-Mails mit werblichem Zusatz sind **rechtswidriger Eingriff in das allgemeine Persönlichkeitsrecht** und begründen einen Unterlassungsanspruch. Werbung in Confirm-Mails / Auto-Reply-/No-Reply-Mails ist damit kein UWG-Spezialthema, sondern allgemein-deliktisch unzulässig — bestätigt durch ständige Linie der Instanzgerichte (OLG München 29 U 1682/12, LG Stendal 22 S 87/20).

**Erlaubt in der Confirm-Mail:**

- Aufforderung zur Bestätigung mit Confirm-URL und Hinweis, dass ohne Klick keine Anmeldung wirksam wird
- Hinweis auf die Datenschutzerklärung (Link)
- Hinweis auf das jederzeitige Widerrufsrecht
- Absender-Identifikation (Impressums-Pflichten)
- Kurze Erklärung, was passiert, falls die Anmeldung nicht vom Empfänger stammt (ignorieren genügt)

**Nicht erlaubt:**

- Werbe-Banner, Produkt-Empfehlungen, Cross-Sell
- Rabatt-Codes „nur für Anmelder", „Bonus zur Anmeldung"
- Angebot weiterer Newsletter-Listen
- Open-Tracking-Pixel (zählt als Marketing-Tracking, siehe `TRACKING-IN-MAIL.md`)
- Klick-Tracking-Redirects auf Werbe-Ziele

Praktische Konsequenz: das Confirm-Mail-Template ist eine reine Service-Mail — Klassifikation `transactional` (siehe `SERVICE-VS-MARKETING.md`).

## Beweisbarkeit (Art. 7 I DSGVO)

Im Streitfall muss der Versender beweisen, dass der konkrete Empfänger eingewilligt hat. Erforderliche Felder im DOI-Datensatz (siehe Schema oben):

| Feld | Zweck | Aufbewahrung |
|---|---|---|
| `email` (Klartext) | Identifikation | bis Widerruf |
| `email_hash` (HMAC-SHA-256 + Pepper) | Suppression nach Widerruf | dauerhaft |
| `ip_address` (gekürzt) | Beweis Anmeldung von welchem Anschluss | 30–90 Tage in Klartext, danach hashen — siehe `dsgvo-auth-and-logging/IP-ADDRESSES.md` |
| `user_agent` | Beweis-Plausibilität | wie IP |
| `form_url` | Beweis welche Seite, welche AGB-Version | bis Widerruf |
| `purposes` (JSONB) | welche Zwecke wurden eingewilligt | bis Widerruf |
| `consented_at` | Zeitstempel Anmeldung | bis Widerruf |
| `confirmed_at` | Zeitstempel Bestätigung | bis Widerruf |

Aufbewahrung nach Widerruf: 3 Jahre UWG-Verjährung (§ 11 UWG) als Schutz gegen Falsch-Behauptung „nie eingewilligt" — danach löschen oder vollständig pseudonymisieren.

## Re-Subscribe-Falle

Hat ein Empfänger sich abgemeldet, ist der ursprüngliche DOI-Datensatz nicht reaktivierbar. Eine erneute Anmeldung erfordert einen frischen DOI-Durchlauf.

```sql
-- Pattern: vor dem Insert prüfen, ob es einen Widerruf gibt
SELECT confirmed_at, withdrawn_at
FROM subscription_consents
WHERE email_hash = $1
ORDER BY id DESC LIMIT 1;
```

- Wenn `withdrawn_at IS NOT NULL` für die jüngste Zeile → neuer DOI-Flow notwendig (komplett neue Anmeldung mit neuer Bestätigungs-Mail).
- „Reaktivierung" durch internes Flag-Toggle ohne neue Bestätigung verstößt gegen Art. 7 III DSGVO und ist UWG-§-7-relevant.

## Single-Opt-In als Anti-Pattern

In Deutschland und Österreich gilt Single-Opt-In für Werbe-Mails als abmahnfähig. Wer die Einwilligung nicht durch DOI dokumentiert, kann ihre Wirksamkeit nicht beweisen — Beweispflicht trifft den Versender (Art. 7 I DSGVO; BGH-Linie u.a. I ZR 164/09 zur Ungeeignetheit rein elektronischer DOI-Beweise bei verwandten Werbeformen). Bereits eine einmalige unverlangte Werbe-Mail ist nach BGH I ZR 218/07 („E-Mail-Werbung II") rechtswidriger Eingriff in den Gewerbebetrieb (B2B nicht frei).

Schweiz-Sonderfall: SOI ist in der Praxis tolerierter, aber bei DACH-Mischlisten unsicher. Details + revDSG-Cross-Link in `UWG-7.md` (CH-Sektion).

Maintainer-Empfehlung: einheitlich DOI fahren, auch wenn der Verteiler nur Schweizer Empfänger enthält — die Komplexität, später Listen zu trennen, ist höher als der Aufwand DOI für alle.

## Kinder-Newsletter (Art. 8 DSGVO)

Wenn die Zielgruppe potenziell Kinder unter 16 Jahren erreicht (Spielzeug, Schule, Gaming, Comics, Kinder-Streaming, Edu-Plattformen), greift Art. 8 DSGVO. Der Wortlaut ist eindeutig: „Beruht die Verarbeitung auf einer Einwilligung [...] und wird das Kind nicht das 16. Lebensjahr vollendet haben, ist die Verarbeitung nur rechtmäßig, sofern und soweit diese Einwilligung durch den Träger der elterlichen Verantwortung [...] erteilt oder mit dessen Zustimmung erteilt wird. Der Verantwortliche unternimmt unter Berücksichtigung der verfügbaren Technik **angemessene Anstrengungen, um sich in solchen Fällen zu vergewissern, dass die Einwilligung [...] erteilt oder mit dessen Zustimmung erteilt wurde**" (Art. 8 Abs. 1 und 2 DSGVO).

### Altersgrenze DACH

- **Deutschland:** 16 Jahre (Art. 8 Abs. 1 DSGVO direkt, keine Absenkung durch BDSG). Art. 8 Abs. 1 Unterabsatz 2 erlaubt Mitgliedstaaten eine Absenkung auf nicht weniger als das vollendete 13. Lebensjahr — DE hat davon keinen Gebrauch gemacht.
- **Österreich:** 14 Jahre (Datenschutzgesetz/DSG § 4 Abs. 4 — niedrigste Grenze in der EU, Sonderfall innerhalb der von Art. 8 Abs. 1 UA 2 gestatteten Spanne).
- **Schweiz:** revDSG kennt keine feste Altersgrenze; allgemeine Geschäftsfähigkeit nach ZGB; in der Praxis vergleichbar zu 16 Jahren.

Bei DACH-übergreifenden Listen einheitlich auf **16 Jahre** abstellen (strengste Regel), AT-Sonderfall nur dokumentieren wenn Versand ausschließlich AT.

### Risikobasierter Maßstab statt einer fixen Hürde

Art. 8 Abs. 2 verlangt **„angemessene Anstrengungen unter Berücksichtigung der verfügbaren Technik"** — der Wortlaut ist explizit risikobasiert. Die EDPB-Guidelines 05/2020 zur Einwilligung machen es klarer: Für Low-Risk-Verarbeitung kann eine Altersabfrage zusammen mit einer Eltern-E-Mail-Verifikation ausreichend sein; bei höherem Risiko sind stärkere Mechanismen geboten. Eine pauschale „Geburtsjahr-Self-Declaration ist verboten"-Regel existiert in der Aufsichtspraxis (Stand Mai 2026) nicht — entscheidend ist die Risiko-Bewertung des konkreten Verarbeitungs-Kontextes.

**Was bedeutet „Risiko" hier?** Zu betrachten sind insbesondere:

- **Inhalt:** rein informativer Newsletter vs. gezieltes Marketing für Kinder vs. Gaming-/In-App-Kauf-Trigger vs. sensible Inhalte (Health, Sexualität, Sucht)
- **Datenarten:** nur E-Mail vs. zusätzliches Profiling, Verhaltens-Tracking, Standort, Art.-9-Daten
- **Zielgruppen-Ausrichtung:** Newsletter, der zufällig auch Kinder erreicht (Tech-/Mode-/Allgemein-Newsletter) vs. Newsletter, der gezielt Kinder als Zielgruppe hat (Spielzeug, Schul-Tools, Gaming-Communities)
- **Folgewirkungen:** rein redaktionelle Mail vs. Vertragsschluss-Trigger vs. monetäre Aktion

### Pattern-Hierarchie nach Risiko-Stufe

| Stufe | Mechanismus | Wann passend | Aufwand |
|---|---|---|---|
| 0 | Nur Häkchen „Ich bin 16+" | Nur, wenn Inhalt eindeutig nicht auf Kinder ausgerichtet ist und Kinder als Empfänger unwahrscheinlich sind. Bei jeder realistischen Kinder-Reichweite zu schwach. | minimal |
| 1 | Geburtsjahr-Eingabe + Eltern-Mail-Bestätigung | **Low-Risk-Verarbeitung** (rein redaktioneller Newsletter ohne Profiling, ohne monetäre Trigger, ohne sensible Inhalte). EDPB 05/2020 lässt das ausdrücklich zu. Maintainer-Empfehlung: Plausibilitäts-Check (Stufe 2) anhängen, kostet wenig zusätzlich. | gering |
| 2 | Stufe 1 + Plausibilitäts-Check (z. B. Eltern-Mail-Domain ≠ Kinder-Mail-Domain bei Freemail; Throttling pro IP/Device gegen Mehrfach-Anmeldungen) | Solide Basis für die meisten Marketing-Newsletter mit Kinder-Reichweite | gering |
| 3 | Stufe 2 + Erwachsenen-Indiz im Eltern-Confirm (z. B. Mini-Captcha „Welcher dieser Begriffe ist erwachsen-typisch?"); zusätzliche TTL-Beschränkung des Eltern-Tokens (24–48 h) | Für klar Kinder-gerichtete Marketing-Newsletter (Spielzeug, Schul-Tools), aber ohne monetäre Wirkung | mittel |
| 4 | Stufe 3 + Kreditkarten- / Mikro-Zahlungs-Verifikation (1-Cent-Test, sofortige Rückerstattung — US-COPPA-Standard) | High-Risk: Gaming-Communities mit In-App-Käufen, Streaming-Trial-Newsletter mit Cashback, Premium-Subscription-Anbahnung | hoch (geringe Kosten pro Anmeldung) |
| 5 | Stufe 4 + eID / Video-Ident / Behörden-Schnittstelle (z. B. nPA, AusweisApp, BankIdent) | Sehr hohes Risiko: Glücksspiel, Erwachsenen-naher Content mit Kinder-Schutz-Pflichten, staatsnahe Kinder-Dienste | sehr hoch |

**Maintainer-Empfehlung pro Risiko:**

| Verarbeitungs-Profil | Empfohlene Mindest-Stufe |
|---|---|
| Reiner Edu-/Informations-Newsletter, kein Profiling | Stufe 1 (EDPB-konform), Stufe 2 als Best Practice |
| Marketing-Newsletter mit Produkt-Empfehlungen an Kinder | Stufe 2 oder 3 |
| Gaming/Streaming mit monetären Triggern | Stufe 4 |
| Glücksspiel-nahes oder Erwachsenen-relevantes Umfeld | Stufe 5 |

Diese Stufen sind **kein normatives DSK-Schema**, sondern eine Maintainer-Operationalisierung des „angemessen"-Begriffs aus Art. 8 Abs. 2. Bei Aufsichts-Prüfung argumentiert man mit der Risiko-Bewertung und der gewählten Stufe — nicht mit der Stufe an sich.

### Anti-Pattern, das in jeder Stufe vermieden werden sollte

- **Kein Plausibilitäts-Check zwischen Kinder- und Eltern-Mail:** identische Freemail-Domain (`@gmail.com` für beides) lässt Eltern-Bestätigung an die Kinder-Mailbox zurücklaufen — das ist *immer* ein Problem, unabhängig von der Risiko-Stufe.
- **Geburtsjahr-Field ohne Rate-Limit/Throttling:** ein Kind probiert in 30 Sekunden alle Jahre durch und findet die akzeptierte Schwelle. Mindestens IP- oder Device-basiertes Throttling.
- **Eltern-Confirm-Token ohne TTL:** ein Token, das ewig gilt, kann Wochen später vom Kind selbst geklickt werden. 24–48 h TTL ist die Praxis-Norm.

### Maintainer-Default für Kinder-Randerfassung

Pragmatische Empfehlung für kleine und mittlere Marketing-Teams: wenn die Zielgruppe **mehrheitlich erwachsen** ist und nur am Rand Kinder erfasst werden könnten, kann der Aufwand einer Trägerschafts-Verifikation oft vermieden werden, indem Kinder explizit ausgeschlossen werden:

```html
<label>
  <input type="checkbox" required name="adult_confirm" value="1" />
  Ich bestätige, dass ich mindestens 16 Jahre alt bin.
</label>
<p style="font-size: 11px;">
  Dieser Newsletter ist nicht für Kinder unter 16 Jahren bestimmt. Wir verarbeiten
  keine Daten von Kindern unter 16 ohne nachweisbare Zustimmung der
  Erziehungsberechtigten. Wenn Sie unter 16 sind, melden Sie sich bitte nicht an.
</p>
```

Plus serverseitig: Suppression-Regel, dass alle Anmeldungen, die später als „Kind" auffällig werden (z. B. über Schul-Domains `@schule-musterstadt.de`, oder über Beschwerde-Mails der Eltern), automatisch und unwiderruflich gesperrt werden.

### Code-Pattern: Stufe-2/3-Verifikation (für Newsletter mit Kinder-Reichweite)

```ts
// Anmeldung mit Eltern-Mail + Plausibilität + Eltern-Confirm mit Erwachsenen-Quiz
type ChildSubscription = {
  childEmail: string;
  parentEmail: string;
  birthYear: number;
};

async function subscribeChild(data: ChildSubscription) {
  const age = new Date().getFullYear() - data.birthYear;
  if (age >= 16) {
    // Erwachsenen-Flow
    return regularSubscribe(data.childEmail);
  }

  // 1. Plausibilitäts-Check: nicht dieselbe Mailbox-Domäne wie Kinder-Mail
  const childDomain = data.childEmail.split('@')[1];
  const parentDomain = data.parentEmail.split('@')[1];
  if (childDomain === parentDomain && isFreemailDomain(childDomain)) {
    // Schulmail-Domain ist OK (Kind hat Schul-Account, Eltern haben dieselbe Schul-Domain idR nicht),
    // aber Gmail/Outlook/Yahoo + dieselbe Domain = Verdachts-Pattern.
    throw new Error('Eltern-Mail muss von anderer Domain sein als die Kinder-Mail.');
  }

  // 2. Eltern-DOI-Token erstellen, NICHT an Kind-Mail senden
  const parentToken = await createDOIToken({
    purpose: 'child_newsletter_parental_consent',
    childEmail: data.childEmail,
    parentEmail: data.parentEmail,
    childBirthYear: data.birthYear,
    ttlHours: 48,
  });

  // 3. Confirm-Mail an Eltern mit Verifikations-Schritt
  await sendParentalConsentMail(data.parentEmail, parentToken);

  // 4. Audit-Log: separate Aufbewahrung des Eltern-Confirms parallel zum Kinder-Eintrag
  //    (Art. 7 Abs. 1 DSGVO — Nachweis-Pflicht der Einwilligung des Trägers)
}

async function confirmParentalConsent(token: string, quizAnswer: string) {
  const t = await getToken(token);
  if (!t || t.purpose !== 'child_newsletter_parental_consent') throw new Error('invalid_token');

  // 5. Erwachsenen-Quiz: simples zusatzliches Indiz (Stufe 3, kein hartes Ident)
  if (!isAdultQuizCorrect(quizAnswer)) {
    await logFailedParentalConfirm(t);
    throw new Error('parental_confirm_failed');
  }

  // 6. Aktivierung erst jetzt
  await activateSubscription(t.childEmail, {
    parentalConsentRecord: {
      parentEmail: t.parentEmail,
      confirmedAt: new Date(),
      quizPassed: true,
      childBirthYear: t.childBirthYear,
    },
  });
}
```

### Eltern-Confirm-Mail — Pflicht-Inhalte

Die Confirm-Mail an die Eltern muss laut DSK-Hinweisen mindestens enthalten:

- **klare Identifikation des Kindes** (Name oder E-Mail-Adresse), damit Eltern überhaupt erkennen, worum es geht
- **Beschreibung des Newsletters** (Inhalte, Versand-Frequenz, Datenarten)
- **expliziter Hinweis auf das Alter des Kindes** und die Sonderschutz-Bedürftigkeit
- **klare Bestätigungs-Handlung** mit dokumentiertem Erwachsenen-Indiz (Quiz / Mikro-Payment / eID)
- **einfacher Widerrufs-Link** für die Eltern jederzeit
- **Datenschutzerklärung-Verweis** auf den Abschnitt „Kinder"

### Aufbewahrung des Eltern-Confirm-Records

Der Datensatz „Eltern haben in die Verarbeitung der Kinder-Daten eingewilligt" ist getrennt vom Kinder-Newsletter-Datensatz aufzubewahren und erfüllt Art. 7 Abs. 1 (Nachweis-Pflicht). Aufbewahrungsfrist: solange die Newsletter-Anmeldung aktiv ist plus Verjährungsfrist nach Widerruf (in DE typischerweise 3 Jahre nach Widerruf, vergleichbar zur regulären Einwilligungs-Aufbewahrung).

## Lead-Magnet-Kopplung (Art. 7 IV DSGVO)

Anti-Pattern: „PDF-Download → automatische Newsletter-Anmeldung im Hintergrund". Das ist Kopplung der Vertragserfüllung (PDF-Bereitstellung) an die Einwilligung — verboten nach Art. 7 IV.

Pattern: PDF-Download auch ohne Newsletter-Anmeldung verfügbar. Newsletter als zusätzliche, optionale Checkbox, default unchecked. Beim Anmeldevorgang ist klar, dass die Newsletter-Wahl nichts mit dem PDF zu tun hat.

```html
<form method="post" action="/leadmagnet/download">
  <input type="email" name="email" required placeholder="E-Mail für PDF-Versand" />

  <label>
    <input type="checkbox" name="newsletter" value="1" />
    Ja, ich möchte zusätzlich den Newsletter erhalten (Anmeldung erfolgt
    nur nach separater Bestätigungs-Mail).
  </label>

  <button type="submit">PDF anfordern</button>
</form>
```

Wichtig: ist `newsletter=1` gesetzt, separate DOI-Pipeline starten. PDF-Versand erfolgt unabhängig davon (Art. 6 I b — Vertragserfüllung).

## Co-Sponsor / Shared Lists

Werben mehrere Werbetreibende gemeinsam auf einer Liste, braucht jede Partei eine eigene Einwilligung — pauschale „Sie erhalten Mails von uns und unseren Partnern" ist unzureichend.

Anforderungen:

- Liste der Werbetreibenden im Anmeldeformular sichtbar machen (oder transparenter Link auf eine aktuelle Partner-Liste)
- Pro Werbetreibendem ein eigenes Häkchen, oder
- Ein Sammel-Häkchen mit ausdrücklichem Hinweis auf die Partner und transparente Liste — aber jeder Partner muss separat aus der Empfängerliste ausgetragen werden können (One-Click pro Partner, nicht Sammel-Unsubscribe)

Aufbewahrung: pro Empfänger Einwilligung pro Werbetreibendem getrennt protokollieren.

## Cross-Links

| Thema | siehe |
|---|---|
| IP-Speicherung beim DOI-Confirm — Pflichten der IP-Adresse als Datum | `dsgvo-auth-and-logging/IP-ADDRESSES.md` |
| Versand-Log und Bounce-Log Format / Scrubbing | `dsgvo-auth-and-logging/LOGGING.md` |
| Rate-Limit auf `/newsletter/subscribe` (Bot-Schutz) | `dsgvo-auth-and-logging/AUTH-TOM.md` |
| Art. 28 AVV mit Newsletter-Provider | `dsgvo-third-country-transfer/ROLES.md` |
| US-Newsletter-Provider DPF-Status + Cloud Act | `dsgvo-third-country-transfer/PROVIDERS.md` |
| Schweiz-SOI-Sonderfall + revDSG | `UWG-7.md` (CH-Sektion) + `dsgvo-third-country-transfer/CH-REVDSG.md` |
| Confirm-Mail-Tracking-Pixel (NIE) | `TRACKING-IN-MAIL.md` |
| Suppression-Hash nach Unsubscribe | `UNSUBSCRIBE-AND-RETENTION.md` |
| Confirm-Mail = Service-Mail-Klassifikation | `SERVICE-VS-MARKETING.md` |

## Quellen

- [DSGVO Art. 6, 7, 8 (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679)
- BGH I ZR 218/07 (20.05.2009 — „E-Mail-Werbung II") — einmalige unverlangte Werbe-Mail an Gewerbetreibende = Eingriff Gewerbebetrieb
- BGH I ZR 164/09 (10.02.2011 — „Telefonaktion II") — rein elektronisches DOI ungeeignet zum Nachweis Telefonwerbungs-Einwilligung
- BGH VI ZR 134/15 (15.12.2015 — „No-Reply") — Auto-Reply mit werblichem Zusatz = Eingriff Persönlichkeitsrecht; Unterlassungsanspruch
- EuGH C-673/17 (01.10.2019) — Planet49: Default-aktivierte Cookie-Boxes sind keine Einwilligung; analoge Anwendung auf Newsletter-Boxen
- [EDPB Guidelines 05/2020 on Consent](https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-052020-consent-under-regulation-2016679_en) — Bedingungen wirksamer Einwilligung
- [DSK-Orientierungshilfe Direktwerbung 2022](https://www.datenschutzkonferenz-online.de/) — aktuelle Version vor Code-Entscheidungen live prüfen

## Disclaimer

Best-Practice-Sammlung, **keine Rechtsberatung**. Bei Massen-Versand mit > 100.000 Empfängern, Kinder-/Jugend-Marketing, B2B-Cold-Outreach in regulierten Branchen (Health, Finanz), DACH-übergreifenden Listen oder Aufsichtsanfragen: Anwalt oder Datenschutzbeauftragten konsultieren.

**Stand:** Mai 2026. EDPB-Guidelines, BGH-Rechtsprechung, EuGH-Urteile zur Einwilligung quartalsweise live prüfen.
