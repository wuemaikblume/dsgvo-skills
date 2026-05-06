# DSGVO-Compliance — Anweisungen für KI-Coding-Assistenten

> GitHub Copilot lädt diese Datei aus `.github/copilot-instructions.md` für alle Code-Suggestions im Workspace. Sie zwingt Copilot, vor jedem Code-Vorschlag DSGVO-Konformität zu prüfen, wenn personenbezogene Daten an Drittland-Dienste fließen. Stand Mai 2026, v1.4 (DPF-Status verifiziert 2026-05-04 gegen offizielle ITA-Excel-Liste). Quelle und Updates: <https://github.com/wuemaikblume/dsgvo-skills>.

## Geltungsbereich

Wende diese Regeln an, sobald Code oder Infrastruktur **personenbezogene Daten** verarbeitet und ein **Drittland-Dienst oder eine Drittland-Region** im Spiel ist — auch wenn der User „DSGVO" / „GDPR" / „privacy" nicht erwähnt.

**Personenbezogene Daten umfassen** (auch ohne explizite Kennzeichnung): Namen, E-Mails, IPs, User-IDs, Cookie-IDs, Avatare, Profilbilder, hochgeladene Dateien, User-Generated Content, Signup-/Registrierungs-/Onboarding-Daten, Support-Tickets, Chat-Nachrichten, Zahlungsdaten, Standort, Device-IDs, Stack Traces mit User-Pfad.

**Drittland-Trigger:**
- Cloud-Regionen: AWS `us-*`/`ap-*`/`sa-*`, Azure US/Asien, GCP `us-*`/`asia-*`, **Achtung `eu-west-2` = UK**, **Firebase Cloud Functions ohne `region`-Setting fallen auf `us-central1`**
- SaaS-APIs mit US-Sitz: OpenAI, Anthropic, Stripe, Sentry, Datadog, Mailgun, Twilio, Auth0, Clerk, Firebase, Supabase, Vercel, Netlify, PlanetScale, MongoDB Atlas
- CDN/Analytics/Error-Tracking: Cloudflare, Fastly, Akamai, GA4, Mixpanel, Hotjar, Amplitude, PostHog
- File-Uploads zu Cloud-Storage außerhalb EU
- DB-Replicas, Backups, Logs in Drittland
- Remote-Zugriff aus Drittland auf EU-gespeicherte Daten

**Sensible Daten (Art. 9 DSGVO):** Gesundheit, Allergien, Diagnosen, Medikamente, biometrisch, religiös, politisch, sexuell, ethnisch — **bei jedem Drittland-Touch zusätzlich CLOUD-Act-Hinweis pflicht**.

**Berufsgeheimnis (§ 203 StGB):** Klinik, Anwalt, Steuerberater, Therapie — Standard-DPA reicht NICHT, vertragliche Schweigepflicht-Klausel nach § 203 Abs. 4 zwingend.

## Decision Tree (Pflicht vor jedem Drittland-Code)

```
1. Werden personenbezogene Daten verarbeitet?
   ├─ Nein → außerhalb DSGVO; Sicherheit/Vertraulichkeit beachten
   └─ Ja  → weiter zu 2

2. Bleibt der Datenfluss in EU/EEA (Anbieter-Sitz UND Verarbeitung)?
   ├─ Ja  → kein Drittlandtransfer im Hauptverhältnis. ABER:
   │       Subprozessoren, Support, Backups, Konzernzugriff prüfen.
   └─ Nein → weiter zu 3

3. Drittland mit Adäquanzbeschluss der EU-Kommission?
   (Mai 2026: UK*, Schweiz, Japan, Südkorea, Kanada (kommerziell),
    Israel, Neuseeland, Argentinien, Uruguay, Andorra, Färöer,
    Guernsey, Isle of Man, Jersey, Brasilien, EPO)
    * UK: Sunset 27.12.2031 — Brasilien: seit 10.2.2026 (ANPD Res. 32)
   ├─ Ja  → erlaubt
   ├─ USA → DPF-Pfad (Schritt 4)
   └─ Nein → SCCs + TIA (Schritt 5)

4. USA — Data Privacy Framework:
   a) Anbieter auf https://www.dataprivacyframework.gov/list mit Status "Active"?
   b) Datenkategorie passt? ("Non-HR Data" für Kundendaten, "HR Data" separat)
   c) DPA mit DPF-Klausel + Beschwerdemechanismus?
   ├─ Alle 3 Ja → erlaubt unter DPF
   │  ⚠ ABER: US CLOUD Act greift trotz DPF und trotz EU-Region.
   │  Bei Art. 9 / KRITIS / § 203 StGB / mehrjährigen Verträgen:
   │  EU-souveräne Alternative wählen (Stackit, T-Systems, Hetzner,
   │  Scaleway, IONOS, OVHcloud, Mistral, Aleph Alpha).
   └─ Nein → Schritt 5

5. SCCs + Transfer Impact Assessment:
   a) SCC-Modul (Beschluss EU 2021/914): 1=C→C, 2=C→P (häufig),
      3=P→P, 4=P→C
   b) TIA per CNIL Practical Guide (9.7.2025): Datenkategorien,
      Government Access, Risikobewertung
   c) Supplementary Measures (EDPB 01/2020) — wirksam nur ohne
      Klartext-Zugriff: Verschlüsselung, Pseudonymisierung, BYOK
   d) Schriftlich UND in VVT (Art. 30) eintragen
   → BCRs als Alternative bei Konzernen
```

