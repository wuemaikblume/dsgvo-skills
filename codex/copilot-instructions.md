# DSGVO-Compliance — Anweisungen für KI-Coding-Assistenten

> GitHub Copilot lädt diese Datei aus `.github/copilot-instructions.md` für alle Code-Suggestions im Workspace. Sie zwingt Copilot, vor jedem Code-Vorschlag DSGVO-Konformität zu prüfen, wenn personenbezogene Daten an Drittland-Dienste fließen. Stand Mai 2026, v1.3. Quelle und Updates: <https://github.com/wuemaikblume/dsgvo-skills>.

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
| OpenAI | OpenAI Ireland Ltd. EWR-Vertragspartner; US-Mutter DPF | Vertrag mit Ireland; SCCs für US-Backend; ZDR/Modified Abuse Monitoring nur auf Antrag |
| Anthropic | DPF (Live-Prüfung) | DPA separat; ZDR via Enterprise/Bedrock prüfen |
| Stripe | hybrid: Controller + Processor je Service | Datenflüsse zu Stripe Inc. (US) im VVT dokumentieren |
| Sentry | EU-Region verfügbar | EU-DSN `*.ingest.de.sentry.io` + breites PII-Scrubbing |
| Datadog | DPF + EU-Site | EU1 (`datadoghq.eu`), nicht US1 |
| Auth0 | DPF | EU-Tenant explizit (`*.eu.auth0.com`) |
| Clerk | **DPF aktiv seit 22.2.2024** | DPA + DPF-Klausel |
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
// EWR-Nutzer: Vertragspartner = OpenAI Ireland Ltd.
// Backend-Verarbeitung primär US (intrakonzernlich via SCCs/DPF).
// VVT-Eintrag: "OpenAI Ireland Ltd." als Auftragsverarbeiter.
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

## Disclaimer

Diese Anweisungen sind eine **Best-Practice-Sammlung mit Stand Mai 2026**, keine Rechtsberatung. Bei Art. 9-Daten, KRITIS, Berufsgeheimnis-Sektoren, Bußgeld-Risiko oder Aufsichtsanfragen: Anwalt für IT-Recht oder externen Datenschutzbeauftragten einbinden.
