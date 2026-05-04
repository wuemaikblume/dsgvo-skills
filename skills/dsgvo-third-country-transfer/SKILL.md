---
name: dsgvo-third-country-transfer
description: Use when code or infrastructure decisions transfer personal data of EU/EEA users to non-EU services. Triggers on AWS us-*, Azure US/Asia, GCP us-* regions, S3 buckets outside EU, third-party SaaS APIs (OpenAI, Anthropic, Stripe, Sentry, Datadog, Mailgun, Twilio, Auth0, Clerk, Firebase, Supabase), CDN providers (Cloudflare, Fastly, Akamai), analytics (GA4, Mixpanel, Hotjar, Amplitude), error tracking, and any external service receiving names, emails, IPs, or user IDs. Covers GDPR Art. 44-49, EU-US Data Privacy Framework verification, Standard Contractual Clauses, Transfer Impact Assessments per CNIL/EDPB guidance, and German DSK position.
---

# DSGVO Drittlandtransfer (Art. 44-49)

## Kernprinzip

Personenbezogene Daten dürfen die EU/EEA nur verlassen, wenn eine Rechtsgrundlage nach Art. 44-49 DSGVO vorliegt. **Bevor Code oder Infrastruktur Daten an einen Drittland-Dienst sendet, muss diese Rechtsgrundlage geklärt UND dokumentiert sein.** Die Verantwortung bleibt immer beim Verantwortlichen — auch bei DPF-zertifizierten US-Anbietern.

## Wann dieser Skill greift

**Trigger-Symptome:**
- Cloud-Region außerhalb EU/EEA (`us-east-1`, `us-central1`, `eastus`, `ap-*`)
- SaaS-Integration mit US-Sitz (OpenAI, Anthropic, Stripe, Sentry, Datadog, Mailgun, Twilio, Auth0, Clerk, Firebase, Supabase US)
- File-Upload an Drittland-CDN/Storage (S3 US, Cloudflare R2, Backblaze)
- DB-Replica oder Backup in Drittland
- Analytics/Tracking-Pixel (GA4, Mixpanel, Hotjar, Amplitude)
- Externe API mit Personendaten in Request-Body (Namen, E-Mails, IPs, User-IDs, Stack Traces)

**Skip wenn:**
- Anbieter ist 100% in EU/EEA hosted (Hetzner, IONOS, Scaleway, OVHcloud EU)
- Daten sind echt anonymisiert (Erwägungsgrund 26 — re-Identifizierung ausgeschlossen)
- Keine personenbezogenen Daten im Datenfluss (z.B. öffentliche Geo-Daten ohne User-Bezug)

## Decision Tree

```
1. Werden personenbezogene Daten verarbeitet?
   (Name, E-Mail, IP, User-ID, Cookie-ID, Standort, Stack Trace mit User-Pfad)
   ├─ Nein  → außerhalb DSGVO; nur Vertraulichkeit/Sicherheit beachten
   └─ Ja    → weiter zu 2

2. Hat der Anbieter Sitz UND Verarbeitung in EU/EEA?
   ├─ Ja    → kein Drittlandtransfer; AVV nach Art. 28 reicht
   └─ Nein  → weiter zu 3

3. Drittland mit Adäquanzbeschluss der EU-Kommission?
   (Stand Mai 2026: UK*, Schweiz, Japan, Südkorea, Kanada (kommerziell),
    Israel, Neuseeland, Argentinien, Uruguay, Andorra, Färöer-Inseln,
    Guernsey, Isle of Man, Jersey)
   * UK: am 19.12.2025 erneuert, Laufzeit mit Sunset-Klausel
     bis 27.12.2031
   ├─ Ja    → erlaubt; AVV + Dokumentation im VVT genügen
   │         WICHTIG: auch wenn der Hauptanbieter EU-Sitz hat,
   │         kann er Subprozessoren in Drittländern einsetzen —
   │         in der Anbieter-Doku oder im AVV prüfen
   ├─ USA   → weiter zu 4 (DPF-Pfad)
   └─ Nein  → weiter zu 5 (SCCs + TIA)

4. USA — Data Privacy Framework (DPF):
   a) Anbieter auf https://www.dataprivacyframework.gov/list
      mit Status "Active"?
   b) Zertifizierung umfasst die Datenkategorie?
      ("Non-HR Data" für Kundendaten / "HR Data" separat für
       Mitarbeiterdaten)
   c) Vertraglich verankert: Anbieter erfüllt DPF-Pflichten +
      Beschwerdemechanismus?
   ├─ Alle 3 Ja → erlaubt unter DPF + AVV
   └─ Nein     → weiter zu 5

5. SCCs + Transfer Impact Assessment (TIA):
   a) Korrektes SCC-Modul wählen (EU 2021/914):
      - Modul 1: Controller → Controller
      - Modul 2: Controller → Processor (häufigster Fall:
        EU-Verantwortlicher beauftragt US-Auftragsverarbeiter)
      - Modul 3: Processor  → Processor (Sub-Auftragsverarbeitung)
      - Modul 4: Processor  → Controller (selten — z.B. EU-Prozessor
        gibt Daten an US-Hauptverantwortlichen zurück)
   b) TIA durchführen (CNIL Practical Guide Juli 2025):
      - Datenkategorien, Empfänger, Zweck
      - Recht und Praxis im Drittland (Government Access)
      - Risikobewertung
   c) Supplementary Measures (EDPB 01/2020):
      - Technisch: starke Verschlüsselung at-rest + in-transit,
        Pseudonymisierung, BYOK
      - Vertraglich: Transparenz-Klauseln, Auditrechte
      - Organisatorisch: interne Prozesse, Schulung
   d) Schriftlich dokumentieren UND im VVT (Art. 30) eintragen
```