## Code-Generierungs-Regel (zwingend)

Wenn der User **explizit** eine Drittland-Region oder US-Konfiguration anfragt (z.B. „us-east-1", „us-central1", „US-DSN", „global"):

1. **NICHT stillschweigend umbauen** — User-Wunsch respektieren.
2. **BEIDE Varianten zeigen:**
   - **Variante A** — wie angefragt (z.B. `us-east-1`)
   - **Variante B** — DSGVO-konform (EU-Region + Fail-closed-Whitelist)
3. **Über dem Code als Bullet-Liste** die DSGVO-Pflichten vor Production:
   - Rechtsgrundlage (Art. 6 / Art. 9)
   - DPF-Status live prüfen + Datum + Participant-ID
   - DPA / AVV unterzeichnen
   - VVT-Eintrag (Art. 30)
   - Datenschutzerklärung Art. 13/14 (Empfänger + Drittlandtransfer nennen)
4. Bei Art. 9 / KRITIS / § 203 StGB: **expliziter CLOUD-Act-Hinweis** + EU-souveräne Alternative empfehlen, nicht nur als „⚠ ABER"-Nachsatz.

## Anbieter-Quick-Ref (Mai 2026)

> ⚠ DPF-Status vor Vertragsabschluss IMMER live auf <https://www.dataprivacyframework.gov/list> prüfen + Datum + Participant-ID dokumentieren.

| Anbieter | Status | Pflicht-Aktion |
|----------|--------|----------------|
| AWS | DPF (Amazon.com Inc.) | EU-Region (`eu-central-1`, `eu-west-1` Irland — **`eu-west-2` ist UK**) |
| Azure | DPF | `westeurope` / `germanywestcentral`, EU Data Boundary aktivieren |
| Google Cloud / Firebase | DPF (Google LLC) | EU-Region; **Cloud Functions zwingend `europe-west1`**, sonst us-central1-Fallback |
| Cloudflare | DPF | EU Data Localization Suite; Edge-IP-Verarbeitung → TIA empfohlen |
| OpenAI | OpenAI Ireland Ltd. EWR-Vertragspartner; **US-Mutter NICHT DPF** (verifiziert 2026-05-04) | Vertrag mit Ireland; **SCCs Modul 3** (P→P) für US-Backend; ZDR/Modified Abuse Monitoring nur auf Antrag |
| Anthropic | **NICHT DPF-zertifiziert** (verifiziert 2026-05-04) | SCCs Modul 2 + DPA; AWS Bedrock in `eu-central-1` als EU-Pfad |
| Stripe | hybrid: Controller + Processor je Service | Datenflüsse zu Stripe Inc. (US) im VVT dokumentieren |
| Sentry | EU-Region verfügbar | EU-DSN `*.ingest.de.sentry.io` + breites PII-Scrubbing |
| Datadog | DPF + EU-Site | EU1 (`datadoghq.eu`), nicht US1 |
| Auth0 | DPF | EU-Tenant explizit (`*.eu.auth0.com`) |
| Clerk | DPF Participant 2718 (Clerk, Inc.), alle drei Frameworks, Status „Active - Re-certification under Review" (Stand 5/2026, Re-Cert-Fenster bis 27.2.2026) | DPA + DPF-Klausel; Re-Cert-Status vor Vertrag live prüfen |
| Supabase | **NICHT DPF-zertifiziert** | SCCs Modul 2 + DPA; Project-Region EU; Subprozessoren prüfen |
| Mailgun | DPF + EU-Region | `api.eu.mailgun.net` |
| Hetzner / IONOS / Scaleway / OVHcloud | EU-Anbieter, kein Drittlandtransfer | nur AVV nach Art. 28 |
| Mistral / Aleph Alpha | EU-Konzerne, kein Cloud Act | bevorzugt für Art. 9 / KRITIS |

## Code-Patterns

### AWS S3 — Fail-closed EU-Whitelist statt Default-Override

