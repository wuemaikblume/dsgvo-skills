---
name: dsgvo-third-country-transfer
description: Use when code or infrastructure decisions transfer personal data of EU/EEA users to non-EU services. Triggers on AWS us-*, Azure US/Asia, GCP us-* regions, S3 buckets outside EU, third-party SaaS APIs (OpenAI, Anthropic, Stripe, Sentry, Datadog, Mailgun, Twilio, Auth0, Clerk, Firebase, Supabase), CDN providers (Cloudflare, Fastly, Akamai), analytics (GA4, Mixpanel, Hotjar, Amplitude), error tracking, and any external service receiving names, emails, IPs, or user IDs. Covers GDPR Chapter V (Art. 44-49), EU-US Data Privacy Framework verification, Standard Contractual Clauses, Transfer Impact Assessments per CNIL/EDPB guidance, German DSK position, and US CLOUD Act risk for EU-hosted data.
---

# DSGVO Drittlandtransfer (DSGVO Kapitel V, Art. 44-49)

## Kernprinzip

Bevor Code oder Infrastruktur personenbezogene Daten an einen Drittland-Dienst sendet, müssen **zwei Dinge geklärt sein**:

1. **Rechtsgrundlage der Verarbeitung** (Art. 6 / Art. 9 DSGVO) — z.B. Vertrag, berechtigtes Interesse, Einwilligung
2. **Transfermechanismus für den Drittlandtransfer** (Art. 44-49 DSGVO) — z.B. Adäquanzbeschluss, SCCs, Ausnahme

Beides ist erforderlich. Art. 44-49 ersetzt Art. 6 nicht — er ergänzt ihn nur für den Transferweg. Die Verantwortung bleibt beim Verantwortlichen, auch bei DPF-zertifizierten US-Anbietern.

## Wann dieser Skill greift

**Trigger-Symptome:**
- Cloud-Region außerhalb EU/EEA (`us-east-1`, `us-central1`, `eastus`, `ap-*`, **achtung `eu-west-2` = UK**)
- SaaS-Integration mit US-Sitz (OpenAI, Anthropic, Stripe, Sentry, Datadog, Mailgun, Twilio, Auth0, Clerk, Firebase, Supabase US)
- File-Upload an Drittland-CDN/Storage (S3 US, Cloudflare R2, Backblaze)
- DB-Replica oder Backup in Drittland
- Analytics/Tracking-Pixel (GA4, Mixpanel, Hotjar, Amplitude) — beachte zusätzlich `EPRIVACY.md`
- Externe API mit Personendaten in Request-Body (Namen, E-Mails, IPs, User-IDs, Stack Traces)
- **Remote-Zugriff aus Drittland** auf EU-gespeicherte Daten (Support, Admin, Debugging) — siehe `CLOUD-ACT.md`

**Auch bei EU-Anbietern prüfen** (kein automatisches Skip):
- Subprozessoren-Liste (oft US-Backend für Hauptanbieter)
- Support-Standorte (24/7-Teams oft außerhalb EU)
- Backups, Logs, Monitoring (oft separate Region)
- Konzernzugriff bei US-Tochter eines EU-Anbieters

**Skip wenn:**
- Daten sind anonymisiert nach Erwägungsgrund 26 — d.h. Re-Identifizierung mit „**vernünftigerweise einsetzbaren Mitteln**" nicht mehr möglich (NICHT zu verwechseln mit Pseudonymisierung wie gehashten User-IDs — die unterliegen weiter der DSGVO)
- Keine personenbezogenen Daten im Datenfluss (öffentliche Geo-Daten, technische Metriken ohne User-Bezug)

## Decision Tree

