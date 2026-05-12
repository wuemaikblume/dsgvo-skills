# Roadmap & Working Notes — dsgvo-skills

> Diese Datei ist gleichzeitig öffentliche Roadmap UND **Onboarding-Doku für die nächste Arbeits-Session**. Sie enthält genug Kontext, dass weder ein neuer Claude-Lauf noch ein menschlicher Mitwirkender eine separate Erklärung braucht.

**Stand:** 2026-05-12, **Version 1.8 live**. v1.8 erweitert `dsgvo-email-marketing` um vier Cross-Review-Themen (WhatsApp/Messenger-Marketing, Drittland-Outbound, Kinder-Newsletter risikobasiert, Soft-Bounce als DSGVO-Pattern). Externer Tiefenrecherche-Review (Grok + GPT-5.5 + Gemini Deep Research) gegen Primärquellen (EUR-Lex, AI Act Service Desk, ICO, FTC, CRTC, ACMA, BGH/OLG-Aktenzeichen) eingearbeitet. Drei Skills live über alle drei Tool-Targets (Claude / Cursor / Codex+Copilot).

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
| v1.8 | (siehe Tag) | `dsgvo-email-marketing` Patch-Update mit vier Erweiterungen aus dem Cross-Review v1.6 — Issues #1/#3/#4/#5 gebündelt. **WhatsApp/Messenger-Marketing** in `UWG-7.md` (OLG Hamm 18 U 154/22 erfasst WhatsApp; Channel-Follow nur für Channel-Updates; App vs. API über BSP; Bulk-Adressbuch-Upload-Verstöße über Art. 5 I b/c + Art. 6 + Art. 13/14 + Meta-Business-Policy). **Drittland-Outbound** in `UWG-7.md` (Art. 49 ist NICHT der Anker bei Privatempfänger; EDPB 05/2021; CAN-SPAM Opt-out 15 USC § 7701; UK PECR Reg. 22 individual vs. corporate; CASL express/implied 1 Mio./10 Mio. CAD; Spam Act AU express/inferred 5 Werktage Opt-Out). **Kinder-Newsletter risikobasiert** in `CONSENT-AND-DOI.md` (Art. 8 Abs. 2 „angemessene Anstrengungen", EDPB 05/2020 lässt für Low-Risk Geburtsjahr+Eltern-Mail zu; 5-stufige Pattern-Hierarchie nach Risiko; DE 16/AT 14/CH ungeregelt; Plausibilitäts-Check + Token-TTL + Throttling Pflicht in jeder Stufe). **Soft-Bounce als DSGVO-Anknüpfung** in `UNSUBSCRIBE-AND-RETENTION.md` (Art. 5 I d/e + Abs. 2, keine fixe Behörden-Schwelle, dokumentierte interne Policy mit Versionsnummer, Audit-Log mit `policy_version`/`consent_record_ref`/`manual_override`-Feldern). SKILL.md Decision Tree um Schritte 8–11 erweitert. **Tiefenrecherche-Review durchgeführt 2026-05-12** (Grok + GPT-5.5/ChatGPT + Gemini Deep Research) mit Primärquellen-Verifikation gegen EUR-Lex, AI Act Service Desk, ICO, FTC, CRTC, ACMA, OLG-Aktenzeichen; wesentliche Korrekturen eingearbeitet (Wettbewerbszentrale-Halluzination raus, „5/14-Tage" als Policy nicht als Behörden-Konsens, Kinder-Verifikation umkehrt zu risikobasiert statt apodiktisch, CASL implied + express statt nur express). Cursor + Codex + Copilot synchron. |
| v1.7 (released 2026-05-12) | Branch `feat/ai-act-art50-email-marketing` (gemerged) | `dsgvo-email-marketing` neue Sub-Datei `AI-CONTENT-AND-TRANSPARENCY.md` für EU AI Act Art. 50 (Stichtag 02.08.2026, Sanktionen bei Art. 50-Verstoß **bis € 15 Mio. / 3 % nach Art. 99 Abs. 4 lit. g**). Differenzierung Anbieter-Pflichten (Abs. 1 Interaktions-Transparenz EG 132, Abs. 2 maschinenlesbare Markierung EG 133) vs. Betreiber-Pflichten (Abs. 4 Satz 1 Deepfake i. S. d. Art. 3 Nr. 60 EG 134, Abs. 4 Satz 2 Text bei „Angelegenheiten von öffentlichem Interesse" mit strikter Editorial-Control-Ausnahme, Werke-Ausnahme statt „offensichtlich künstlich"-Generalausnahme). Klarstellung: für die Mehrzahl klassischer Werbe-Newsletter greift NICHT die Art. 50-Markierungspflicht — Schwerpunkt liegt auf DSGVO Art. 13/22/25/28 + Kap. V (LLM-API-Drittland), DSFA-Schwelle (DSK-Blacklist Nr. 11) plus eigenständige **UWG-Schiene** (§ 3a / § 5 / § 5a / Schwarze Liste — Mitbewerber-Abmahnvektor). Code-Patterns für Subject-Line-Pseudonymisierung, AI-Footer, AI-Chatbot-Disclosure inkl. Offensichtlichkeits-Ausnahme bei `chatbot@…`-Adressen, AI-Bild-Caption mit klarer Trennung Deepfake (Art. 3 Nr. 60) vs. UWG-Testimonial-Risiko. Cursor (`dsgvo-ai-content-marketing.mdc`) + Codex AGENTS.md + Copilot copilot-instructions.md synchron erweitert + korrigiert. SKILL-BOUNDARIES.md ergänzt. **Tiefenrecherche-Review durchgeführt 2026-05-12** (Grok + GPT-5.5 + Gemini Deep Research; Primärquellen gegen AI Act Service Desk verifiziert). Adressiert Issue #2 von 5 v1.7-Issues; Issues #1, #3, #4, #5 (WhatsApp, B2B-Drittland-Empfänger, Kinder-Newsletter, Soft-Bounce) folgen separat. |

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