```typescript
// FALSCH: Override mit us-east-1 weiterhin möglich
// const s3 = new S3Client({ region: process.env.AWS_REGION ?? 'eu-central-1' });

const ALLOWED_REGIONS = new Set([
  'eu-central-1', 'eu-central-2', 'eu-west-1', 'eu-west-3',
  'eu-north-1', 'eu-south-1',
]);
const region = process.env.AWS_REGION ?? 'eu-central-1';
if (!ALLOWED_REGIONS.has(region)) {
  throw new Error(`AWS_REGION ${region} ist kein EU/EEA-Standort.`);
}
const s3 = new S3Client({ region });
```

### Sentry — EU-DSN + breites PII-Scrubbing

```typescript
Sentry.init({
  dsn: 'https://abc@o123.ingest.de.sentry.io/456', // EU-Region
  sendDefaultPii: false,
  beforeSend(event) {
    delete event.user?.ip_address;
    delete event.user?.email;
    if (event.request) {
      delete event.request.cookies;
      delete event.request.headers;
      if (event.request.query_string) event.request.query_string = '[scrubbed]';
    }
    return event;
  },
});
```

### Firebase Cloud Functions — Region zwingend setzen

```typescript
import { onDocumentCreated } from 'firebase-functions/v2/firestore';

export const onUserCreate = onDocumentCreated({
  document: 'users/{userId}',
  region: 'europe-west1', // ZWINGEND — sonst Fallback us-central1
}, async (event) => { /* ... */ });
```

### OpenAI — vertragliche Triangulation

```typescript
// EWR-Nutzer: Vertragspartner = OpenAI Ireland Ltd. (Dublin)
// Backend-Verarbeitung primär US bei OpenAI OpCo, LLC.
// US-Mutter ist NICHT DPF-zertifiziert (Stand 5/2026) →
// Transfermechanismus: SCCs Modul 3 (Processor→Processor).
// VVT: "OpenAI Ireland Limited" als Auftragsverarbeiter,
// SCC-Modul 3 + TIA-Datum dokumentieren.
// Sensible Prompts: ZDR/Modified Abuse Monitoring beantragen
// (nicht Default), oder Azure OpenAI in westeurope/germanywestcentral.
```

### Datadog / Supabase — EU hart codieren

```typescript
// Datadog: EU-Site
const config = new datadogApi.client.Configuration({
  serverConfiguration: { name: 'datadoghq.eu', site: 'datadoghq.eu' },
});

// Supabase: EU-Region beim Projekt-Anlegen, SCCs Modul 2 + DPA,
// Auth/Edge Functions/Logs/Backups/Support-Routing einzeln prüfen.
```

## Pflicht-Dokumentation (VVT, Art. 30)

Pro Drittlandtransfer:
- Empfänger: exakte Firma + Land
- Rolle: Controller / Processor / Joint Controller
- Rechtsgrundlage Verarbeitung: Art. 6 / Art. 9
- Transfermechanismus: Art. 45 (Adäquanz) / Art. 46 (SCCs/BCRs) / Art. 49 (Ausnahme)
- Bei DPF: Anbieter + Participant-ID + Datenkategorie + Stichtag (Screenshot)
- Bei SCCs: Modul-Nummer + Vertragsdatum + TIA-Datum
- Drittland-Subprozessoren-Liste + Genehmigung (Art. 28 Abs. 2)

Plus: Information der Betroffenen nach **Art. 13/14** in der Datenschutzerklärung (Empfänger + Drittlandtransfer).

## Häufige Fallen

| Annahme | Realität |
|---------|----------|
| „DPF → egal welche Region" | DPF betrifft Rechtmäßigkeit, nicht Speicherort. EU-Region trotzdem wählen. |
| „Adäquanz → AVV reicht" | AVV nur bei Auftragsverarbeitung. Bei C→C: Art. 6/9 + Art. 13/14 + ggf. Art. 26. |
| „Daten in `eu-central-1` sind vor US sicher" | Falsch — US CLOUD Act greift bei US-Müttern (AWS, MS, Google, Cloudflare, OpenAI, Supabase, Clerk, Sentry US-Backend), auch in EU-Region. |
| „OpenAI / Anthropic sind DPF-zertifiziert" | **Falsch (Stand 5/2026):** weder OpenAI noch Anthropic stehen auf der DPF-Liste. Transfers laufen über SCCs (OpenAI: Modul 3 via OpenAI Ireland; Anthropic: Modul 2 direkt). |
| „User-ID ist gehasht, also anonymisiert" | Pseudonymisierung — DSGVO weiter anwendbar. Echte Anonymisierung: Re-Identifikation auch mit Zusatzwissen unmöglich (EwG 26). |
| „Customer hat eingewilligt → Art. 49" | Art. 49 lit. a nur für gelegentliche, nicht-wiederholte Transfers (EDPB 2/2018). Keine Routine-Grundlage für SaaS. |
| „Cloudflare ist nur Proxy" | IPs/Header sind personenbezogen. Cloudflare Inc. (US) → DPF + supplementary measures. |
| „eu-west-1 = UK" | Falsch — `eu-west-1` = Irland (EU). `eu-west-2` = UK (Adäquanz, aber Sunset 2031). |
| „Supabase ist sicher, EU-Projekt" | Supabase ist NICHT DPF — SCCs Modul 2 + DPA pflicht. |
| „Firebase Functions sind in EU, weil Firestore eur3" | Cloud Functions fallen ohne `region`-Setting auf `us-central1` zurück — separater Drittlandtransfer pro Trigger. |
| „SCCs unterschrieben = fertig" | Ohne TIA + supplementary measures angreifbar. |