## Schnell-Referenz: häufige Anbieter

> ⚠️ **Stichtags-Hinweis:** Die folgende Tabelle wurde **Mai 2026** erstellt und basiert auf öffentlichen Angaben der Anbieter. **DPF-Zertifizierungen wurden nicht für jeden Eintrag live verifiziert** — Zertifizierungen können auslaufen, zurückgezogen werden oder die Datenkategorie wechseln. **Vor Vertragsabschluss IMMER auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) live prüfen** und Datum der Prüfung dokumentieren.

| Anbieter | Status (per Anbieter-Angabe) | Was zu tun ist |
|----------|------------------------------|----------------|
| AWS Inc. | DPF-zertifiziert | EU-Region wählen (`eu-central-1`, `eu-west-1`); DPA mit DPF-Klausel |
| Microsoft Azure | DPF-zertifiziert | EU Data Boundary aktivieren; `westeurope` / `germanywestcentral` |
| Google Cloud (LLC) | DPF-zertifiziert (deckt Firebase ab) | EU-Region (`europe-west1`, `europe-west3`); Assured Workloads für sensible Daten |
| Cloudflare Inc. | DPF-zertifiziert | TIA empfohlen; optional Geo-Restrictions (EU Data Localization Suite) |
| OpenAI | DPF-zertifiziert | AVV separat; **ZDR (Zero Data Retention) und EU-Datenresidenz sind Enterprise-only** — für Standard-API gilt: nicht in EU verarbeitet |
| Anthropic | DPF-zertifiziert (zur Prüfung empfohlen) | AVV separat; ZDR via Enterprise/Bedrock prüfen |
| Stripe | EU-Verantwortlicher (Stripe Payments Europe Ltd, IE) | AVV; Datenflüsse zu Stripe Inc. (US, DPF-zertifiziert) dokumentieren |
| Sentry | EU-Region verfügbar | EU DSN nutzen (`*.ingest.de.sentry.io`); sonst DPF + AVV |
| Mailgun | DPF + EU-Region | EU-Region wählen (`api.eu.mailgun.net`) |
| Datadog | DPF + EU-Site | EU1 Site (`datadoghq.eu`); kein US1 |
| Auth0 (Okta) | DPF-zertifiziert | EU-Tenant explizit anfordern (`*.eu.auth0.com`) |
| Clerk | DPF-Status zur Prüfung | DPA + DPF-Klausel; EU-Datenresidenz auf Anfrage prüfen |
| Firebase (Google) | abgedeckt über Google LLC DPF | Multi-Region: `eur3` (Belgien/NL); kein `us-central` |
| Supabase | EU-Region verfügbar; DPF-Status zur Prüfung | Project Region = EU (Frankfurt/Irland); selbst hosten möglich |
| Hetzner | EU-Anbieter | nur AVV nötig |
| IONOS | EU-Anbieter | nur AVV nötig |
| Scaleway | EU-Anbieter (FR) | nur AVV nötig |