```
1. Werden personenbezogene Daten verarbeitet?
   (Name, E-Mail, IP, User-ID, Cookie-ID, Standort, Stack Trace mit User-Pfad)
   ├─ Nein  → außerhalb DSGVO; Vertraulichkeit/Sicherheit beachten
   └─ Ja    → weiter zu 2

2. Rolle klären (siehe ROLES.md):
   ├─ Eigene Verarbeitung (Controller) ohne Drittland → Art. 6/9 + AVV-frei
   ├─ Auftragsverarbeitung (Processor) → AVV nach Art. 28 zwingend
   ├─ Gemeinsame Verantwortung → Vereinbarung nach Art. 26 zwingend
   └─ Plus: Rechtsgrundlage Art. 6 (oder 9 bei sensiblen Daten) festlegen
   → bei Drittlandtransfer weiter zu 3

3. Hat der Anbieter Sitz UND Verarbeitung in EU/EEA?
   ├─ Ja → kein Drittlandtransfer im Hauptverhältnis. ABER:
   │       Subprozessoren-Liste, Support, Backups, Konzernzugriff prüfen.
   │       Wenn dort Drittland → weiter zu 4 für diese Datenflüsse.
   └─ Nein → weiter zu 4

4. Drittland mit Adäquanzbeschluss der EU-Kommission?
   (Stand Mai 2026, 17 Drittländer/Gebiete: UK*, Schweiz, Japan, Südkorea,
    Kanada (kommerziell), Israel, Neuseeland, Argentinien, Uruguay,
    Andorra, Färöer-Inseln, Guernsey, Isle of Man, Jersey, **Brasilien**,
    plus EPO als internationale Organisation)
   * UK: am 19.12.2025 erneuert, Sunset-Klausel bis 27.12.2031
   * Brasilien: gegenseitige Adäquanz seit 10.2.2026 (ANPD Resolution 32)
   ├─ Ja  → erlaubt unter Adäquanzbeschluss
   ├─ USA → weiter zu 5 (DPF-Pfad)
   └─ Nein → weiter zu 6 (SCCs + TIA)

5. USA — Data Privacy Framework (DPF):
   a) Anbieter auf https://www.dataprivacyframework.gov/list mit
      Status "Active"?
   b) Zertifizierung umfasst die Datenkategorie?
      ("Non-HR Data" für Kundendaten / "HR Data" separat für
       Mitarbeiterdaten)
   c) Vertraglich verankert: DPF-Klausel im DPA + Beschwerdemechanismus?
   ├─ Alle 3 Ja → erlaubt unter DPF
   │  ⚠ ABER: Auch DPF-zertifizierte US-Konzerne unterliegen dem
   │  US CLOUD Act — auch wenn sie in `eu-central-1` hosten.
   │  Bei Art. 9-Daten / KRITIS / mehrjährigen Verträgen unbedingt
   │  CLOUD-ACT.md lesen für echte EU-souveräne Alternativen
   │  (Stackit, T-Systems, Hetzner, Scaleway, IONOS).
   └─ Nein → weiter zu 6

6. SCCs + Transfer Impact Assessment (TIA):
   a) Korrektes SCC-Modul wählen (Beschluss EU 2021/914):
      - Modul 1: Controller → Controller
      - Modul 2: Controller → Processor (häufigster Fall)
      - Modul 3: Processor  → Processor (Sub-Auftragsverarbeitung)
      - Modul 4: Processor  → Controller (selten)
   b) TIA durchführen (CNIL Practical Guide vom 9.7.2025):
      - Datenkategorien, Empfänger, Zweck
      - Recht und Praxis im Drittland (Government Access)
      - Risikobewertung
   c) Supplementary Measures (EDPB 01/2020) — wirksam nur, wenn der
      Importeur keinen Klartext-Zugriff braucht:
      - Technisch: Verschlüsselung at-rest + in-transit, Pseudonymisierung,
        clientseitige Verschlüsselung, BYOK
      - Vertraglich: Transparenz-Klauseln, Auditrechte
      - Organisatorisch: interne Prozesse, Schulung
      - Bei Klartext-Zugriff durch Importeur → Pseudonymisierung,
        EU-Proxy oder EU-Alternative bevorzugen
   d) Schriftlich dokumentieren UND im VVT (Art. 30) eintragen
   → BCRs als Alternative siehe CLOUD-ACT.md

→ Bei Behördenanordnung aus Drittland (z.B. US CLOUD Act): Art. 48 DSGVO
  beachten — siehe CLOUD-ACT.md
```

## Anbieter-Quick-Reference