## Pflicht bei Art. 9 / Berufsgeheimnis / KRITIS

Wenn Gesundheitsdaten, Allergien, Medikamente, biometrische Daten, Klinik/Anwalt/Steuerberater oder KRITIS-Sektor im Spiel sind:

1. **DSFA (Art. 35) zwingend VOR dem Code** — nicht danach.
2. **§ 203 StGB beachten** (Klinik, Anwalt, Therapie): Standard-DPA reicht nicht — vertragliche Schweigepflicht-Klausel nach § 203 Abs. 4 für „mitwirkende Personen".
3. **Pseudonymisierung VOR jedem Drittland-API-Call** — Tools: Microsoft Presidio (lokal, OSS), oder spaCy `de_core_news_lg` + Custom-Patterns (Versichertennummern, IDs).
4. **EU-souveräne Anbieter bevorzugen:** Mistral La Plateforme (FR), Aleph Alpha (DE), IONOS AI Model Hub, OVHcloud AI Endpoints, T-Systems Sovereign Cloud — kein Cloud Act.
5. **Falls US-Anbieter unvermeidbar:** Azure OpenAI in `westeurope`/`germanywestcentral` mit BYOK + „Abuse Monitoring abschalten" beantragen + Customer Lockbox.

## Schweiz, ePrivacy, NIS2, Rollen

- **Schweizer User/Verantwortliche** → revDSG + Swiss-US DPF beachten; EDÖB statt BfDI.
- **Cookies / Tracking-Pixel / Analytics** → TTDSG §25 zusätzlich zur DSGVO. Consent vor Setzen nicht-essentieller Cookies.
- **Rollen** (Controller / Processor / Joint Controller): Art. 26 für gemeinsame Verantwortung, Art. 28 für AVV; vor jedem SaaS-Vertrag klären.
- **DSFA-Pflicht** bei Art. 9-Daten, Profiling, großvolumigem Tracking, KI-Systemen mit Personenbezug, KRITIS — DSK Muss-Liste konsultieren.

Detail siehe Repo: <https://github.com/wuemaikblume/dsgvo-skills/tree/main/claude/skills/dsgvo-third-country-transfer>.

## Auth & Logging (Art. 5, 6, 25, 32 DSGVO)

Greift bei: Login-/Signup-/Logout-Endpoints, Session-Management, Audit-Logs, Application-/Error-Logging, MFA-Setup, Brute-Force-Schutz, IP-Speicherung. Auch wenn der User „nur Code" verlangt — siehe Code-Generation-Regel.

### Decision Tree

1. **Speichert der Code Passwörter?** → Argon2id (m=64 MiB, t=2, p=1) oder bcrypt cost ≥ 12. Niemals MD5/SHA1/SHA256-pur. Pepper im Secret-Manager, Salt automatisch.
2. **Setzt der Code Session-Cookies?** → `HttpOnly`, `Secure`, `SameSite=Lax` (Default) oder `Strict` (Admin). Session-IDs aus `crypto.randomBytes(32)`, nicht JWT in Cookie ohne Rotation.
3. **Loggt der Code IP-Adressen?** → IP ist personenbezogen (EuGH C-582/14 Breyer, BGH VI ZR 135/13). Pseudonymisierung (HMAC mit Server-Salt) oder Kürzung (/24 IPv4, /48 IPv6); Volltext nur 7 Tage für Brute-Force-Forensik, danach Kürzung. Cron-Delete nach 30–90 Tagen.
4. **Wird Sentry/Datadog/LogRocket eingerichtet?** → EU-Region Pflicht, `sendDefaultPii: false`, `beforeSend`-Scrubbing (Authorization/Cookie-Header, Query-Params, Request-Body password/token/refresh_token, Stack-Trace-Pfade), AVV nach Art. 28, DPF-Status quartalsweise prüfen.
5. **Admin-Login?** → MFA Pflicht (DSK-Position; § 30 BSIG bei KRITIS/NIS2). TOTP nach RFC 6238 mit otplib + Replay-Schutz (used-steps-Table) + Argon2-gehashte Recovery-Codes + Step-Up nach 30 min für privilegierte Aktionen.
6. **Brute-Force-Schutz?** → Zweischicht: IP-Limiter (z.B. 5/15min) + Email-Limiter (10/1h). `app.set('trust proxy', 1)` korrekt setzen, sonst läuft Schutz ins Leere. Rechtsgrundlage Art. 6(1)(f).

