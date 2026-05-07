---
name: dsgvo-email-marketing
description: Use whenever code touches newsletter, marketing-email, drip-campaign, re-engagement, win-back, lead-magnet, or any outbound email with promotional content — even if the user's prompt doesn't mention DSGVO, GDPR, UWG, privacy, or "personal data". Triggers on Mailchimp / @mailchimp/mailchimp_marketing, Brevo / @getbrevo/brevo / sib-api-v3-sdk (Sendinblue legacy), Klaviyo / klaviyo-api / klaviyo-php, HubSpot Marketing / @hubspot/api-client, ActiveCampaign, MailerLite / @mailerlite/mailerlite-nodejs, CleverReach, Rapidmail, GetResponse, Kit / ConvertKit, Constant Contact, Resend / Postmark / Amazon SES / SendGrid / Mailgun / Mailjet when used for marketing or bulk newsletter sends (transactional-only use is out of scope and covered by `dsgvo-auth-and-logging` for log-scrubbing). Triggers on email-template frameworks React Email / MJML / Maizzle / Handlebars when the template contains promotional content, unsubscribe footers, or tracking pixels. Triggers on endpoints/code-paths /newsletter/subscribe, /subscribe, /confirm-email, /unsubscribe, /opt-in, /opt-out, lists.addListMember, lists.members.create, contacts.createOrUpdate, subscribers.upsert, sendCampaign, createCampaign, sendNewsletter, broadcastEmail. Triggers on email-header logic List-Unsubscribe, List-Unsubscribe-Post, RFC 8058 one-click unsubscribe. Triggers on tracking pixels (1x1.gif, open-tracking image src, /open?id=, /c/<token>?u=), click-redirect URLs, UTM-tagged outbound links inside email templates. Triggers on DB-schema work involving subscribers, audience, contacts, mailing_list, newsletter_subscriptions, consent_log, email_suppression_list, double_opt_in_tokens, bounce_log, unsubscribe_log. Also triggers on phrases like "Newsletter einrichten", "Mailing rausschicken", "DOI-Flow", "Double-Opt-In implementieren", "Abmelde-Link einbauen", "Bestätigungs-Mail", "Tracking-Pixel im Newsletter", "B2B Cold-Mailing", "LinkedIn DM-Outreach automatisieren", "Win-Back-Kampagne", "Re-Engagement-Mailing", "Bewertungs-Mail nach Kauf", "Cross-Sell in Bestellbestätigung". Covers UWG § 7 (DE) inkl. § 7 II Nr. 3 + § 7 III Bestandskunden-Privileg, AT TKG 2021 § 174, CH UWG Art. 3 I o, DSGVO Art. 6 I a + Art. 7 (Einwilligung im Marketing-Kontext), Art. 7 IV (Kopplungsverbot Lead-Magnet), Art. 8 (Kinder < 16), Art. 21 II (Direktwerbungs-Widerspruch), TDDDG § 25 (vormals TTDSG, umbenannt 14.05.2024 — Tracking-Pixel-Einwilligung), BGH I ZR 218/07 vom 20.05.2009 („E-Mail-Werbung II" — einmalige unverlangte Werbe-Mail = Eingriff Gewerbebetrieb, auch B2B), BGH I ZR 164/09 vom 10.02.2011 („Telefonaktion II" — rein elektronisches DOI ungeeignet zum Nachweis Telefonwerbungs-Einwilligung), BGH VI ZR 134/15 vom 15.12.2015 („No-Reply" — Auto-Reply / Confirm-Mail mit werblichem Zusatz = Eingriff Persönlichkeitsrecht), BGH VI ZR 225/17 vom 10.07.2018 (Bewertungs-Bitte = Werbung), DSK-Orientierungshilfe Direktwerbung, EDPB Guidelines 05/2020 Consent, RFC 8058 One-Click-Unsubscribe, Gmail/Yahoo Sender Requirements Feb 2024 + Microsoft Outlook/Hotmail/Live High-Volume-Sender seit 05.05.2025 (> 5.000 Mails/Tag, SPF/DKIM/DMARC verpflichtend). Triggers ALSO on **Cold-Outreach via Plattform-DMs** — auch wenn der Prompt rein nach Tools / Services / Limits fragt: LinkedIn-DM, LinkedIn-InMail, LinkedIn Sales Navigator, Xing-Nachrichten, Instagram-DM, Twitter/X-DM, Facebook-Messenger-Outreach. Triggers on **Outreach-Automatisierungs-Tools** HeyReach, Expandi, Dripify, Waalaxy, Lemlist, Skylead, Phantombuster, La Growth Machine, Apollo.io (Sequences), Outreach.io, Salesloft, Reply.io, Woodpecker, Instantly.ai, Smartlead, MeetAlfred, Octopus CRM, LinkedHelper, Dux-Soup, Zopto. Begründung: § 7 II Nr. 3 UWG („elektronische Post") ist plattformneutral; **OLG Hamm 18 U 154/22 vom 03.05.2023** stellt direkt fest, dass Direktnachrichten in Xing/LinkedIn/Facebook/WhatsApp elektronische Post iSd § 7 II Nr. 3 UWG sind und Erstkontakt ohne ausdrückliche Einwilligung verboten ist — gilt auch B2B (BGH I ZR 218/07 „E-Mail-Werbung II": einmalige unverlangte Werbe-Mail an Gewerbetreibenden = Eingriff Gewerbebetrieb). Skill greift bei „LinkedIn-DMs an X Profile schicken", „Welches Tool für LinkedIn-Outreach", „Cold-DM-Sequenz aufsetzen", „Sales-Navigator automatisieren". Cross-Link: Drittland-Aspekt (US-Newsletter-Provider, DPF-Status, SCCs) → dsgvo-third-country-transfer; IP-Speicherung beim DOI-Confirm + Versand-Log-Format → dsgvo-auth-and-logging.
---

# DSGVO E-Mail-Marketing (UWG § 7 + Art. 6 / 7 / 21 DSGVO + TDDDG § 25)

## Kernprinzip

Drei Sätze, die für jede Marketing-Mail gelten:

1. **Werbung per E-Mail braucht in DACH grundsätzlich Double-Opt-In.** Single-Opt-In ist abmahnfähig (UWG § 7 II Nr. 3, DE). Das Bestandskunden-Privileg (UWG § 7 III) greift nur unter vier kumulativen Voraussetzungen — Detail in `UWG-7.md`. Schweiz-Sonderfall: SOI praktisch tolerierter, aber für DACH-Mischlisten unsicher.
2. **„Werbung" ist weit auszulegen** (ständige BGH-Rechtsprechung, vgl. BGH VI ZR 225/17 — Bewertungsbitte, BGH VI ZR 134/15 — Auto-Reply mit Werbezusatz): jede Form mittelbarer Absatzförderung. Cross-Sell-Banner in Bestellbestätigungen, „wir vermissen Sie"-Mails — alles zählt. Rein transaktionale Inhalte (Versandstatus, Rechnung, Sicherheitswarnung) bleiben einwilligungsfrei. Auch im B2B gilt: bereits eine einmalige unverlangte Werbe-Mail an Gewerbetreibende ist Eingriff in den Gewerbebetrieb (BGH I ZR 218/07 — „E-Mail-Werbung II").
3. **Open-Tracking-Pixel und Click-Tracking sind eigenständige Datenverarbeitung** mit eigener Rechtsgrundlage (TDDDG § 25 + Art. 6 DSGVO). Separate Einwilligung; nicht im Schwarm-Häkchen mit der Newsletter-Anmeldung mitfangen.

## Wann dieser Skill greift

**Trigger-Symptome (Code-Patterns):**

- **Imports / SDKs:** `@mailchimp/mailchimp_marketing`, `@getbrevo/brevo`, `sib-api-v3-sdk`, `klaviyo-api`, `@hubspot/api-client`, `mailerlite`, `cleverreach`, `rapidmail`, `getresponse`, `convertkit` / `kit`. Auch `resend`, `postmark`, `@aws-sdk/client-sesv2`, `@sendgrid/mail`, `mailgun`, `node-mailjet` — wenn für Marketing-Versand verwendet (Trans-Only ist OUT, siehe Skip-Liste).
- **Endpoints:** `/newsletter/subscribe`, `/subscribe`, `/confirm-email`, `/confirm-newsletter`, `/unsubscribe`, `/opt-in`, `/opt-out`, `/preferences-center`
- **API-Methoden:** `lists.addListMember`, `lists.members.create`, `contacts.createOrUpdate`, `subscribers.upsert`, `sendCampaign`, `createCampaign`, `sendNewsletter`, `broadcastEmail`
- **Mail-Header:** `List-Unsubscribe`, `List-Unsubscribe-Post: List-Unsubscribe=One-Click` (RFC 8058)
- **Tracking-Code in Mail-HTML:** `<img src=".../open?id=">`, `<img src=".../1x1.gif">`, Klick-Redirect-Domains (`/c/{token}?u=`), UTM-Parameter in Outbound-Links
- **DB-Schema:** Tabellen `subscribers`, `audience`, `contacts`, `mailing_list`, `newsletter_subscriptions`, `consent_log`, `email_suppression_list`, `double_opt_in_tokens`, `bounce_log`, `unsubscribe_log`
- **Mail-Template-Frameworks:** React Email / MJML / Maizzle / Handlebars-Templates mit Werbe-Inhalt, Unsubscribe-Footer oder Tracking-Pixel

**Skip wenn:**

- Reine **Transactional-Mails ohne Werbe-Anteil** (Order-Confirmation ohne Cross-Sell, Password-Reset, MFA-Code, Verify-Email, Versand-Status, Liefer-Avis, Rechnung, Mahnung, Vertragsinformation, Sicherheits-Benachrichtigung) — kein UWG, kein Marketing-DSGVO. Log-Scrubbing dafür wohnt in `dsgvo-auth-and-logging/LOGGING.md`.
- Interne Mails an Mitarbeiter ohne Werbe-Inhalt (Onboarding-Doku, Compliance-Erinnerungen) — § 26 BDSG, geplanter `dsgvo-employment`-Skill.
- Behördlich vorgeschriebene Pflicht-Mails (z.B. AGB-Änderung mit gesetzlicher Wirkung) — Art. 6 I c, kein UWG.
- US-Empfänger-Versand außerhalb DACH (CAN-SPAM ist eigene Rechtsordnung, nicht Scope dieses Skills).

## Decision Tree

```
1. Ist der Mail-Inhalt Werbung iSd UWG § 7?
   ├─ Reine Service-Mail (Versandstatus, Rechnung, Sicherheitswarnung, Reset)
   │   → kein UWG, weiter zu 5 (Tracking-Pixel-Check)
   │   → Logging via dsgvo-auth-and-logging/LOGGING.md
   ├─ Werbung (Cross-Sell, Bewertungsbitte, Drip, Win-Back, Cross-Promo)
   │   → weiter zu 2
   └─ Mischmail (Service-Onboarding mit Promo-Anteil)
       → klassifiziert als Marketing — siehe SERVICE-VS-MARKETING.md

2. Einwilligung dokumentiert mit Double-Opt-In?
   ├─ DOI mit IP, User-Agent, Form-URL, Zeitstempel beweisbar (siehe CONSENT-AND-DOI.md)
   │   → weiter zu 3
   ├─ Single-Opt-In oder kein Confirm-Klick
   │   → STOP: abmahnfähig in DE / AT
   │   → CH-Sonderfall: SOI tolerierbar, aber bei DACH-Mischliste unsicher (UWG-7.md)
   └─ DOI älter als 24 Monate ohne Aktivität
       → Re-Permission empfohlen (UNSUBSCRIBE-AND-RETENTION.md)

3. Greift das Bestandskunden-Privileg (UWG § 7 III, DE)?
   Vier Voraussetzungen kumulativ — Details UWG-7.md:
   (a) Adresse aus Kauf erlangt (nicht Lead-Liste)
   (b) Werbung für ähnliche Waren / DL
   (c) Klare Hinweise bei Erhebung + jeder Mail
   (d) Widerruf kostenfrei
   ├─ alle vier ✓ → vereinfachter Pfad ohne separate Einwilligung
   └─ eine Voraussetzung fehlt → DOI-Pfad notwendig

4. Ist Werbung von Service sauber getrennt?
   → SERVICE-VS-MARKETING.md
   → Mail-Templates explizit klassifizieren (transactional | marketing)
   → Marketing-Templates nur an Empfänger mit gesetzter Marketing-Einwilligung

5. Tracking-Pixel oder Click-Tracking aktiv?
   → TRACKING-IN-MAIL.md
   → Separate Einwilligung Pflicht (TDDDG § 25 + Art. 6 DSGVO, DSK-Orientierungshilfe)
   → Confirm-Mail rendert NIE Tracking-Pixel (Service-Mail-Charakter)

6. Unsubscribe-Mechanik korrekt?
   → UNSUBSCRIBE-AND-RETENTION.md
   ├─ One-Click + List-Unsubscribe-Header (RFC 8058) → ✓
   ├─ Login-Pflicht oder Pflichtformular → STOP
   └─ Suppression-Hash nach Abmeldung statt Klartext-E-Mail
```

## Code-Generation-Regel

1. **Default:** DSGVO-/UWG-konforme Variante schreiben — DOI-Token kryptographisch erzeugen, Confirm-Mail werbefrei, One-Click-Unsubscribe + List-Unsubscribe-Header gesetzt, Tracking-Pixel einwilligungs-gekoppelt, Suppression-Hash nach Unsubscribe. Kein Hinweis nötig, wenn der User nichts Schwächeres verlangt.

2. **Wenn der User explizit eine schwächere Variante will** — erkennbar an Phrasen wie:
   - „Single-Opt-In reicht uns"
   - „Confirm-Mail soll auch Promo enthalten"
   - „Pixel immer aktiv, ist doch nur Statistik"
   - „kalte B2B-Liste anmailen"
   - „Bewertungs-Mail brauchen wir nicht extra einwilligen"

   → **Zwei Code-Blöcke** generieren:

   - **Variante A: DSGVO-/UWG-konform** (Default-Implementation, lauffähig)
   - **Variante B: Wunsch-Variante** mit Tabelle der konkreten Pflichten, die verletzt werden (Norm + erwartete Konsequenz: Abmahn-Risiko UWG § 8 / Bußgeld nach Art. 83 DSGVO / DSFA-Pflicht / Aufsichtsbehörde-Beanstandung). Die Tabelle steht **vor** dem Code, nicht versteckt darunter.

3. **Drei Top-Code-Smells, die der Skill sofort fixt:**

### a) Confirm-Mail enthält Werbe-Block (BGH VI ZR 134/15 — „No-Reply")

**Vorher** (TypeScript + React Email):

```tsx
export const ConfirmEmail = ({ confirmUrl, firstName }) => (
  <Html>
    <Body>
      <Heading>Hallo {firstName}, fast geschafft!</Heading>
      <Text>Bitte bestätige deine Anmeldung:</Text>
      <Button href={confirmUrl}>Anmeldung bestätigen</Button>
      <Section>
        <Heading as="h2">Übrigens — diese Angebote nur für dich:</Heading>
        <ProductCarousel products={topProducts} />  {/* ❌ Werbung in Confirm-Mail */}
      </Section>
    </Body>
  </Html>
);
```

**Nachher:**

```tsx
export const ConfirmEmail = ({ confirmUrl, firstName }) => (
  <Html>
    <Body>
      <Heading>Hallo {firstName}, fast geschafft!</Heading>
      <Text>Bitte bestätige deine Anmeldung:</Text>
      <Button href={confirmUrl}>Anmeldung bestätigen</Button>
      <Text>
        Du hast den Newsletter angefordert. Wenn nicht, ignoriere diese Mail —
        ohne Bestätigung wird keine Anmeldung wirksam.
      </Text>
      <Text>
        Datenschutz: <Link href={privacyUrl}>Datenschutzerklärung</Link> ·
        Widerruf jederzeit per <Link href={unsubscribeUrl}>Abmelde-Link</Link>.
      </Text>
    </Body>
  </Html>
);
```

### b) Order-Confirmation hat Cross-Sell-Block (BGH VI ZR 134/15 / VI ZR 225/17 — Linie)

**Vorher** (Python + Jinja2):

```jinja
<h1>Vielen Dank für Ihre Bestellung #{{ order.id }}</h1>
<p>Wir haben Ihre Bestellung erhalten und verarbeiten sie...</p>

{# Bestelldaten ... #}

<h2>Diese Produkte könnten Sie auch interessieren:</h2>  {# ❌ Werbung in Trans-Mail #}
{% for p in recommended_products %}
  <a href="{{ p.url }}?utm_source=order_confirmation">...</a>
{% endfor %}
```

**Nachher:** Cross-Sell-Block entfernen. Wenn der Empfänger Marketing-Einwilligung hat, separate Marketing-Mail im Drip ausliefern; Trans-Mail bleibt rein.

### c) Open-Pixel ohne Einwilligung (TDDDG § 25 + DSK 2021)

**Vorher** (PHP + Twig):

```twig
{# Always-on Open-Tracking #}
<img src="https://provider/open?id={{ recipient.id }}" width="1" height="1" alt="" />
```

**Nachher:**

```twig
{# Pixel nur bei expliziter Tracking-Einwilligung #}
{% if recipient.consent_tracking %}
  <img src="https://provider/open?id={{ recipient.id }}" width="1" height="1" alt="" />
{% endif %}
```

Dasselbe Pattern für Click-Redirects: `if recipient.consent_tracking → URL via /c/{token}; else → direkter Link`.

**Refusal-Klausel:** Bei B2B-Cold-Mailing-Anfragen für DACH-Empfänger NICHT die kalt-versendende Variante generieren — UWG § 7 II Nr. 3 verbietet das auch im B2B (entgegen häufiger Annahme). Hinweis stattdessen: ausdrückliche Rücksprache mit Anwalt oder DSB; alternativ DOI-basierter Lead-Funnel statt Cold-Mailing.

## In diesem Skill

| Sub-File | Wann laden |
|---|---|
| `CONSENT-AND-DOI.md` | DOI-Flow, Confirm-Mail-Inhalt, Re-Subscribe, Lead-Magnet-Kopplung, Kinder-Newsletter |
| `UWG-7.md` | UWG § 7 (DE), TKG § 174 (AT), UWG Art. 3 I o (CH), Cold-B2B, LinkedIn-DM |
| `TRACKING-IN-MAIL.md` | Open-Pixel, Click-Redirect, externe Bilder, Personalisierungs-Tracking |
| `UNSUBSCRIBE-AND-RETENTION.md` | One-Click, RFC 8058, Suppression-Hash, Bounce, Aufbewahrung, Art. 21 II |
| `SERVICE-VS-MARKETING.md` | BGH-Linie, Trans-Mail-mit-Werbung, Onboarding-Drip-Mischmails |

## Cross-Links

| Thema | siehe |
|---|---|
| US-Newsletter-Provider Drittland-Aspekt (Mailchimp / Klaviyo / HubSpot / ActiveCampaign DPF + Cloud Act) | `dsgvo-third-country-transfer/PROVIDERS.md` |
| TDDDG § 25 als Norm (generelle Cookie-/Tracking-Pflichten außerhalb von Mail) | `dsgvo-third-country-transfer/EPRIVACY.md` |
| Cloud Act / Konzernzugriff auf Newsletter-Daten US-Provider | `dsgvo-third-country-transfer/CLOUD-ACT.md` |
| Schweiz revDSG für CH-Empfänger | `dsgvo-third-country-transfer/CH-REVDSG.md` |
| Art. 6 / 7 generell, Art. 28 AVV mit Newsletter-Provider | `dsgvo-third-country-transfer/ROLES.md` |
| DPIA-Pflicht (Art. 35) bei Profiling großer Reichweite | `dsgvo-third-country-transfer/DPIA.md` |
| IP-Speicherung beim DOI-Confirm — Pflichten der IP als Datum | `dsgvo-auth-and-logging/IP-ADDRESSES.md` |
| Versand-Log / Bounce-Log Scrubbing (PII in Logs) | `dsgvo-auth-and-logging/LOGGING.md` |
| Rate-Limit auf Signup-Endpunkt (Bot-Schutz) | `dsgvo-auth-and-logging/AUTH-TOM.md` |
| Account-Löschung mit aktivem Newsletter-Abo | `dsgvo-subject-rights` (geplant) |
| § 26 BDSG bei Beschäftigten-Newsletter | `dsgvo-employment` (geplant) |

## Häufige Fallen

- **Confirm-Mail / Auto-Reply mit „Übrigens, hier unsere aktuellen Angebote"** — BGH VI ZR 134/15 („No-Reply"): Werbung in automatisch generierter Bestätigungs-Mail ist rechtswidriger Eingriff in das allgemeine Persönlichkeitsrecht; Unterlassungsanspruch. Fix: Confirm-Mail nur Bestätigungs-Bitte + Datenschutz-Hinweis + Widerruf.
- **Order-Confirmation mit Footer-Banner „Diese Produkte könnten Sie auch interessieren"** — BGH VI ZR 134/15 + VI ZR 225/17 (Bewertungsbitte = Werbung) + ständige Rechtsprechung: Cross-Sell-Hinweis macht aus Trans-Mail eine Werbe-Mail; ohne Marketing-Einwilligung abmahnfähig. Fix: Cross-Sell aus Trans-Mail rausnehmen oder Trans-Mail explizit als Marketing klassifizieren.
- **Mailchimp-Audience mit Open- und Click-Tracking by default** — Mailchimp aktiviert Tracking automatisch; ohne Einwilligung kollidiert das mit TDDDG § 25 + DSK-Position 2021. Fix: in der Audience-Einstellung Tracking deaktivieren oder pro Kontakt einwilligungs-gesteuert schalten.
- **Cold-B2B-Akquise „LinkedIn-DM ist ja keine E-Mail"** — falsch; § 7 II Nr. 3 UWG erfasst „elektronische Post" weit; LinkedIn-/XING-Direktnachrichten sind erfasst. Fix: nur an Empfänger schreiben, die ausdrücklich zugestimmt haben (DOI-Funnel) oder das Bestandskunden-Privileg klar greift.
- **Newsletter-Pflichtfeld „Geburtsdatum"** beim Anmeldeformular — Art. 5 I c Datenminimierung: nur E-Mail darf Pflichtfeld sein. Fix: alle anderen Felder optional, Default unmarkiert.
- **Lead-Magnet ohne separate Newsletter-Checkbox** — „PDF-Download → Newsletter automatisch" verstößt gegen Art. 7 IV DSGVO (Kopplungsverbot). Fix: PDF auch ohne Newsletter-Anmeldung; Newsletter als zusätzliche Opt-In-Checkbox, default unchecked.
- **Re-Subscribe alter Listen aus 2018 nach DSGVO-Inkrafttreten** — der ursprüngliche Confirm-Datensatz ist nach Widerruf nicht reaktivierbar. Fix: vor Re-Anschreiben neuen DOI durchführen; ohne erneute Bestätigung keine Werbe-Mails.
- **„CAN-SPAM reicht für Europa"** — falsche Annahme. UWG + DSGVO + ePrivacy-Bestimmungen gelten zusätzlich; CAN-SPAM regelt nur den US-Markt. Fix: für EU-/DACH-Empfänger DOI-Pfad und Unsubscribe-Mechanik nach EU-Recht aufsetzen.
- **Bewertungsbitte 14 Tage nach Kauf ohne separate Marketing-Einwilligung** — BGH VI ZR 225/17: Bewertungsbitte = Werbung iSd UWG § 7. Fix: nur an Empfänger schicken, die der Marketing-Verarbeitung zugestimmt haben.
- **Single-Opt-In für CH-Empfänger generell tolerieren** — CH UWG Art. 3 I o + revDSG-Transparenzpflichten greifen weiter; bei DACH-Mischlisten ist DOI für alle die sichere Linie. Fix: einheitlich DOI fahren.
- **One-Click-Unsubscribe + List-Unsubscribe-Header fehlen** — Gmail/Yahoo Sender-Anforderungen Februar 2024 verlangen für Bulk-Sender (≥ 5.000 Mails/Tag) RFC 8058 + DKIM/SPF/DMARC. Ohne diese landen Mails dauerhaft im Spam. Fix: List-Unsubscribe-Header korrekt setzen, One-Click-Endpoint implementieren.
- **Klartext-E-Mail nach Unsubscribe in der DB lassen** — Art. 5 I c + e: nach Widerruf nur Suppression-Hash speichern (Schutzfunktion), Klartext löschen. Fix: **HMAC-SHA-256 mit serverseitigem Pepper** (nicht plain SHA-256 — E-Mail-Adressen sind low-entropy und über Rainbow-Tables trivial reversibel) in `email_suppression_list`, Klartext entfernen.

## Quellen

- [DSGVO Volltext (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679) — Art. 6 / 7 / 8 / 21
- [UWG § 7 (gesetze-im-internet.de)](https://www.gesetze-im-internet.de/uwg_2004/__7.html) — DE
- [UWG § 8 (gesetze-im-internet.de)](https://www.gesetze-im-internet.de/uwg_2004/__8.html) — Unterlassungsanspruch
- [TKG Österreich 2021 § 174 (RIS)](https://www.ris.bka.gv.at/) — Direktwerbung per E-Mail
- [UWG Schweiz Art. 3 (admin.ch)](https://www.fedlex.admin.ch/eli/cc/1988/223_223_223) — lit. o Massen-E-Mail-Werbung
- [TDDDG (gesetze-im-internet.de)](https://www.gesetze-im-internet.de/tddg/) — § 25 (vormals TTDSG, umbenannt 14.05.2024)
- BGH I ZR 218/07 (20.05.2009 — „E-Mail-Werbung II") — einmalige unverlangte Werbe-Mail an Gewerbetreibende = Eingriff Gewerbebetrieb (B2B nicht frei)
- BGH I ZR 164/09 (10.02.2011 — „Telefonaktion II") — rein elektronisches DOI ungeeignet zum Nachweis Telefonwerbungs-Einwilligung
- BGH VI ZR 134/15 (15.12.2015 — „No-Reply") — automatische Bestätigungs-Mail mit werblichem Zusatz = Eingriff Persönlichkeitsrecht; Unterlassungsanspruch
- BGH VI ZR 225/17 (10.07.2018) — Bewertungs-/Zufriedenheitsbitte = Werbung iSd § 7 UWG
- OLG Hamm 18 U 154/22 (03.05.2023 — Beschluss) — Direktnachrichten in Xing/LinkedIn/Facebook/WhatsApp = elektronische Post iSd § 7 II Nr. 3 UWG; Erstkontakt ohne ausdrückliche Einwilligung verboten
- [DSK-Orientierungshilfe Direktwerbung 2022](https://www.datenschutzkonferenz-online.de/) — aktuelle Version vor jeder Code-Generierung live prüfen
- [EDPB Guidelines 05/2020 on Consent](https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-052020-consent-under-regulation-2016679_en)
- [RFC 8058 — One-Click List-Unsubscribe](https://www.rfc-editor.org/rfc/rfc8058)
- [RFC 2369 — List-Unsubscribe-Header](https://www.rfc-editor.org/rfc/rfc2369)
- [Gmail Sender Requirements (postmaster.google.com)](https://support.google.com/mail/answer/81126) — Februar 2024 Update; Stand laufend prüfen
- [Microsoft Outlook High-Volume-Sender Requirements](https://techcommunity.microsoft.com/blog/microsoftdefenderforoffice365blog/strengthening-email-ecosystem-outlook%E2%80%99s-new-requirements-for-high%E2%80%90volume-senders/4399730) — seit 05.05.2025: > 5.000 Mails/Tag, SPF + DKIM + DMARC verpflichtend; sofortige 5xx-Rejects bei fehlendem DMARC

## Disclaimer

Best-Practice-Sammlung, **keine Rechtsberatung**. Bei Massen-Versand mit > 100.000 Empfängern, Health-/Finanz-/KRITIS-Newsletter, Beschäftigten-Versand (§ 26 BDSG), Cross-Border-EU-Versand außerhalb DACH oder Aufsichts-/Abmahn-Anfragen: Anwalt oder Datenschutzbeauftragten konsultieren.

**Stand:** Mai 2026. UWG-/TKG-/CH-UWG-Änderungen, DSK-Beschlüsse, Sender-Anforderungen Gmail / Yahoo / Microsoft, neue BGH-Rechtsprechung und DPF-Status quartalsweise live prüfen — Verweis-URLs in Sektion „Quellen".