> ⚠️ **Stichtags-Hinweis (Mai 2026).** DPF-Zertifizierungen können sich ändern. Vor Vertragsabschluss IMMER auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) live prüfen + Datum + DPF-Participant-ID dokumentieren. **Detaillierte Anbieter-Profile siehe `PROVIDERS.md`.**

| Anbieter | Status | Was zu tun ist |
|----------|--------|----------------|
| AWS Inc. | DPF (Amazon.com Inc.) | EU-Region (`eu-central-1`, `eu-west-1` Irland — **`eu-west-2` ist UK!**); DPA mit DPF-Klausel |
| Microsoft Azure | DPF | EU Data Boundary aktivieren; `westeurope` / `germanywestcentral` |
| Google Cloud | DPF | EU-Region; siehe `PROVIDERS.md` für Firebase-Spezifika |
| Cloudflare Inc. | DPF | EU Data Localization Suite; IP-Verarbeitung am Edge — TIA empfohlen |
| OpenAI | OpenAI Ireland Ltd. ist EWR-Vertragspartner; US-Mutter DPF | Vertrag mit OpenAI Ireland; SCCs für US-Backend; ZDR/MAM für sensible Prompts (nicht Standard) |
| Anthropic | DPF (zur Live-Prüfung) | DPA separat; ZDR via Enterprise/Bedrock prüfen |
| Stripe | hybride Rolle — Controller + Processor | siehe `PROVIDERS.md` (Detail-Erklärung) |
| Sentry | EU-Region verfügbar | EU DSN (`*.ingest.de.sentry.io`) + clientseitiges PII-Scrubbing (siehe Code-Patterns) |
| Datadog | DPF + EU-Site | EU1 Site (`datadoghq.eu`); kein US1 |
| Auth0 (Okta) | DPF | EU-Tenant explizit (`*.eu.auth0.com`) |
| Clerk | **DPF aktiv seit 22.2.2024** (Non-HR Data) | DPA + DPF-Klausel |
| Firebase (Google) | über Google LLC DPF | Firestore: `eur3` (BE/NL); **Cloud Functions zwingend `europe-west1` setzen** — sonst Fallback `us-central1` |
| Supabase | **NICHT DPF-zertifiziert** | SCCs Modul 2 + DPA; Project-Region EU; Subprozessoren/Edge Functions/Logs prüfen |
| Mailgun | DPF + EU-Region | EU-Region (`api.eu.mailgun.net`) |
| Hetzner / IONOS / Scaleway | EU-Anbieter | nur AVV nach Art. 28 |

## Code-Generierungs-Regel

Wenn der User **explizit eine Drittland-Region oder eine US-spezifische Konfiguration** anfragt (z.B. „us-east-1", „us-central1", „US-DSN", „global"):

1. **NICHT stillschweigend umbauen.** Der User-Wunsch wird respektiert.
2. **Beide Varianten im Code zeigen**, klar gekennzeichnet:
   - Die angefragte Variante (z.B. mit `us-east-1`)
   - Die DSGVO-konforme Variante (EU-Region + Whitelist-Pattern)
3. Über dem Code als Kurz-Bullet die DSGVO-Pflichten benennen, die der User vor Production-Deployment erfüllen muss (DPF-Verifikation / SCCs / TIA / VVT / Art. 13/14).
4. Bei sensiblen Daten (Art. 9) oder KRITIS: zusätzlich auf den **US CLOUD Act** hinweisen — auch DPF schützt nicht vor US-Behörden­anordnungen gegen US-Mütter (siehe `CLOUD-ACT.md`).

So sieht der User, was er angefragt hat, weiß was rechtlich nötig ist, und hat das compliante Pattern direkt zum Vergleich.

## Code-Patterns

### AWS S3 — Whitelist statt unsicherem Default