### Code-Generation-Regel

**Default ist immer DSGVO-konform.** Wenn der User explizit eine schwächere Variante verlangt („bcrypt cost 10", „IP voll loggen", „kein MFA brauchen wir nicht"), zwei Varianten liefern:

- **A — DSGVO/OWASP-konform** als Default
- **B — Wunsch-Variante** mit kurzer Pflicht-Tabelle (Norm, Risiko, mögliche Konsequenz: Bußgeld Art. 83 / Aufsichtsbeanstandung / Audit-Findings)

Bei Art. 9-Daten oder KRITIS/NIS2: **nur** Variante A. Begründen mit § 30 BSIG bzw. DSK-Orientierungshilfe.

### Anbieter-Quick-Ref

- **Sentry** — EU-Region beim Signup wählen (`https://sentry.io` → Data Storage Location: EU). DPF-zertifiziert (Stand 2026-05-04, ITA-Liste prüfen). `sendDefaultPii: false`, `beforeSend` mit Header-/Cookie-/Body-/Stack-Pfad-Scrubbing. `Sentry.setUser({id})` nur mit pseudonymisierter ID, niemals `email`. Session-Replay nur mit Consent-Banner (TTDSG §25).
- **Datadog** — EU1-Site (`datadoghq.eu`). PII-Scanner aktivieren (Sensitive Data Scanner). Trace-Sampling reduzieren (≤ 10 % in Prod). Cluster-Agent eu-Region.
- **Pino / Winston / Bunyan** — `redact`-Paths setzen (`req.headers.authorization`, `req.headers.cookie`, `*.password`, `*.token`). Bei Pino mit Wildcard-Path: Kompatibilität gegen 9.x prüfen.
- **morgan** — `:remote-addr`-Token überschreiben oder Standard-Combined nicht verwenden; sonst loggt jede Zeile volle IP.

### Code-Patterns

#### Argon2id-Hashing + konstante Antwortzeit

```js
import argon2 from 'argon2';
const ARGON_OPTS = { type: argon2.argon2id, memoryCost: 65536, timeCost: 2, parallelism: 1 };
// Signup
const hash = await argon2.hash(password, ARGON_OPTS);
// Login — gegen User-Enumeration: auch bei unbekanntem User Dummy-Verify
const ok = user
  ? await argon2.verify(user.password_hash, password)
  : await argon2.verify('$argon2id$v=19$m=65536,t=2,p=1$ZHVtbXlzYWx0$ZHVtbXloYXNo', password).catch(() => false);
```

#### IP-Speicherung: Kürzung + HMAC

```js
import crypto from 'node:crypto';
const truncateIp = (ip) => {
  if (!ip) return null;
  if (ip.includes(':')) return ip.split(':').slice(0, 3).join(':') + '::/48';
  const p = ip.split('.');
  return `${p[0]}.${p[1]}.${p[2]}.0/24`;
};
const hashIp = (ip) => crypto.createHmac('sha256', process.env.IP_HMAC_SALT).update(ip).digest('hex');
// Trust-Proxy einmal in app.js
app.set('trust proxy', 1);
```

#### Sichere Cookie-Flags

```js
res.cookie('sid', sid, {
  httpOnly: true, secure: true, sameSite: 'lax',
  maxAge: 7 * 24 * 60 * 60 * 1000, path: '/',
});
```

#### Brute-Force (zweischichtig)

```js
import rateLimit, { ipKeyGenerator } from 'express-rate-limit';
const ipLimiter = rateLimit({ windowMs: 15*60_000, max: 5, keyGenerator: r => ipKeyGenerator(r.ip), skipSuccessfulRequests: true });
const emailLimiter = rateLimit({ windowMs: 60*60_000, max: 10, keyGenerator: r => `email:${(r.body?.email||'').toLowerCase()}`, skipSuccessfulRequests: true });
app.post('/login', ipLimiter, emailLimiter, loginHandler);
```

#### Sentry minimal-DSGVO-konform

```ts
Sentry.init({
  dsn: process.env.SENTRY_DSN,
  sendDefaultPii: false,
  tracesSampleRate: 0.1,
  beforeSend(event) {
    if (event.request?.headers) { delete event.request.headers.authorization; delete event.request.headers.cookie; }
    if (event.request?.data) for (const k of ['password','token','refresh_token','access_token','ssn']) if (k in event.request.data) event.request.data[k] = '[redacted]';
    if (event.user?.id) { event.user.id = hmacHashUserId(String(event.user.id)); delete event.user.email; delete event.user.ip_address; }
    return event;
  },
});
```

### Audit-Log-Schema (Logins)

```sql
CREATE TABLE auth_audit_log (
  id BIGSERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE SET NULL,
  email_hash TEXT,                    -- HMAC-SHA256(email)
  event_type TEXT NOT NULL,           -- login_success | login_failed | mfa_success | password_reset
  ip_address INET,                    -- volle IP, wird nach 7 Tagen genullt
  ip_truncated INET,                  -- /24 oder /48, bleibt 90 Tage
  session_id_hash TEXT,
  success BOOLEAN NOT NULL,
  failure_reason TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
-- Cron täglich:
-- UPDATE auth_audit_log SET ip_address = NULL WHERE ip_address IS NOT NULL AND created_at < NOW() - INTERVAL '7 days';
-- DELETE FROM auth_audit_log WHERE created_at < NOW() - INTERVAL '90 days';
```

### Häufige Fallen

- `tracesSampleRate: 1.0` in Production → DSGVO-Risiko durch Volldaten-Sampling, plus Performance.
- `Sentry.setUser({email})` → Klartext-Mail in jedem Event.
- `req.ip` ohne `app.set('trust proxy', ...)` hinter nginx → immer 127.0.0.1, Schutz wirkungslos.
- IP als Klartext im Redis-Key (`bf:user:${username}`) → Personenbezug ohne Pseudonymisierung.
- Authorization-Header in Pino/Winston-Logs → Tokens im Klartext.
- E-Mail-OTP als zweiter Faktor → NIST SP 800-63B-4 schließt das aus.

Detail siehe Repo: <https://github.com/wuemaikblume/dsgvo-skills/tree/main/claude/skills/dsgvo-auth-and-logging> (SKILL/AUTH-TOM/IP-ADDRESSES/LOGGING).

## E-Mail-Marketing (UWG § 7 + Art. 6 / 7 / 21 DSGVO + TDDDG § 25)

Greift bei: Newsletter-/Drip-/Re-Engagement-/Win-Back-Code, Newsletter-Provider-SDKs (Mailchimp, Klaviyo, HubSpot, Brevo, ActiveCampaign, MailerLite, CleverReach, Rapidmail), DOI-Endpoints, Tracking-Pixel und Click-Redirects in Mail-HTML, List-Unsubscribe-Header, Suppression-Logik, Service-Mails mit Werbe-Anteil. Gilt parallel zu UWG-Lauterkeitsrecht (DE/AT/CH).

### Decision Tree

1. **Ist der Mail-Inhalt Werbung iSd UWG § 7?** → Werbung weit auszulegen (BGH I ZR 218/07): Cross-Sell, Bewertungs-Bitte, Upgrade-Promotion = Werbung. Reine Trans-Mails (Versandstatus, Reset, MFA-Code, Rechnung) bleiben einwilligungsfrei.
2. **DOI dokumentiert?** → Token + IP + UA + Form-URL + Zeitstempel + `confirmed_at` müssen beweisbar sein (BGH I ZR 218/07 Beweislast). Single-Opt-In ist in DE/AT abmahnfähig; CH-Sonderfall bei reiner CH-Liste tolerabel, bei DACH-Mischliste unsicher.
3. **Bestandskunden-Privileg (UWG § 7 III)?** → Vier Voraussetzungen kumulativ: (a) Adresse aus Verkauf, (b) Werbung für ähnliche Waren/DL, (c) klarer Hinweis bei Erhebung + jeder Mail, (d) Widerruf kostenfrei. Lead-Magnet ≠ Verkauf. Cold-B2B ist NICHT erlaubt (§ 7 II Nr. 3 macht keine B2B-Ausnahme; LinkedIn-DM zählt analog BGH I ZR 169/04).
4. **Service vs. Werbung sauber getrennt?** → Mail-Templates explizit als `transactional` oder `marketing` typisieren. Cross-Sell-Block in Order-Confirmation = Werbung (OLG Hamm 4 U 121/13). Bewertungs-Bitte = Werbung (BGH VI ZR 225/17). Confirm-Mail darf KEINE Werbung enthalten (BGH I ZR 164/09).
5. **Tracking-Pixel oder Click-Redirect aktiv?** → Eigene Einwilligung pro Empfänger (TDDDG § 25 + Art. 6 + DSK 2021). Confirm-Mail rendert NIE Pixel. Externe Bilder via CID inline einbetten oder einwilligungs-gekoppelt rendern.
6. **One-Click-Unsubscribe + List-Unsubscribe-Header?** → Pflicht für Bulk-Sender ≥ 5.000 Mails/Tag (Gmail/Yahoo Sender Requirements Februar 2024 + Microsoft 2025). RFC 8058 + DKIM/SPF/DMARC + Spam-Rate < 0,3 %. Suppression-Hash (SHA-256) statt Klartext-E-Mail nach Widerruf.

### Code-Generation-Regel

**Default ist immer DSGVO-/UWG-konform.** Wenn der User explizit eine schwächere Variante will („Single-Opt-In reicht", „Pixel immer aktiv", „Confirm-Mail mit Promo", „kalte B2B-Liste"), zwei Varianten liefern:

- **A — DSGVO-/UWG-konform** als Default
- **B — Wunsch-Variante** mit Pflicht-Tabelle (Norm, Risiko, Konsequenz: UWG-Abmahnung mit 100–500 € Streitwert pro Mail / Bußgeld Art. 83 DSGVO / Aufsichtsbeanstandung)

Bei Cold-B2B-Mailing in DACH: **nur Variante A** — Cold-Outreach via E-Mail/LinkedIn-DM ist seit 2010 nicht erlaubt; alternativ DOI-Funnel statt Cold-Liste.

### Anbieter-Quick-Ref (DPF Mai 2026)

- **Mailchimp** (US, Intuit) — DPF Active (ID 7693). Open- und Click-Tracking by default aktiv → pro Audience deaktivieren oder einwilligungs-gekoppelt schalten. Confirm-Mails über Mailchimp Transactional / separat, nicht Marketing-Audience.
- **Klaviyo** (US) — DPF Active (ID 6149). Sehr granulares Profiling — DPIA prüfen bei großer Reichweite. Cross-Link `dsgvo-third-country-transfer/DPIA.md`.
- **HubSpot** (US) — DPF Active (ID 5812). EU-Hosting-Option seit 2022, muss aktiv gewählt werden — Default ist US.
- **ActiveCampaign** (US) — DPF Active (ID 4495). Site-Tracking-Snippet nur nach expliziter Einwilligung laden.
- **Brevo** (FR, Paris) — EU-Server Default, AVV im Standard-DPA. Häufige Migration weg von Mailchimp.
- **CleverReach** (DE, Rastede) / **Rapidmail** (DE, Freiburg) — DACH-Spezialisten, ISO 27001, EU-only.
- **MailerLite** (LT, Vilnius) — EU-Server Default, preisgünstige EU-Alternative.

### Code-Patterns

#### Double-Opt-In mit Token (TypeScript + Postgres)

```ts
import crypto from 'node:crypto';

const tokenBytes = crypto.randomBytes(32);
const token = tokenBytes.toString('hex');
const tokenHash = crypto.createHash('sha256').update(tokenBytes).digest('hex');
const emailHash = crypto.createHash('sha256').update(email.toLowerCase().trim()).digest('hex');

await db.query(
  `INSERT INTO subscription_consents
   (email, email_hash, ip_address, user_agent, form_url, purposes,
    confirmation_token_hash, confirmation_token_expires_at)
   VALUES ($1, $2, $3, $4, $5, $6, $7, NOW() + INTERVAL '7 days')`,
  [email, emailHash, truncateIp(req.ip), req.headers['user-agent'],
   formUrl, JSON.stringify(purposes), tokenHash]
);
await sendConfirmationMail(email, token); // Confirm-Mail OHNE Werbung, OHNE Pixel
```

#### Mail-Template-Klassifikation mit Send-Guard

```ts
type MailType = 'transactional' | 'marketing';

async function send(template: { id: string; type: MailType; ... }, recipient: Subscriber) {
  if (template.type === 'marketing' && !recipient.consent.marketing) {
    throw new ConsentError(`Recipient ${recipient.id} not consented to marketing.`);
  }
  await provider.send({
    to: recipient.email,
    template: template.id,
    trackOpens: template.type === 'marketing' && recipient.consent.tracking,
    trackClicks: template.type === 'marketing' && recipient.consent.tracking,
  });
}
```

#### One-Click-Unsubscribe + Suppression-Hash

```sql
CREATE TABLE email_suppression_list (
  id BIGSERIAL PRIMARY KEY,
  email_hash CHAR(64) NOT NULL UNIQUE,
  suppressed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  reason TEXT NOT NULL CHECK (reason IN
    ('user_unsubscribe','hard_bounce','spam_complaint','manual','re_permission_failed'))
);
```

```ts
// Versand-Filter
const eligible = await db.query(
  `SELECT email FROM subscribers
   WHERE active AND consent_marketing
     AND NOT EXISTS (SELECT 1 FROM email_suppression_list
                     WHERE email_hash = sha256(LOWER(TRIM(subscribers.email))))`
);
```

#### List-Unsubscribe-Header (RFC 8058)

```text
List-Unsubscribe: <mailto:unsubscribe@example.com?token=XYZ>, <https://example.com/unsubscribe?token=XYZ>
List-Unsubscribe-Post: List-Unsubscribe=One-Click
```

### Häufige Fallen

- **Confirm-Mail mit „Übrigens, hier unsere Angebote"** — BGH I ZR 164/09: Confirm-Mail darf keine Werbung. Gesamter DOI-Beweis fällt.
- **Cross-Sell-Block in Order-Confirmation** — OLG Hamm 4 U 121/13: macht aus Trans-Mail eine Werbe-Mail.
- **Mailchimp-Audience mit Default-Tracking** — TDDDG § 25 + DSK 2021: separate Einwilligung erforderlich, nicht im Newsletter-Häkchen mit-eingewilligt.
- **„LinkedIn-DM ist keine E-Mail"** — falsch; § 7 II Nr. 3 erfasst „elektronische Post" weit (BGH I ZR 169/04 zu SMS analog).
- **Re-Subscribe alter Listen ohne neuen DOI** — nach Widerruf ist alter Eintrag nicht reaktivierbar; neuer DOI nötig.
- **„CAN-SPAM reicht für Europa"** — falsch; UWG + DSGVO + ePrivacy gelten zusätzlich.
- **Klartext-E-Mail nach Unsubscribe in DB lassen** — Art. 5 I c verlangt Hash + Klartext-Löschung; Suppression läuft via Hash.
- **Bewertungs-Bitte ohne Marketing-Einwilligung** — BGH VI ZR 225/17: Werbung iSd § 7.

Detail siehe Repo: <https://github.com/wuemaikblume/dsgvo-skills/tree/main/claude/skills/dsgvo-email-marketing> (SKILL/CONSENT-AND-DOI/UWG-7/TRACKING-IN-MAIL/UNSUBSCRIBE-AND-RETENTION/SERVICE-VS-MARKETING).

## Quellen

- DSGVO: <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- Adäquanzbeschlüsse: <https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en>
- DPF-Liste: <https://www.dataprivacyframework.gov/list>
- BfDI Datentransfer: <https://www.bfdi.bund.de/DE/Fachthemen/Inhalte/Europa-Internationales/Internationaler_Datentransfer.html>
- EDPB 01/2020 (supplementary measures): <https://www.edpb.europa.eu/our-work-tools/our-documents/recommendations/recommendations-012020-measures-supplement-transfer_en>
- EDPB 2/2018 (Art. 49): <https://www.edpb.europa.eu/sites/default/files/files/file1/edpb_guidelines_2_2018_derogations_en.pdf>
- CNIL TIA Guide (9.7.2025): <https://www.cnil.fr/en/transfer-impact-assessment-tia-cnil-publishes-final-version-its-guide>
- SCCs (EU 2021/914): <https://eur-lex.europa.eu/eli/dec_impl/2021/914/oj>
- Latombe-Urteil (3.9.2025): <https://iapp.org/news/a/european-general-court-dismisses-latombe-challenge-upholds-eu-us-data-privacy-framework>
- UK-Adäquanz erneuert (19.12.2025): <https://ec.europa.eu/commission/presscorner/detail/en/ip_25_3059>
- Brasilien-Adäquanz (10.2.2026): <https://iapp.org/news/a/brazil-eu-mutual-adequacy-what-changes-for-data-transfers-and-what-doesn-t>
- UWG § 7 (DE): <https://www.gesetze-im-internet.de/uwg_2004/__7.html>
- TKG 2021 § 174 (AT): <https://www.ris.bka.gv.at/>
- UWG Art. 3 (CH): <https://www.fedlex.admin.ch/eli/cc/1988/223_223_223>
- TDDDG § 25 (DE, vormals TTDSG): <https://www.gesetze-im-internet.de/tddg/>
- BGH I ZR 218/07 (10.02.2011) — DOI-Beweislast
- BGH I ZR 164/09 (16.07.2008) — Confirm-Mail-Werbungsfreiheit
- BGH VI ZR 225/17 (10.07.2018) — Bewertungs-Bitte = Werbung
- OLG Hamm 4 U 121/13 (26.11.2013) — Cross-Sell in Trans-Mail
- RFC 8058 — One-Click-Unsubscribe: <https://www.rfc-editor.org/rfc/rfc8058>
- Gmail Sender Requirements: <https://support.google.com/mail/answer/81126>

## Disclaimer

Diese Anweisungen sind eine **Best-Practice-Sammlung mit Stand Mai 2026** (DPF-Status verifiziert 2026-05-04 gegen offizielle ITA-Excel-Liste), keine Rechtsberatung. Bei Art. 9-Daten, KRITIS, Berufsgeheimnis-Sektoren, Bußgeld-Risiko oder Aufsichtsanfragen: Anwalt für IT-Recht oder externen Datenschutzbeauftragten einbinden.