## Code-Patterns

### AWS S3: Region erzwingen

```typescript
// Nicht: Default oder hartkodiert auf US
const s3 = new S3Client({ region: 'us-east-1' });

// Stattdessen: EU-Region per ENV mit sicherem Default
const s3 = new S3Client({
  region: process.env.AWS_REGION ?? 'eu-central-1',
});
```

### Sentry: EU-Hosting nutzen

```typescript
// Nicht: US-DSN (default)
Sentry.init({ dsn: 'https://abc@o123.ingest.sentry.io/456' });

// Stattdessen: EU-DSN
Sentry.init({
  dsn: 'https://abc@o123.ingest.de.sentry.io/456',
  // Personendaten in Stack Traces minimieren
  beforeSend(event) {
    delete event.user?.ip_address;
    return event;
  },
});
```

### OpenAI: EU-Datenresidenz oder Azure-Alternative

```typescript
// Direkt OpenAI: DPF-zertifiziert, aber Verarbeitung primär in US.
// Mindestens: AVV + ZDR (Zero Data Retention) für sensible Prompts.
const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

// Alternative für strikte EU-Anforderungen:
// Azure OpenAI mit Region 'westeurope' oder 'germanywestcentral'
// → Daten verbleiben in EU, MS-DPA gilt
```

### Datadog: EU-Site hart setzen

```typescript
// Nicht: Default ist US1
const config = new datadogApi.client.Configuration({});

// Stattdessen: EU1 Site
const config = new datadogApi.client.Configuration({
  serverConfiguration: {
    name: 'datadoghq.eu',
    site: 'datadoghq.eu',
  },
});
```

### Supabase: EU-Project wählen

```bash
# Beim Anlegen des Projekts (Dashboard oder CLI):
# Region: eu-central-1 (Frankfurt) oder eu-west-1 (Irland)
# Niemals us-east-1 für Produktion mit EU-Usern
```

## DPF-Status live verifizieren

Ein US-Anbieter ist nur dann unter DPF nutzbar, wenn:

1. Auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) gelistet
2. Status: **„Active"** (nicht „Inactive" oder „Withdrawn")
3. Datenkategorie passt:
   - **Non-HR Data** für Kundendaten (Standard)
   - **HR Data** für Mitarbeiterdaten (separates Häkchen — viele Anbieter nur Non-HR)
4. Re-Zertifizierung jährlich erfolgt (Datum prüfen, läuft sonst aus)

## Pflicht-Dokumentation

Im **Verzeichnis von Verarbeitungstätigkeiten (VVT, Art. 30 DSGVO)** für jeden Drittlandtransfer festhalten:

- Empfänger (Firma + Land)
- Rechtsgrundlage des Transfers (Art. 45 / 46 / 49 DSGVO)
- Bei DPF: Anbieter-Name auf der DPF-Liste + Datenkategorie + Stichtag der Prüfung
- Bei SCCs: Modul-Nummer + Vertragsdatum + Datum der TIA
- Bei Art. 49-Ausnahme: Begründung pro konkretem Transfer (eng auszulegen, kein Dauergebrauch)
- Bei Drittland-Subprozessoren des Hauptanbieters: Liste mitführen

## Häufige Fehler

