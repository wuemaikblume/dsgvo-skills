# Roadmap & Working Notes — dsgvo-skills

> Diese Datei ist gleichzeitig öffentliche Roadmap UND **Onboarding-Doku für die nächste Arbeits-Session**. Sie enthält genug Kontext, dass weder ein neuer Claude-Lauf noch ein menschlicher Mitwirkender eine separate Erklärung braucht.

**Stand:** 2026-05-08, Version 1.6 live, **v1.7 in Vorbereitung auf Branch `feat/ai-act-art50-email-marketing`**. v1.7 enthält neue Sub-Datei `AI-CONTENT-AND-TRANSPARENCY.md` im `dsgvo-email-marketing`-Skill — Antwort auf den am 02.08.2026 unmittelbar anwendbaren EU AI Act Art. 50 (Sanktionen bis € 7,5 Mio. / 1,5 % weltweiter Konzern-Jahresumsatz nach Art. 99 Abs. 4 lit. c). **Pre-Tag PFLICHT:** externe Tiefenrecherche-Review (Perplexity/Gemini Deep Research) der Faktenclaims, insbesondere zur DACH-Aufsichts-Position 2026, vor `git tag v1.7`. Vorgängiger Stand v1.6: drei Skills live über alle drei Tool-Targets (Claude / Cursor / Codex+Copilot).

---

## 1. Projekt-Snapshot

### Was es ist

Kostenlose Open-Source-Skills für KI-Coding-Tools (Claude AI, Claude Code, später Cursor und Codex), die DACH-Entwicklern automatisch DSGVO-Wissen mitgeben. Erster Skill: `dsgvo-third-country-transfer` (DSGVO Kapitel V, Art. 44-49 + Cloud Act + ePrivacy + Schweiz + Anbieter-Profile).

### Live-URLs

| Wo | URL |
|----|-----|
| GitHub-Repo | <https://github.com/wuemaikblume/dsgvo-skills> |
| Default-Branch | `main` |
| Long-Form-Artikel | <https://maikblume.de/blog/warum-ich-einen-dsgvo-skill-fuer-claude-gebaut-habe> |
| News-Artikel | <https://chilikanal.de/artikel/dsgvo-skill-fuer-claude-bringt-eu-compliance-ins-ki-coding> |

### Skill-Architektur

```
claude/skills/dsgvo-third-country-transfer/
├── SKILL.md           # 2.300 Wörter, immer geladen wenn getriggert
├── PROVIDERS.md       # Detail-Profile zu 16 Anbietern
├── ROLES.md           # Art. 6/9/26/28 — Controller / Processor / Joint
├── DPIA.md            # Art. 35 — Datenschutz-Folgenabschätzung
├── CLOUD-ACT.md       # Art. 48 / Cloud Act / BCRs / Datensouveränität
├── CH-REVDSG.md       # Schweiz revDSG, Swiss-US DPF, EDÖB
└── EPRIVACY.md        # TTDSG §25, Cookies, Tracking-Pixel
```

Sub-Dateien laden via Anthropic-Progressive-Disclosure-Pattern nur on-demand.

### Multi-Tool-Struktur (Repo)

```
dsgvo-skills/
├── claude/      # Anthropic Skills (Hauptdatei + 6 Sub-Files)
├── cursor/      # 6 .cursor/rules/*.mdc (Agent Requested)
├── codex/       # AGENTS.md (Codex CLI) + copilot-instructions.md
├── README.md
├── DISCLAIMER.md
├── LICENSE      # MIT (deutsche Übersetzung)
├── ROADMAP.md   # diese Datei
└── .gitignore
```

---

## 2. Was bisher passiert ist (Versions-Historie)