```typescript
// Nicht: ?? eu-central-1 → ENV-Override mit us-east-1 weiterhin möglich
const s3 = new S3Client({
  region: process.env.AWS_REGION ?? 'eu-central-1',
});

// Stattdessen: Fail-closed Whitelist
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
// Nicht: nur ip_address löschen — URLs, Query-Strings, Breadcrumbs,
// Headers und Stack Traces enthalten oft personenbezogene Daten
Sentry.init({
  dsn: 'https://abc@o123.ingest.de.sentry.io/456', // EU-Region
  sendDefaultPii: false,                           // SDK-PII-Default aus
  beforeSend(event) {
    delete event.user?.ip_address;
    delete event.user?.email;
    if (event.request) {
      delete event.request.cookies;
      delete event.request.headers;
      // Query-Strings können IDs/Tokens enthalten
      if (event.request.query_string) event.request.query_string = '[scrubbed]';
    }
    return event;
  },
});
```

Zusätzlich: serverseitige Data-Scrubbing-Regeln im Sentry-Projekt aktivieren.

### OpenAI — vertragliche Triangulation beachten

```typescript
// EWR-Nutzer: OpenAI Ireland Ltd. ist Vertragspartner.
// Backend-Verarbeitung läuft primär in US (intrakonzernlich via SCCs/DPF).
// Im VVT muss "OpenAI Ireland Ltd." als Auftragsverarbeiter stehen.
const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

// Für sensible Prompts: ZDR/Modified Abuse Monitoring beantragen
// (nicht Default, erfordert Genehmigung).
// Echte EU-Datenresidenz nur für berechtigte Projekte/Endpoints —
// EU-Residency-Genehmigung pro Projekt einholen.

// Strenge EU-Variante: Azure OpenAI in westeurope/germanywestcentral
// → Daten verbleiben in EU, MS-DPA gilt.
```

### Firebase Cloud Functions — Region zwingend setzen

```typescript
import { onDocumentCreated } from 'firebase-functions/v2/firestore';

// Nicht: ohne region() → fällt auf us-central1 zurück, auch wenn
// Firestore in eur3 (BE/NL) ist. Jeder Trigger erzeugt einen
// undokumentierten Drittlandtransfer.
export const onUserCreate = onDocumentCreated({
  document: 'users/{userId}',
  region: 'europe-west1', // ZWINGEND setzen
}, async (event) => { /* ... */ });
```

### Datadog — EU-Site hart codieren

```typescript
const config = new datadogApi.client.Configuration({
  serverConfiguration: {
    name: 'datadoghq.eu',
    site: 'datadoghq.eu',
  },
});
```

### Supabase — EU-Region wählen UND Subprozessoren prüfen

```bash
# Beim Anlegen: Region eu-central-1 (Frankfurt) oder eu-west-1 (Irland).
# Niemals us-east-1 für EU-Nutzer.
# Zusätzlich: SCCs Modul 2 + DPA unterzeichnen
# (Supabase ist nicht DPF-zertifiziert).
# Auth, Edge Functions, Logs, Backups, Support-Routing einzeln prüfen.
```

## DPF-Status live verifizieren

Ein US-Anbieter ist nur dann unter DPF nutzbar, wenn:

1. Auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) gelistet
2. Status: **„Active"** (nicht „Inactive" oder „Withdrawn")
3. Datenkategorie passt: **Non-HR Data** für Kundendaten / **HR Data** separat für Mitarbeiterdaten
4. Re-Zertifizierung jährlich erfolgt (Datum prüfen)
5. Genaue **juristische Entity** dokumentieren (z.B. Amazon.com Inc. statt „AWS")

## Pflicht-Dokumentation (VVT, Art. 30 DSGVO)

Pro Drittlandtransfer im VVT festhalten:

- Empfänger: exakte Firma + Land
- Rolle: Controller / Processor / Joint Controller (siehe `ROLES.md`)
- Rechtsgrundlage Verarbeitung: Art. 6 / Art. 9
- Transfermechanismus: Art. 45 (Adäquanz) / Art. 46 (SCCs/BCRs) / Art. 49 (Ausnahme)
- Bei DPF: Anbieter + DPF-Participant-ID + Datenkategorie + Stichtag der Prüfung (idealerweise Screenshot)
- Bei SCCs: Modul-Nummer + Vertragsdatum + Datum der TIA
- Bei Art. 49-Ausnahme: Begründung pro konkretem Transfer (eng auszulegen)
- Drittland-Subprozessoren des Hauptanbieters: Liste mitführen + Genehmigung nach Art. 28 Abs. 2 DSGVO

Zusätzlich: Information der Betroffenen nach **Art. 13/14 DSGVO** über Empfänger und Drittlandtransfer ist Pflicht (Datenschutzerklärung).

## Häufige Fehler

| Annahme | Realität |
|---------|----------|
| „Adäquanzbeschluss → AVV reicht" | AVV nach Art. 28 nur bei Auftragsverarbeitung. Bei Controller→Controller: Art. 6/9 + Art. 13/14, ggf. Art. 26 — siehe `ROLES.md` |
| „Art. 44-49 ist die Rechtsgrundlage" | Falsch — sind Transfermechanismen. Rechtsgrundlage der Verarbeitung ist Art. 6 / 9 DSGVO. |
| „AWS hat DPF, also egal welche Region" | DPF betrifft Rechtmäßigkeit, nicht Speicherort. EU-Region trotzdem wählen. |
| „DPF-Anbieter brauchen kein TIA" | Korrekt: TIA-Pflicht nur bei Art. 46-Tools (SCCs/BCRs). Bei DPF (Art. 45) entfällt die Pflicht. Risikobewertung wegen Schrems-III empfohlen. |
| „Cloudflare ist nur Proxy" | IPs/Header sind personenbezogen. Cloudflare Inc. (US) verarbeitet sie → DPF + supplementary measures. |
| „OpenAI bekommt nur Texte" | Texte enthalten Namen, IDs, sensible Inhalte. Pseudonymisierung im Prompt + ggf. ZDR. |
| „UK ist EU-äquivalent für immer" | UK-Adäquanz am 19.12.2025 erneuert, Sunset 27.12.2031. Vor Ablauf prüfen. |
| „Stripe ist EU, alles entspannt" | Hybride Rolle (Controller + Processor je Service). Siehe `PROVIDERS.md`. Datenflüsse zu Stripe Inc. (US) dokumentieren. |
| „SCCs unterschrieben = fertig" | Ohne TIA + supplementary measures angreifbar. |
| „Wir nutzen `eu-west-1`, also keine US-Berührung" | Backups, Logs, Monitoring, Cross-Region-Replikation oft separat. Provider-Doku prüfen. |
| „Daten in `eu-central-1` sind vor US-Behörden sicher" | Falsch — US CLOUD Act greift bei US-Muttergesellschaften (AWS, Microsoft, Google, Cloudflare, OpenAI, Supabase, Clerk, Sentry US-Backend etc.), auch wenn die Server-Region in der EU liegt. Bei Art. 9-Daten / KRITIS unbedingt EU-Konzerne (Stackit, T-Systems, Hetzner, Scaleway, IONOS) prüfen — siehe `CLOUD-ACT.md`. |
| „Customer hat eingewilligt, also Art. 49" | Art. 49 Abs. 1 lit. a (Einwilligung) ist nach EDPB-Guidelines 2/2018 nur für **gelegentliche, nicht-wiederholte** Transfers zulässig — keine Routine-Grundlage für SaaS/APIs. |
| „User-ID ist gehasht, also anonymisiert" | Pseudonymisierung — unterliegt weiter der DSGVO. Echte Anonymisierung erfordert, dass auch mit Zusatzwissen niemand re-identifizieren kann (Erwägungsgrund 26). |

## Schrems-III-Risiko (Stand Mai 2026)

DPF gilt: Adäquanzbeschluss vom 10. Juli 2023 in Kraft. Am 3. September 2025 hat das Generalgericht (T-553/23) die Latombe-Klage [abgewiesen](https://iapp.org/news/a/european-general-court-dismisses-latombe-challenge-upholds-eu-us-data-privacy-framework). Eine Berufung beim EuGH ist möglich, eine separate noyb-/Schrems-III-Klage wurde angekündigt aber nach öffentlich auffindbarer Lage nicht erhoben.

**Restrisiko-Strategie als Architektur-Faktor (nicht erst nachträglich):**

- Bei sensiblen Datenkategorien (Art. 9) oder hohem Compliance-Anspruch:
  - DPF-Anbieter zusätzlich mit SCCs absichern (doppelte Absicherung)
  - Verschlüsselung at-rest + in-transit als Standard, BYOK wo möglich
  - Datenminimierung im Prompt/API-Body
  - Pseudonymisierung vor Drittland-Versand
- Bei mehrjährigen Vertragslaufzeiten mit US-Anbietern:
  - **EU-Alternative von Anfang an evaluieren** (kein teures Re-Platforming bei DPF-Wegfall)
  - Exit-Strategie schriftlich

## Vertiefung — wann welche Sub-Datei lesen?

- **Rolle unklar** (Controller / Processor / Joint Controller / Subprozessoren) → `ROLES.md`
- **Sensible Daten / Profiling / großvolumiges Tracking** → `DPIA.md` (DSFA-Pflicht prüfen)
- **US-Konzern hostet in EU** (AWS, Azure, Google, OpenAI, Cloudflare, Supabase, Clerk etc. — auch in `eu-central-1` o.ä.) **UND** sensible Daten / Art. 9 / KRITIS / mehrjähriger Vertrag → `CLOUD-ACT.md` lesen für Cloud-Act-Risiko + EU-souveräne Alternativen
- **Behördenanordnung aus Drittland** (US National Security Letter, FBI-Subpoena, etc.) → `CLOUD-ACT.md` (Art. 48 DSGVO + EDPB 02/2024)
- **Nutzer in der Schweiz** → `CH-REVDSG.md` (revDSG, Swiss-US DPF, EDÖB)
- **Cookies / Tracking-Pixel / Analytics** → `EPRIVACY.md` (TTDSG, separater Pflichtenkreis)
- **Detaillierte Anbieter-Profile** (OpenAI, Stripe, Firebase, Supabase, Cloudflare etc.) → `PROVIDERS.md`

## Quellen

- DSGVO (konsolidiert): <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- EU-Kommission, Adäquanzbeschlüsse: <https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en>
- EU-US Data Privacy Framework: <https://www.dataprivacyframework.gov/>
- DPF-Liste: <https://www.dataprivacyframework.gov/list>
- BfDI — Internationaler Datentransfer: <https://www.bfdi.bund.de/DE/Fachthemen/Inhalte/Europa-Internationales/Internationaler_Datentransfer.html>
- DSK Beschlüsse: <https://www.bfdi.bund.de/DE/Fachthemen/Gremienarbeit/Datenschutzkonferenz/DSK-tableBeschl%C3%BCsse.html>
- EDPB Recommendations 01/2020 (supplementary measures): <https://www.edpb.europa.eu/our-work-tools/our-documents/recommendations/recommendations-012020-measures-supplement-transfer_en>
- EDPB Guidelines 2/2018 (Art. 49 derogations): <https://www.edpb.europa.eu/sites/default/files/files/file1/edpb_guidelines_2_2018_derogations_en.pdf>
- CNIL Practical TIA Guide (final, 9.7.2025): <https://www.cnil.fr/en/transfer-impact-assessment-tia-cnil-publishes-final-version-its-guide>
- SCCs (Beschluss EU 2021/914): <https://eur-lex.europa.eu/eli/dec_impl/2021/914/oj>
- Latombe-Urteil (Generalgericht, 3.9.2025, T-553/23): <https://iapp.org/news/a/european-general-court-dismisses-latombe-challenge-upholds-eu-us-data-privacy-framework>
- UK-Adäquanzbeschluss erneuert (19.12.2025): <https://ec.europa.eu/commission/presscorner/detail/en/ip_25_3059>
- Brasilien-Adäquanzbeschluss (10.2.2026): <https://iapp.org/news/a/brazil-eu-mutual-adequacy-what-changes-for-data-transfers-and-what-doesn-t>

## Disclaimer

Dieser Skill ist eine **Best-Practice-Sammlung mit Stand Mai 2026**, keine Rechtsberatung. Bei sensiblen Datenkategorien (Art. 9 DSGVO), hohen Compliance-Anforderungen, KRITIS, Bußgeld-Risiken oder konkreten Aufsichtsanfragen: Anwalt für IT-Recht oder externen Datenschutzbeauftragten konsultieren.
