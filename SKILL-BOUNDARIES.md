# Skill-Boundaries — dsgvo-skills

> Architektur-Referenz: welcher DSGVO-/NIS2-/UWG-Aspekt gehört in welchen Skill der Familie. Ziel: Doppelung vermeiden, nichts vergessen, Cross-Links sauber halten.
>
> **Verbindlich für jeden neuen Skill.** Vor dem ersten Wurf hier nachsehen, danach hier ergänzen.

## 1. Bundle-Annahme

Die `dsgvo-*`-Skills sind als **Familie** konzipiert. Maintainer- und Team-Setups installieren typischerweise alle gemeinsam. Daher gilt:

- Einzelne Skills dürfen in Sub-Files auf Sub-Files anderer Skills im selben Repo verweisen (z.B. `dsgvo-auth-and-logging/SKILL.md` linkt auf `dsgvo-third-country-transfer/ROLES.md`).
- Jeder Skill muss aber eigenständig **Trigger-Description** und **Inline-Mini-Decision-Tree** haben, damit er auch isoliert sinnvoll arbeitet, wenn jemand nur einen davon installiert.
- Querschnitts-Themen (Roles, DPIA, Cloud Act, Schweiz, ePrivacy, Anbieter-Profile) wohnen in genau einem Skill und werden von dort referenziert.

## 2. Querschnitts-Themen — Wohnsitz und Cross-Link-Punkt

| Thema | Wohnsitz | Cross-Link aus |
|---|---|---|
| Rollen (Art. 6 / 9 / 26 / 28) | `dsgvo-third-country-transfer/ROLES.md` | jedem Skill mit Verarbeitungs-Rollen |
| DPIA / DSFA (Art. 35) | `dsgvo-third-country-transfer/DPIA.md` | Art. 9, KRITIS, Drittland, Profiling, Auth-Logs für Beschäftigte |
| Cloud Act / Art. 48 | `dsgvo-third-country-transfer/CLOUD-ACT.md` | US-Anbieter, Konzernzugriff, Remote-Support |
| Schweiz revDSG / EDÖB | `dsgvo-third-country-transfer/CH-REVDSG.md` | DACH-Schweiz-Datenflüsse |
| ePrivacy / TTDSG § 25 | `dsgvo-third-country-transfer/EPRIVACY.md` | Cookies, Tracking-Pixel, Local Storage |
| Anbieter-Profile (16+) | `dsgvo-third-country-transfer/PROVIDERS.md` | konkreter Anbieter-Hinweis (Sentry, Auth0, Clerk, …) |

Wenn ein neuer Skill ein neues Querschnitts-Thema braucht, das nirgendwo passt: hier ergänzen, in ROADMAP.md verlinken, in einem Skill als Wohnsitz wählen.

## 3. Skill-Mapping (Themen → Skill)

