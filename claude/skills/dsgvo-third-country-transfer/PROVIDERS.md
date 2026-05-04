# Anbieter-Profile (Detail)

> Erweiterte Profile zu den in `SKILL.md` genannten Anbietern. Stand Mai 2026. Vor jedem Vertragsabschluss DPF-Status auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) live prüfen + Datum + DPF-Participant-ID dokumentieren.

## AWS (Amazon Web Services)

- **Juristische Entity:** Amazon.com, Inc. ist DPF-Teilnehmer; Amazon Web Services, Inc. als „covered entity" enthalten. Im VVT exakte Entity nennen.
- **EU-Regionen:**
  - `eu-central-1` (Frankfurt)
  - `eu-central-2` (Zürich — Schweiz, Adäquanz)
  - `eu-west-1` (Irland)
  - `eu-west-3` (Paris)
  - `eu-north-1` (Stockholm)
  - `eu-south-1` (Mailand)
- **WICHTIG: `eu-west-2` ist London (UK)** — UK ist Adäquanzland (bis 27.12.2031), aber **nicht EU/EEA**. Wenn striktes EU-Hosting gefordert ist (z.B. KRITIS-Vorgabe), ausschließen.
- **Cross-Region-Datenflüsse prüfen:** CloudWatch-Logs, IAM, einige Backup-Konfigurationen können Daten in US-Regionen replizieren. Service-Doku konsultieren.
- **Pflichten:** DPA mit DPF-Klausel; bei sensiblen Daten zusätzlich SCCs als Belt-and-Suspenders.

## Microsoft Azure / 365

- **Status:** DPF-zertifiziert.
- **EU-Regionen:** `westeurope` (Niederlande), `northeurope` (Irland), `germanywestcentral` (Frankfurt), `germanynorth` (Berlin), `francecentral`, `swedencentral`, `switzerlandnorth`.
- **EU Data Boundary:** seit 2024 vollständig — Customer Data und Service-Daten (Logs, Telemetrie) bleiben in EU. **Aktivierung in Tenant-Settings prüfen.**
- **Microsoft 365:** unter EU Data Boundary; bei sensiblen Daten zusätzlich Customer Lockbox + Customer Key.

## Google Cloud Platform / Workspace

- **Status:** Google LLC ist DPF-Teilnehmer; deckt GCP, Workspace und Firebase ab.
- **EU-Regionen:** `europe-west1` (Belgien), `europe-west3` (Frankfurt), `europe-west4` (Niederlande), `europe-west6` (Zürich — CH), `europe-west9` (Paris), `europe-north1` (Finnland).
- **Multi-Region:** `eur3` für Cloud Storage und Firestore (Daten in BE/NL, Witness in Finnland) — gilt **nur** für diese Dienste.
- **Assured Workloads:** für besondere Anforderungen (Public Sector, sensitive Daten) — schränkt Support- und Konsolen-Zugriffe auf EU-Personal ein.

## Firebase (Google)

- **Status:** abgedeckt über Google LLC DPF.
- **Firestore:** Multi-Region `eur3` ist EU-konform (Belgien + Niederlande, Witness Finnland).
- **⚠ Cloud Functions:** **kein automatisches EU-Routing**. Ohne explizites `region: 'europe-west1'` (oder andere EU-Region) fallen Functions auf Default `us-central1` zurück. Bei jedem Trigger fließen Daten transatlantisch — undokumentierter Drittlandtransfer pro Aufruf.
- **Authentication, Storage, Hosting, Realtime Database, Crashlytics:** alle einzeln auf EU-Region konfigurieren — kein Sammel-Setting.
- **Analytics:** nutzt Google Analytics — separater ePrivacy/TTDSG-Pflichtenkreis (siehe `EPRIVACY.md`).

## Cloudflare