### v1.7 Tiefenrecherche-Review (durchgeführt 2026-05-12)

Drei voneinander unabhängige Tiefenrecherchen (Grok + GPT-5.5/ChatGPT + Gemini Deep Research) zur AI-Act-Art-50-Sektion. Primärquellen-Verifikation gegen AI Act Service Desk der EU-Kommission und artificialintelligenceact.eu (Erwägungsgründe 132/133/134).

**Eingearbeitete Korrekturen (Commit auf Branch `feat/ai-act-art50-email-marketing`):**

- Sanktionsnorm: **Art. 99 Abs. 4 lit. g** (€ 15 Mio. / 3 %) statt fälschlich lit. c (€ 7,5 Mio. / 1,5 %) — lit. g listet explizit Art. 50; lit. c betrifft Verstöße gegen Anbieter-Pflichten aus Art. 16
- Erwägungsgrund-Zuordnung: **EG 132** (Interaktions-Transparenz Abs. 1) / **EG 133** (Anbieter-Markierung Abs. 2) / **EG 134** (Betreiber-Disclosure Abs. 4)
- Werke-Ausnahme statt vager „offensichtlich künstlich"-Ausnahme: Abs. 4 enthält **keine** separate Generalausnahme für offensichtlich künstliche Inhalte. Die einschlägige Ausnahme reduziert die Pflicht bei „künstlerischem, kreativem, satirischem, fiktionalem oder analogem Werk" auf eine „werkschonende" Offenlegung
- Deepfake-Definition (Art. 3 Nr. 60) strikt am Wortlaut: Bezug zu **wirklichen** Personen/Objekten/Orten/Einrichtungen/Ereignissen + Echtheits-Eindruck. Fiktiv-fotorealistische „Kunden"-Testimonials erfüllen den engen Wortlaut nicht zwingend
- **Neue Sektion UWG-Schiene:** § 3a UWG (Marktverhaltensregelung), § 5 / § 5a UWG (Irreführung), Schwarze Liste (Fake-Testimonials per se unlauter). Mitbewerber- und Verbraucherschutzverbands-Abmahnvektor unabhängig von Aufsicht
- Editorial-Control-Ausnahme strikt: namentlich benannte Verantwortung + dokumentierte manuelle Vorab-Prüfung. Reines Template-Approval und reine Stichprobenkontrolle reichen nicht
- Pseudonymisierung risikoadjustiert (Art. 25/32 DSGVO), nicht als ausnahmslose Pflicht formuliert
- DSFA-Schwelle: keine starre 100k-Empfänger-Zahl mehr; DSK-Blacklist Nr. 11 (KI-Profiling) als zwingender Auslöser
- Code of Practice präziser: 1. Entwurf 17.12.2025, 2. Entwurf 05.03.2026, Final Juni 2026 erwartet. Multi-Layer-Ansatz (C2PA + Wasserzeichen + Fingerprinting + EU-Piktogramm). Compliance-Demonstration für Signatories statt klassischer Konformitätsvermutung
- Entwurfsleitlinien der Kommission zu Art. 50 in Konsultation seit 08.05.2026 ergänzt
- Art. 2 Abs. 7 AI Act (DSGVO bleibt unberührt) als Klammer eingefügt
- Chatbot-Pattern: Offensichtlichkeits-Ausnahme bei `chatbot@…`/`ai@…`-Sender-Adresse ergänzt
- BfDI-Handreichung korrekt zitiert: „KI in Behörden — Datenschutz von Anfang an mitdenken", 22.12.2025
- DSK-Quellen entwirrt: „KI und Datenschutz" 06.05.2024 / „TOMs bei KI-Systemen" Juni 2025 / „RAG-Systeme" Oktober 2025

Spiegelung auf `cursor/rules/dsgvo-ai-content-marketing.mdc`, `codex/AGENTS.md`, `codex/copilot-instructions.md`, `claude/skills/dsgvo-email-marketing/SKILL.md` durchgeführt.

**Verbleibende offene Punkte (Auslegung in Bewegung, dokumentiert in `AI-CONTENT-AND-TRANSPARENCY.md`):**

- [ ] Finale Kommissions-Leitlinien zu Art. 50 (Konsultation 08.05.2026, Endfassung steht aus) — insb. Auslegung „matters of public interest" und Editorial-Control-Schwelle
- [ ] Code of Practice Final-Fassung Juni 2026 — endgültige technische Marker-Standards
- [ ] DACH-Rechtsprechung speziell zu AI-Testimonial-Bildern in Werbemails (keine OLG-Entscheidung mit alleinigem AI-Bezug)
- [ ] Genaue quantitative DSFA-Schwelle bei skalierter AI-Personalisierung
- [ ] BSI-Empfehlungen zu C2PA und Wasserzeichen-Standards (Stand Mai 2026 nichts Spezifisches)

**Merge-Vorbereitung:** Mit der Korrektur-Integration ist die Pre-Tag-Pflicht aus dem v1.5-Argon2id-Vorfall erfüllt. Nächster Schritt: User-Approval, dann `gh pr merge 6 --squash` + `git tag v1.7 && git push --tags`.

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