| Version | Commit | Was |
|---------|--------|-----|
| v1.0 | `944797b` | Initialer Prototyp — ein Skill, ein File |
| v1.1 | `275f633` | Multi-Tool-Restrukturierung + drei Reviews integriert (Brasilien, Supabase, Clerk, OpenAI Ireland, Firebase, Stripe, AWS eu-west-2 = UK, Sentry-Scrubbing, Anonymisierung-Präzisierung). Sub-Datei-Architektur eingeführt. |
| v1.1+docs | `0307cee` | `<REPO>`-Platzhalter ersetzt durch `wuemaikblume` |
| v1.2 | `42be843` | Cloud-Act-Trigger geschärft + neue „Code-Generierungs-Regel" für explizite Drittland-Wünsche |
| v1.3 | `ccc453f` | Description-Härtung für Sonnet-4.6-Trigger: Avatar/Upload/Signup explizit, „any user data = personal data unter GDPR"-Klausel, Art. 9-Trigger-Liste, § 203 StGB + revDSG im Scope |
| v1.4 | (siehe Tag) | Faktenkorrektur DPF-Status (verifiziert gegen offizielle ITA-Excel-Liste 2026-05-04): Anthropic und OpenAI sind NICHT DPF-zertifiziert — Transfermechanismus auf SCCs Modul 2 (Anthropic) bzw. Modul 3 (OpenAI via Ireland) korrigiert. Clerk-Detail erweitert (Participant ID 2718, Status „Active - Re-certification under Review", Re-Cert-Fenster bis 27.2.2026). Stichtag mit Verifikations-Quelle aktualisiert. Korrekturen über Claude / Cursor / Codex / Copilot synchron. |
| v1.5 | (siehe Tag) | `dsgvo-auth-and-logging` v1.0 — vier Sub-Files (SKILL/LOGGING/AUTH-TOM/IP-ADDRESSES). Drei Cross-Reviews (Sonnet intern, GPT-5.5 extern, Tiefenrecherche extern) eingearbeitet. Trigger-Tests Opus 6/6 PASS, Sonnet 4.6 3/6 PASS — Modell-Hinweis in README+INSTALL ergänzt (Sonnet erfordert ggf. expliziten Skill-Aufruf bei Setup-Fragen). Cursor + Codex/Copilot synchron mitversioniert. |
| v1.6 | (siehe Tag) | `dsgvo-email-marketing` v1.0 — fünf Sub-Files (SKILL/CONSENT-AND-DOI/UWG-7/TRACKING-IN-MAIL/UNSUBSCRIBE-AND-RETENTION/SERVICE-VS-MARKETING). UWG § 7 (DE) + AT TKG § 174 + CH UWG Art. 3 I o + DSGVO Art. 6/7/21 + TDDDG § 25. BGH-Linie zur Service-vs-Werbung-Abgrenzung (I ZR 218/07, VI ZR 225/17, I ZR 164/09, OLG Hamm 4 U 121/13). DPF-Status der vier US-Newsletter-Provider live verifiziert (Mailchimp 7693, Klaviyo 6149, HubSpot 5812, ActiveCampaign 4495 — alle Active). PROVIDERS.md um 8 Profile erweitert (4 US + 4 EU). Cursor + Codex/Copilot synchron. |
| v1.7 (in Vorbereitung) | Branch `feat/ai-act-art50-email-marketing` | `dsgvo-email-marketing` neue Sub-Datei `AI-CONTENT-AND-TRANSPARENCY.md` für EU AI Act Art. 50 (Stichtag 02.08.2026, Sanktionen bis € 7,5 Mio. / 1,5 % weltweiter Konzern-Jahresumsatz). Differenzierung Provider-Pflichten (Abs. 1 Interaktions-Transparenz, Abs. 2 maschinenlesbare Output-Markierung) vs. Deployer-Pflichten (Abs. 4 Deepfake Bild/Audio/Video, Abs. 4 Text mit „matters of public interest"-Schwelle und Editorial-Control-Ausnahme). Klarstellung: für 90 % der klassischen Werbe-Newsletter greift NICHT die Art. 50-Markierungspflicht — Schwerpunkt liegt auf DSGVO Art. 13 (Privacy Notice für AI-Personalisierung), Art. 28 + Kap. V (DPA + DPF/SCCs für LLM-API), Art. 22 (automatisierte Entscheidung Edge-Case), Art. 35 + EDPB WP 248 (DSFA bei AI-verstärktem Profiling). Code-Patterns für Subject-Line-Pseudonymisierung, AI-Footer, AI-Chatbot-Disclosure, Deepfake-Bild-Caption. Cursor (`dsgvo-ai-content-marketing.mdc`) + Codex AGENTS.md + Copilot copilot-instructions.md synchron erweitert. SKILL-BOUNDARIES.md ergänzt. **PRE-TAG-PFLICHT:** externer Tiefenrecherche-Review der Faktenclaims (insbesondere DACH-Aufsichts-Position 2026, finaler Code of Practice Juni 2026, Sanktions-Höhe Art. 99). Adressiert Issue #2 von 5 v1.7-Issues; Issues #1, #3, #4, #5 (WhatsApp, B2B-Drittland-Empfänger, Kinder-Newsletter, Soft-Bounce) folgen separat. |

### Drei Cross-Reviews (alle Befunde im Repo eingearbeitet)

1. **Sonnet-Subagent intern** (mit WebSearch-Zugriff) — fand CNIL-URL-Datum-Inkonsistenz, Latombe-Quelle fehlt inline, Anthropic/Clerk DPF nicht live verifiziert
2. **GPT/Gemini Standard-Cross-Review** (extern) — fand Stripe Controller/Processor-Hybrid, Schweizer revDSG fehlt komplett, Art. 26/28/35/48 fehlen
3. **Tiefenrecherche** (extern, mit Quellen-Crawling) — fand **Brasilien-Adäquanzbeschluss 10.02.2026**, **Supabase nicht DPF**, **Clerk DPF aktiv seit 22.02.2024**, **OpenAI Ireland Ltd. als Vertragspartner**, **Firebase Cloud Functions Default `us-central1`**, **AWS eu-west-2 = UK (nicht EU)**

### Trigger-Tests (in echter Claude-Code-Session)

| Test | Prompt-Kern | v1.1 | v1.3 |
|------|-------------|------|------|
| 1 | „Node-Skript S3-Upload us-east-1" | Skill triggert, aber nur Hinweis vorab — Code rein us-east-1 | ✅ Opus + Sonnet 4.6: beide Varianten generiert, DPF-Pflichten als Bullets |
| 2 | „Sentry für Next.js" | ✅ EU-DSN, `sendDefaultPii: false`, `beforeSend`, Replay-Masking | (nicht erneut nötig) |
| 3 | „OpenAI mit Patient-Allergie-Tickets" | Art. 9 erkannt, DSFA + Pseudonymisierung, **aber Cloud Act fehlte** | ✅ Opus: Cloud Act + § 203 StGB führend, vier Architektur-Optionen, Refusal ohne DSFA |
| 4 | „DigitalOcean Frankfurt → Hetzner Falkenstein" | ✅ Skill nicht überschießend: 4 DSGVO-Bullets + Migrations-Beratung | (nicht erneut nötig) |

---

## 3. Offene Punkte (priorisiert + handlungsfertig)

### Externer Re-Review v1.3 (optional)

- [ ] Den v1.3-Skill nochmal durch **GPT-5.5** und **Gemini** schicken. Zweck: Verifikation, dass Brasilien-Adäquanz, Supabase-Korrektur, Clerk-DPF-Update, OpenAI-Ireland-Klärung und die neue „any user data = personal data"-Klausel sauber sind.
- [ ] Bei neuen Befunden: in einem v1.4-Patch einarbeiten.
- [ ] **Achtung:** Beim Verfassen des externen Cross-Check-Prompts den SKILL.md-Body **nicht versehentlich doppelt einfügen** (passiert leicht — beide externen KIs flaggen sonst „doppelte Tabelle", was am Prompt liegt, nicht am Skill).

### Cursor-Adaption ✅ (v1.3, Commit `d122638`)

Sechs `.mdc`-Rules in `cursor/rules/`, alle Agent-Requested (`alwaysApply: false`), Description aus v1.3 übernommen:

- `dsgvo-third-country-transfer.mdc` (konsolidiert SKILL + PROVIDERS, ~30 KB)
- `dsgvo-roles.mdc`, `dsgvo-dpia.mdc`, `dsgvo-cloud-sovereignty.mdc`, `dsgvo-eprivacy-tracking.mdc`, `dsgvo-revdsg-ch.mdc`

Plus `cursor/INSTALL.md` (Agent-Requested-Erklärung, Verifikations-Prompt, Update-Befehle) und aktualisierte `cursor/README.md`.

**Community-Tasks** (Maintainer hat kein Cursor — Praxisbericht via Issue/PR willkommen):
- Re-Test 1 (S3-Upload us-east-1) und Re-Test 3 (OpenAI + Patient-Allergie) in echtem Cursor-Projekt fahren, Trigger-Verhalten dokumentieren
- Falls Cursor schwächer triggert: Description schärfen oder `globs:` mit File-Pattern-Triggern füllen (z.B. `globs: ["**/*.tf", "**/aws-*.{js,ts}"]`)

### OpenAI Codex / GitHub Copilot Adaption ✅ (v1.3)

- `codex/AGENTS.md` (~250 Zeilen) — Decision Tree, Anbieter-Quick-Ref, Code-Patterns, Code-Generation-Regel, CLOUD-Act-Branch, Häufige Fallen, VVT-Pflicht, Quellen
- `codex/copilot-instructions.md` — identischer Inhalt, Header zeigt auf Copilot
- `codex/INSTALL.md` (Codex + Copilot Setup, Verifikations-Prompt, Tag-Pinning)
- `codex/README.md` aktualisiert (Status v1.3)

**Community-Tasks** (Maintainer hat weder Codex CLI noch Copilot — Praxisbericht via Issue/PR willkommen):
- In echtem Codex-CLI-Projekt installieren und Re-Test 1 + 3 fahren — verifizieren, ob Codex die Code-Generation-Regel (zwei Varianten) zuverlässig umsetzt
- In Copilot-fähigem Repo installieren und in VS Code mit `@workspace` testen
- Falls Codex/Copilot schwächer triggern: AGENTS.md schärfen oder schon vorne mit explizitem `## CRITICAL`-Block beginnen, der bei Drittland-Code immer durchschlagen soll

### Weitere Skill-Familien

Alle nach demselben Schema: Prototyp → drei Reviews (Sonnet + extern × 2) → Korrekturen → Tests → Veröffentlichung.

**Vor jedem neuen Skill verbindlich:** [SKILL-BOUNDARIES.md](SKILL-BOUNDARIES.md) abgleichen, damit kein Thema doppelt gehört wird und keines verloren geht.

- [x] **`dsgvo-auth-and-logging`** ✅ live (v1.5) — Audit-Logs, IP-Speicherung, Login-Tracking, Zweckbindung, Speicherbegrenzung, Pseudonymisierung in Logs. Vier Sub-Files: `SKILL.md`, `LOGGING.md`, `AUTH-TOM.md`, `IP-ADDRESSES.md`. Cursor + Codex/Copilot synchron.
- [ ] **`dsgvo-subject-rights`** — Auskunft (Art. 15), Berichtigung (16), Löschung (17), Einschränkung (18), Portabilität (20), Widerspruch (21). Trigger: User-Account-Features, „Konto löschen"-Endpoints, Datenexport-APIs, Auskunfts-Tickets.
- [ ] **`dsgvo-personal-data-storage`** — Verschlüsselung at-rest und in-transit, Aufbewahrungsfristen pro Datenkategorie, Backups + Löschkonzept, Pseudonymisierung-Patterns. Trigger: DB-Schema, S3-Bucket-Konfig, Encryption-Settings, Backup-Strategien.
- [ ] **`nis2-security-baseline`** — NIS2-Umsetzungsgesetz Deutschland (Stand prüfen), Mindestmaßnahmen Art. 21, 24/72h-Meldepflichten, Risikomanagement, Incident-Response. Trigger: Security-relevante Architektur, Auth-Hardening, Backup-Strategien, KRITIS-Sektor-Hinweise.
- [ ] **`dsgvo-employment`** — § 26 BDSG (Beschäftigtendatenschutz), Bewerber-Daten, Performance-Tracking, HR-Tools (Personio, BambooHR, etc.), Krankenakten. Trigger: HR-System-Integrationen, Performance-Monitoring, Bewerber-Tools.
- [x] **`dsgvo-email-marketing`** ✅ live (v1.6) — UWG §7 + AT TKG § 174 + CH UWG Art. 3 I o + DSGVO Art. 6/7/21 + TDDDG § 25. Fünf Sub-Files: `SKILL.md`, `CONSENT-AND-DOI.md`, `UWG-7.md`, `TRACKING-IN-MAIL.md`, `UNSUBSCRIBE-AND-RETENTION.md`, `SERVICE-VS-MARKETING.md`. Trigger: Newsletter-Provider-SDKs (Mailchimp/Klaviyo/HubSpot/Brevo/CleverReach/Rapidmail/MailerLite/ActiveCampaign), DOI-Endpoints, Mail-Tracking-Pixel, List-Unsubscribe-Header (RFC 8058), Service-vs-Werbung-Abgrenzung. Cursor + Codex/Copilot synchron.

**Empfohlene Reihenfolge:** Auth-Logging zuerst (am meisten Synergie zum Drittlandtransfer-Skill), dann Subject-Rights, dann NIS2, dann Marketing/Employment.

### Anbieter-Updates pflegen (laufend)

- [ ] **DPF-Status quartalsweise auf <https://www.dataprivacyframework.gov/list> live prüfen** — speziell: Anthropic, Clerk, Supabase, OpenAI, AWS, Microsoft, Google
- [ ] **Schrems-III-Status** verfolgen — Latombe-Berufung, neue noyb-Klagen, EuGH-Verfahren
- [ ] **EuGH-Rechtsprechung** zu Anonymisierung, Cookies, IP-Adressen
- [ ] **DSK-Beschlüsse** auf <https://www.bfdi.bund.de/DE/Fachthemen/Gremienarbeit/Datenschutzkonferenz/DSK-tableBeschl%C3%BCsse.html>
- [ ] **NIS2-UmsuCG**-Stand in DE
- [ ] Wenn Befund relevant: einen v1.x-Patch im Skill machen + Stichtag aktualisieren

### Issues-Templates und Contributor-Doku

- [ ] `.github/ISSUE_TEMPLATE/bug-report.md` — typisch: „Skill triggert nicht / falsch / zu oft"
- [ ] `.github/ISSUE_TEMPLATE/skill-suggestion.md` — Vorschlag für neue Skill-Familie
- [ ] `.github/ISSUE_TEMPLATE/provider-update.md` — Anbieter-DPF-Status hat sich geändert (Vorlage mit Pflichtfeldern: Anbieter, neuer Status, Quelle, Datum)
- [ ] `CONTRIBUTING.md` — Hinweise auf Cross-Review-Pflicht, Quellen-Pflicht, Stichtag in jedem Skill, Disclaimer-Pflicht

### Anthropic-DPF-Status live verifizieren

Der Skill schreibt aktuell „Anthropic: DPF (zur Live-Prüfung empfohlen)". Vor jedem Cross-Review oder Release einmal auf `dataprivacyframework.gov/list` verifizieren und das Prüfdatum in `PROVIDERS.md` dokumentieren.

---

## 4. Konventionen für Mitwirkung

### Commit-Style

- `feat:` — neue Funktion / neuer Skill
- `fix:` — Korrektur faktischer Fehler
- `docs:` — Dokumentation
- `chore:` — Repo-Pflege

### Cross-Review-Disziplin (Pflicht für jeden neuen Skill)

1. Erster Wurf von einer Person/AI
2. **Drei** unabhängige Reviews:
   - Sonnet-Subagent intern (mit WebSearch)
   - Externe Standard-KI-Review (GPT, Gemini, Mistral)
   - Tiefenrecherche extern (Quellen-Crawling)
3. Schnittmenge der Befunde priorisiert einarbeiten
4. Trigger-Tests in echter Claude-Code-Session vor Veröffentlichung

### Quellen-Pflicht

- Jeder Skill hat einen `## Quellen`-Abschnitt mit URLs zu Primärquellen (Gesetzestexte, Aufsichtsbehörden, EuGH, EDPB, CNIL)
- Jeder rechtliche Befund hat **mindestens eine** zitierbare Quelle, die unmittelbar verlinkt ist
- Behauptungen über Anbieter-Status (DPF-Zertifizierung etc.) immer mit „Stand X, vor Vertragsabschluss live prüfen"-Hinweis

### Stichtag in jedem Skill

- Im `## Disclaimer`-Abschnitt: **„Stand Monat Jahr"** explizit nennen
- Bei Updates Stichtag aktualisieren
- Bei strukturellen Änderungen Versionierung im Commit-Subject (`v1.x`)

### Disclaimer-Pflicht

- Jeder Skill und das Repo enthalten klar: „Best-Practice-Sammlung, **keine Rechtsberatung**"
- Bei Art. 9-Daten / KRITIS / Aufsichtsanfragen: Hinweis auf Anwalt / DSB

---

## 5. Onboarding für Mitwirkende

Diese ROADMAP ist die Eintrittstür ins Projekt. Sie enthält:

- alle Live-URLs (Repo, Long-Form-Artikel, News)
- Versions-Historie und Inhalt der drei Cross-Reviews
- Test-Ergebnisse mit konkreten verbleibenden Lücken
- Detaillierte To-Dos pro Bereich (Re-Tests, Cursor, Codex, weitere Skill-Familien)
- Konventionen und Pflichten für jeden Skill-Beitrag (Cross-Review, Quellen, Stichtag, Disclaimer)

Damit ist sowohl ein menschlicher Mitwirkender als auch eine neue KI-Session arbeitsfähig, ohne separaten Kontext-Aufbau.