| Thema / Daten-/Code-Aspekt | Wohnsitz | Anmerkung |
|---|---|---|
| Drittlandtransfer Art. 44–49, DPF, SCCs, TIA | `third-country-transfer` ✅ | Wohnsitz Cloud Act, ePrivacy, Schweiz, Roles, DPIA |
| Cloud-Region außerhalb EU, US-SaaS | `third-country-transfer` ✅ | |
| Cookies / Tracking-Consent / TTDSG § 25 | `third-country-transfer/EPRIVACY.md` ✅ | |
| Auth-Provider als US-Dienst (Auth0, Clerk, Firebase Auth) | `third-country-transfer/PROVIDERS.md` ✅ | Drittland-Aspekt dort; Auth-Code-Pattern in `auth-and-logging` |
| **IP-Adressen, Audit-Logs, Pseudonymisierung in Logs** | `auth-and-logging` ✅ | EuGH/BGH zu IP, Kürzung, Speicherdauer |
| **Login-Tracking, Session-Management, JWT-Lifecycle** | `auth-and-logging` ✅ | |
| **Passwort-Hashing, Cookie-Flags, MFA, Rate-Limiting (Art. 32 TOM)** | `auth-and-logging` ✅ | NIS2 verschärft nur für KRITIS, Cross-Link |
| **Application-Log-Scrubbing (Sentry/Datadog/Pino/Winston)** | `auth-and-logging` ✅ | tiefer Code-Pattern; `third-country` linkt nur grob auf Drittland-Aspekt |
| **Aufbewahrungsfristen Auth-/Sicherheits-Logs** | `auth-and-logging` ✅ | typisch 30/90 Tage |
| Auskunft (Art. 15), Berichtigung (16), Löschung (17), Einschränkung (18), Portabilität (20), Widerspruch (21) | `subject-rights` 📋 | Account-Löschung, Datenexport-API |
| Verschlüsselung at-rest / in-transit | `personal-data-storage` 📋 | DB, Backups, Disks, Keys |
| Aufbewahrungsfristen pro Datenkategorie (HGB, AO, TKG, BDSG) | `personal-data-storage` 📋 | nicht: Auth-Log-Fristen — die in `auth-and-logging` |
| Backup-Strategie + Löschkonzept | `personal-data-storage` 📋 | |
| Pseudonymisierungs-Patterns für Stammdaten | `personal-data-storage` 📋 | nicht: Logs — die in `auth-and-logging` |
| NIS2-Mindestmaßnahmen Art. 21 | `nis2-security-baseline` 📋 | KRITIS-spezifisch |
| Datenpanne-Meldepflicht (Art. 33 / 34 DSGVO + § 8b BSI) | `nis2-security-baseline` 📋 | jeder Breach, nicht auth-spezifisch |
| Risikomanagement, Incident-Response | `nis2-security-baseline` 📋 | |
| § 26 BDSG Beschäftigtendatenschutz | `employment` 📋 | |
| Bewerber-Daten, Performance-Tracking, HR-Tools | `employment` 📋 | |
| Beschäftigten-Login-Monitoring | `employment` 📋 | aber: Auth-Log-Mechanik in `auth-and-logging` |
| Krankenakten / Gesundheitsdaten am Arbeitsplatz | `employment` 📋 | |
| UWG § 7 Werbung, Double-Opt-In, B2B-Privileg | `email-marketing` ✅ | DOI-Flow + Bestandskunden + Cold-B2B + AT TKG + CH UWG |
| Tracking-Pixel in E-Mails | `email-marketing` ✅ | Open-Pixel + Click-Redirect + externe Bilder; Norm bleibt EPRIVACY.md |
| Werbung vs. Service-Mail-Abgrenzung | `email-marketing` ✅ | BGH-Linie I ZR 218/07, VI ZR 225/17, I ZR 164/09 |
| **EU AI Act Art. 50 für AI-personalisierten Newsletter-Content** | `email-marketing/AI-CONTENT-AND-TRANSPARENCY.md` ✅ | v1.7 (02.08.2026 Stichtag); LLM-Provider-Drittland-Status weiterhin in `third-country-transfer/PROVIDERS.md`, DSFA bei AI-verstärktem Profiling in `third-country-transfer/DPIA.md` |

✅ Live · 🔜 In Arbeit · 📋 Geplant

## 4. Doppel-Trigger-Vermeidung

Wenn zwei Skills bei demselben Code anschlagen können, gilt:

- **Drittland-Auth-Provider** (z.B. Auth0): `third-country` triggert auf den Anbieter-Aspekt, `auth-and-logging` auf den Code-Pattern. Beide Skills sollten so geschrieben sein, dass sie aneinander vorbeiarbeiten — nicht beide den vollen Decision Tree durchlaufen.
- **Sentry-Integration**: `third-country` zeigt EU-DSN + minimalen Scrubbing-Patch + Verweis. `auth-and-logging` zeigt den vollen Scrubbing-Code (Stack-Traces, Header, Body-Felder). Keine Doppelung der Anbieter-Pflichten.
- **Backup eines Auth-Logs**: `auth-and-logging` liefert Aufbewahrungsfrist + Pseudonymisierungs-Pflicht. `personal-data-storage` liefert Verschlüsselung + Löschkonzept. Cross-Link in beide Richtungen.
- **Newsletter-SDK-Calls** (`mailchimp.lists.addListMember`, `klaviyo.profile.subscribe` etc.): `email-marketing` triggert auf den Code-Pattern (DOI-Flow, Subscribe-Endpoint, Confirm-Mail). `third-country` triggert nur ergänzend bei Provider-Wahl-Diskussion (DPF-Status, AVV) — nicht bei jedem Call.
- **Tracking-Pixel in Mail-HTML**: `email-marketing/TRACKING-IN-MAIL.md` triggert (Code-Pattern). `third-country/EPRIVACY.md` bleibt Norm-Wohnsitz für TDDDG § 25 / Cookies / Local Storage außerhalb von Mail.
- **IP-Speicherung beim DOI-Confirm**: `auth-and-logging/IP-ADDRESSES.md` bleibt Wohnsitz; `email-marketing/CONSENT-AND-DOI.md` zitiert nur 1 Satz + Cross-Link.
- **Versand-Log eines Newsletters**: `auth-and-logging/LOGGING.md` regelt PII-Scrubbing-Format. `email-marketing/UNSUBSCRIBE-AND-RETENTION.md` regelt Aufbewahrungsfristen für Versand-Status. Cross-Link in beide Richtungen.

Faustregel: Wer den **Code-Pattern** liefert, gewinnt den Trigger. Der andere Skill liefert die **Datenebene** (was darf rein/raus, wie lange).

## 5. Pflicht für jeden neuen Skill

Vor dem ersten Wurf:

1. Diese Datei lesen
2. Geplanten Scope mit Tabelle in §3 abgleichen — Doppel? Lücke?
3. Sub-File-Liste: nur Themen aufnehmen, die nicht schon Wohnsitz in einem anderen Skill haben
4. Cross-Link-Punkte in §2 markieren, die der neue Skill nutzen wird
5. Wenn das Mapping unklar ist: hier eintragen / klären, **bevor** die SKILL.md geschrieben wird

Nach Veröffentlichung des Skills: Tabelle §3 aktualisieren (Status ✅), Sub-Files-Spalte ergänzen falls neuer Wohnsitz für ein Querschnitts-Thema.

## 6. Sub-File-Inventar (aktuell)

```
dsgvo-third-country-transfer/        ✅ live, v1.4
├── SKILL.md
├── PROVIDERS.md       (Wohnsitz: 16 Anbieter-Profile)
├── ROLES.md           (Wohnsitz: Art. 6/9/26/28)
├── DPIA.md            (Wohnsitz: Art. 35)
├── CLOUD-ACT.md       (Wohnsitz: Art. 48 / Cloud Act / BCRs)
├── CH-REVDSG.md       (Wohnsitz: Schweiz revDSG, EDÖB)
└── EPRIVACY.md        (Wohnsitz: TTDSG § 25, Cookies, Tracking-Pixel)

dsgvo-auth-and-logging/              ✅ live, v1.0
├── SKILL.md
├── LOGGING.md         (Wohnsitz: Log-Inhalte, Scrubbing, Retention für Auth-Logs)
├── AUTH-TOM.md        (Wohnsitz: Hashing, Sessions, Cookies, MFA, Rate-Limit — Art. 32)
└── IP-ADDRESSES.md    (Wohnsitz: IP als Personendatum, EuGH/BGH, Kürzung)

dsgvo-email-marketing/               ✅ live, v1.0
├── SKILL.md
├── CONSENT-AND-DOI.md            (Wohnsitz: Art. 6/7 für Marketing-Mail, DOI-Flow, Confirm-Mail-Inhalt)
├── UWG-7.md                      (Wohnsitz: UWG § 7 DE + AT TKG § 174 + CH UWG Art. 3 I o)
├── TRACKING-IN-MAIL.md           (Wohnsitz: Open-Pixel, Click-Redirect, externe Bilder als Code-Pattern)
├── UNSUBSCRIBE-AND-RETENTION.md  (Wohnsitz: One-Click, RFC 8058, Suppression-Hash, Aufbewahrungsfristen)
└── SERVICE-VS-MARKETING.md       (Wohnsitz: BGH-Linie Service vs Werbung, Mail-Template-Klassifikation)
```

Künftige Skills tragen ihre Sub-Files hier ein, sobald veröffentlicht.