| Annahme | Realität |
|---------|----------|
| „AWS hat DPF, also egal welche Region" | DPF betrifft die Recht­mäßigkeit des Transfers, **nicht** den Speicherort. EU-Region trotzdem wählen — sonst echte Datenausreise. |
| „DPF-Anbieter brauchen kein TIA" | Korrekt: TIA ist nur bei Art. 46-Tools (SCCs/BCRs) Pflicht. **Aber** wegen Schrems-III-Risiko ist eine Risikobewertung dringend empfohlen, vor allem bei sensiblen Daten. |
| „Cloudflare ist nur Proxy, keine Daten gespeichert" | IP-Adressen und Request-Header sind personenbezogen. Cloudflare Inc. (US) verarbeitet sie → DPF-Verifikation Pflicht. |
| „OpenAI bekommt nur Texte" | Texte enthalten oft Namen, IDs, sensible Inhalte. Datenfluss-Analyse zwingend. ZDR oder Pseudonymisierung im Prompt nutzen. |
| „UK ist EU-äquivalent, gilt für immer" | UK-Adäquanzbeschluss am 19.12.2025 erneuert (Sunset 27.12.2031). Vor Ablauf erneut prüfen. |
| „Stripe ist EU, alles entspannt" | Stripe Payments Europe Ltd ist Verantwortlicher, aber Datenflüsse zu Stripe Inc. (US, DPF-zertifiziert) finden statt — dokumentieren. |
| „SCCs unterschrieben = fertig" | SCCs ohne TIA + supplementary measures sind aufsichtsrechtlich angreifbar. DSK und EDPB fordern beide. |
| „Wir nutzen `eu-west-1`, also keine US-Berührung" | Backups, Logs, Monitoring laufen oft separat in US-Regionen. Provider-Doku zu Cross-Region-Datenflüssen prüfen. |
| „Customer hat eingewilligt, also Art. 49" | Art. 49 Abs. 1 lit. a (Einwilligung) ist nach EDPB-Leitlinien nur für **gelegentliche, nicht-wiederholte** Transfers zulässig — nicht als Dauergrundlage. |

## Schrems-III-Risiko (Stand Mai 2026)

DPF gilt: Adäquanzbeschluss vom 10. Juli 2023 ist in Kraft. Am 3. September 2025 hat das EU-Gericht (General Court, Rechtssache T-553/23) die Latombe-Klage gegen DPF [abgewiesen](https://iapp.org/news/a/european-general-court-dismisses-latombe-challenge-upholds-eu-us-data-privacy-framework). Latombe hat Berufung beim EuGH eingelegt — eine „Schrems III"-Klage ist zusätzlich angekündigt, aber noch nicht erhoben. **Restrisiko bleibt:**

- Bei sensiblen Datenkategorien (Art. 9 DSGVO) oder hohem Compliance-Anspruch:
  - DPF-Anbieter zusätzlich mit SCCs absichern (doppelte Absicherung)
  - Verschlüsselung at-rest + in-transit als Standard
  - Datenminimierung auf das absolut Notwendige
  - BYOK (Bring Your Own Key) wo möglich
- Bei Vertragslaufzeiten mit US-Anbieter über mehrere Jahre:
  - Exit-Strategie und EU-Alternative dokumentieren (Lock-in-Risiko bei Wegfall des DPF)

## Quellen (zum eigenen Verifizieren)

- DSGVO Art. 44-49: <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- EU-US Data Privacy Framework: <https://www.dataprivacyframework.gov/>
- DPF-Liste: <https://www.dataprivacyframework.gov/list>
- BfDI — Internationaler Datentransfer: <https://www.bfdi.bund.de/DE/Fachthemen/Inhalte/Europa-Internationales/Internationaler_Datentransfer.html>
- Datenschutzkonferenz (DSK) — Beschlüsse: <https://www.bfdi.bund.de/DE/Fachthemen/Gremienarbeit/Datenschutzkonferenz/DSK-tableBeschl%C3%BCsse.html>
- EDPB Recommendations 01/2020 (supplementary measures): <https://www.edpb.europa.eu/our-work-tools/our-documents/recommendations/recommendations-012020-measures-supplement-transfer_en>
- CNIL Practical TIA Guide (final, 9. Juli 2025): <https://www.cnil.fr/en/transfer-impact-assessment-tia-cnil-publishes-final-version-its-guide>
- Standardvertragsklauseln (Durchführungsbeschluss 2021/914): <https://eur-lex.europa.eu/eli/dec_impl/2021/914/oj>
- Latombe-Urteil (Generalgericht, 3.9.2025, T-553/23): <https://iapp.org/news/a/european-general-court-dismisses-latombe-challenge-upholds-eu-us-data-privacy-framework>
- UK-Adäquanzbeschluss erneuert (19.12.2025): <https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en>

## Disclaimer

Dieser Skill ist eine **Best-Practice-Sammlung mit Stand Mai 2026**, keine Rechtsberatung. Bei sensiblen Datenkategorien (Art. 9 DSGVO), hohen Compliance-Anforderungen, KRITIS, oder konkreten Aufsichtsanfragen: Anwalt für IT-Recht oder externen Datenschutzbeauftragten konsultieren.