- **Status:** Cloudflare Inc. ist DPF-Teilnehmer.
- **Architekturrolle:** CDN + Reverse Proxy → verarbeitet IP-Adressen, HTTP-Header, TLS-Metadaten am Edge — auch wenn Origin in EU.
- **EU Data Localization Suite:** kostenpflichtige Option, die Verarbeitung von Customer Data auf EU-Edges beschränkt.
- **Pflichten:**
  - DPA mit DPF-Klausel
  - **TIA empfohlen** wegen Schrems-III-Risiko und IP-Verarbeitung am Edge
  - Bei sensiblen Daten: clientseitige Verschlüsselung (TLS-Termination am Origin statt am Edge)

## OpenAI

- **Vertragsstruktur (für EWR-Nutzer):**
  - **Vertragspartner:** OpenAI Ireland Limited (Dublin) — siehe OpenAI DPA
  - **Subprozessoren:** OpenAI OpCo, LLC (US) und weitere
  - **US-Mutter** ist DPF-zertifiziert; intrakonzernlicher Transfer von OpenAI Ireland in die US erfolgt zusätzlich auf Basis SCCs (Modul 2/3)
- **VVT-Eintrag:** „OpenAI Ireland Limited" als Auftragsverarbeiter; Subprozessoren-Liste mitführen.
- **Datenresidenz EU:** verfügbar für berechtigte API-Projekte mit Genehmigung — nicht Default.
- **Zero Data Retention (ZDR) / Modified Abuse Monitoring (MAM):** auf Antrag, keine Pflicht. Empfehlenswert für sensible Prompts (Gesundheit, juristisch, HR).
- **Strenge EU-Variante:** Azure OpenAI in `westeurope` oder `germanywestcentral` → Daten verbleiben in EU, MS-DPA gilt.

## Anthropic

- **Status:** DPF-zertifiziert (Stand zur Live-Prüfung empfohlen).
- **Vertragspartner:** Anthropic, PBC (US).
- **Strenge EU-Variante:** AWS Bedrock mit Claude in EU-Region (`eu-central-1`) — AWS-DPA gilt, Datenflüsse über AWS-Infrastruktur.
- **Pflichten:** DPA + DPF-Klausel; bei Enterprise-Tiers ZDR/erweiterte Datenkontrollen prüfen.

## Stripe

- **Hybride Rolle — entscheidend für korrekte AVV/SCC-Architektur:**
  - **Verantwortlicher (Controller):** für Kernzahlungsabwicklung, Fraud-Prävention (Stripe Radar), regulatorische Pflichten (KYC/AML), Netzwerk-Compliance. Hier ist KEIN AVV nötig — stattdessen **Controller-zu-Controller-Beziehung** mit klarer Aufgabentrennung in der Datenschutzerklärung.
  - **Auftragsverarbeiter (Processor):** für Zusatzdienste, bei denen Stripe weisungsgebunden Kundendaten verarbeitet (z.B. Stripe Billing für eigene Kundenliste, Identity bei kundeninitiiertem KYC, Custom-Metadata-Speicherung). Hier ist **AVV nach Art. 28 DSGVO** erforderlich.
- **Vertragspartner für EWR:** Stripe Payments Europe Limited (SPEL, Dublin).
- **US-Datenfluss:** Stripe Payments Europe Ltd. übermittelt an Stripe, Inc. (US); Stripe Inc. ist DPF-Teilnehmer.
- **VVT:** beide Rollen separat dokumentieren, Subprozessoren-Liste pflegen.

## Supabase

- **⚠ NICHT DPF-zertifiziert** (Stand Mai 2026, gemäß Supabase Trust Center).
- **Rechtsgrundlage für Transfer:** SCCs Modul 2 + DPA — keine Adäquanz-Basis.
- **Konzernsitz:** Delaware C-Corp (USA) → US CLOUD Act greift unabhängig von Project-Region (siehe `CLOUD-ACT.md`).
- **Project-Region:** EU wählen (Frankfurt `eu-central-1` oder Irland `eu-west-1`).
- **Datenflüsse einzeln prüfen:**
  - Auth (Postgres + GoTrue): in Project-Region
  - Edge Functions (Deno): laufen weltweit, nicht zwingend EU
  - Logs / Analytics: zentral
  - Backups: Region prüfen
  - Support: 24/7-Zugriff aus Drittländern möglich
