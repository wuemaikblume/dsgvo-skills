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

Wenn die Zielgruppe potenziell Kinder unter 16 Jahren erreicht (Spielzeug, Schule, Gaming, Comics, Kinder-Streaming), greift Art. 8 DSGVO: die Einwilligung von Kindern unter 16 ist nur mit Zustimmung des Inhabers der elterlichen Verantwortung wirksam.

Code-Pattern:

```html
<form method="post" action="/newsletter/subscribe">
  <label>Geburtsjahr <input type="number" name="birthYear" min="1900" max="2026" required /></label>
  <!-- bei Eingabe < 16 Jahre via Client-Side: alternativen Eltern-Mail-Pfad anzeigen -->
  <label>E-Mail (Empfänger) <input type="email" name="email" required /></label>
  <label hidden id="parent-email-block">
    Eltern-/Erziehungs-E-Mail <input type="email" name="parentEmail" />
  </label>
  ...
</form>
```

Server-Pattern: ist `birthYear` so, dass das Alter < 16 wäre, geht der DOI-Confirm-Link an die Eltern-E-Mail, nicht an die Kinder-E-Mail. Der Kindern-Newsletter wird erst nach Bestätigung durch die Eltern aktiviert. Aufbewahrung des elterlichen Confirm-Datensatzes parallel zur Kinder-Anmeldung.

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