- **Alternative:** Self-hosting in EU (Hetzner, IONOS) entzieht Cloud Act + bringt volle Kontrolle.

## Clerk

- **DPF-Status:** **AKTIV zertifiziert seit 22.2.2024** für **Non-HR Data**. (Quelle: Clerk Legal DPF Notice)
- **Vertragspartner:** Clerk, Inc. (US).
- **Pflichten:** DPA mit DPF-Klausel; jährliche Re-Zertifizierung-Status checken.
- **EU-Datenresidenz:** auf Anfrage prüfen, nicht Default.

## Auth0 (Okta)

- **Status:** DPF-zertifiziert.
- **EU-Tenant:** explizit beim Anlegen wählen — Domain dann `*.eu.auth0.com`. Nachträglicher Wechsel ist **nicht** möglich.
- **Custom Domain:** möglich, ändert aber nicht den Daten-Routing.
- **Universal Login Page:** wird über Auth0-CDN ausgeliefert — IP-Verarbeitung global.

## Sentry

- **EU-Hosting:** ja, Sentry Software GmbH (Wien) als EU-Vertragspartner.
- **EU-DSN-Format:** `https://<key>@o<id>.ingest.de.sentry.io/<project>` — Daten in Frankfurt.
- **Wichtige Konfiguration:**
  - `sendDefaultPii: false` SDK-seitig
  - `beforeSend`-Hook: User-IP, E-Mail, Cookies, Headers, Query-Strings entfernen
  - Server-seitige Data-Scrubbing-Regeln im Sentry-Projekt aktivieren (Project Settings → Security & Privacy → Data Scrubbing)
- **Nicht ausreichend:** US-DSN mit „nur IP gelöscht" — Stack Traces, Breadcrumbs, Request-Bodies können personenbezogen sein.

## Datadog

- **EU-Site:** `datadoghq.eu` (Paris).
- **Konfiguration:** Site explizit in API-Client UND Agent-Config setzen — kein Default-Failover.
- **Logs Sensitive Data Scanner** für In-Place-Maskierung von PII aktivieren.
- **APM Trace-Daten** können Request-Pfade mit User-IDs enthalten — Sampling/Scrubbing prüfen.

## Mailgun

- **EU-Region:** `api.eu.mailgun.net` (Frankfurt).
- **DPF-Status:** zertifiziert.
- **Wichtig:** Domain muss bei Anlage der EU-Region zugeordnet werden — nachträglicher Wechsel zwingt zu DNS-Änderung.

## EU-Anbieter (Hetzner, IONOS, Scaleway, OVHcloud EU)

- **Status:** EU/EEA-Sitz und -Hosting → kein Drittlandtransfer im Hauptverhältnis.
- **Pflichten:** AVV nach Art. 28 DSGVO.
- **Trotzdem prüfen:**
  - Sub-Auftragsverarbeiter-Liste (manche nutzen US-Komponenten für Monitoring, Mail-Versand, Anti-DDoS)
  - Support-Standorte
  - Konzernzugehörigkeit (z.B. OVHcloud hat globale Präsenz)
- Bei reiner EU-Lieferkette: einfachster Compliance-Pfad.

## Anbieter-Verifikations-Checkliste (Vorlage VVT-Anhang)

```
Anbieter:                   __________________
Juristische Entity:         __________________
Land:                       __________________
Rolle:                      [ ] Controller  [ ] Processor  [ ] Joint Controller
DPF-Participant-ID:         __________________
DPF-Status (Active/...):    __________________
Datenkategorie:             [ ] Non-HR Data  [ ] HR Data
Datum der Live-Prüfung:     __________________
SCC-Modul (falls Art. 46):  __________________
TIA-Datum:                  __________________
Subprozessoren-Liste:       __________________
Support-Region(en):         __________________
Backup-Region(en):          __________________
Notizen:                    __________________
```
