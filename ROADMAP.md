# Roadmap & Working Notes — dsgvo-skills

> Diese Datei ist gleichzeitig öffentliche Roadmap UND **Onboarding-Doku für die nächste Arbeits-Session**. Sie enthält genug Kontext, dass weder ein neuer Claude-Lauf noch ein menschlicher Mitwirkender eine separate Erklärung braucht.

**Stand:** 2026-05-04, Version 1.3 live. v1.2-Re-Tests unter Opus beide PASS (S3-Upload mit zwei Varianten, OpenAI+Patient mit Cloud Act + § 203 StGB). v1.2-Re-Test unter Sonnet 4.6 FAIL (Trigger feuerte nicht) → v1.3 schärft die Description.

---

## 1. Projekt-Snapshot

### Was es ist

Kostenlose Open-Source-Skills für KI-Coding-Tools (Claude AI, Claude Code, später Cursor und Codex), die DACH-Entwicklern automatisch DSGVO-Wissen mitgeben. Erster Skill: `dsgvo-third-country-transfer` (DSGVO Kapitel V, Art. 44-49 + Cloud Act + ePrivacy + Schweiz + Anbieter-Profile).

### Live-URLs

| Wo | URL |
|----|-----|
| GitHub-Repo | <https://github.com/wuemaikblume/dsgvo-skills> |
| Default-Branch | `main` |
| SSH-Remote | `(SSH-remote)` |
| Long-Form-Artikel (persönlich) | <https://maikblume.de/blog/warum-ich-einen-dsgvo-skill-fuer-claude-gebaut-habe> |
| News-Artikel (Chilikanal) | <https://chilikanal.de/artikel/dsgvo-skill-fuer-claude-bringt-eu-compliance-ins-ki-coding> |

### Server-Pfade

| Was | Pfad |
|-----|------|
| Repo lokal | `<REDACTED-PATH>/` |
| Aktiver Skill in Claude Code (User-Personal) | `~/.claude/skills/dsgvo-third-country-transfer/` |
| Maikblume-Projekt | `<REDACTED-PATH>/` |
| Chilikanal-Projekt | `<REDACTED-PATH>/` |

DB-Zugänge: stehen im Memory (`<REDACTED-PATH>`).

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
├── claude/      # gefüllt
├── cursor/      # Platzhalter (README erklärt geplante Struktur)
├── codex/       # Platzhalter
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

### Drei Cross-Reviews (alle Befunde im Repo eingearbeitet)

1. **Sonnet-Subagent intern** (mit WebSearch-Zugriff) — fand CNIL-URL-Datum-Inkonsistenz, Latombe-Quelle fehlt inline, Anthropic/Clerk DPF nicht live verifiziert
2. **GPT/Gemini Standard-Cross-Review** (extern, vom User) — fand Stripe Controller/Processor-Hybrid, Schweizer revDSG fehlt komplett, Art. 26/28/35/48 fehlen
3. **Tiefenrecherche** (extern, vom User, mit Quellen-Crawling) — fand **Brasilien-Adäquanzbeschluss 10.02.2026**, **Supabase nicht DPF**, **Clerk DPF aktiv seit 22.02.2024**, **OpenAI Ireland Ltd. als Vertragspartner**, **Firebase Cloud Functions Default `us-central1`**, **AWS eu-west-2 = UK (nicht EU)**

### Vier Trigger-Tests (in echter Claude-Code-Session)

| Test | Prompt-Kern | Ergebnis |
|------|-------------|----------|
| 1 | „Node-Skript S3-Upload us-east-1" | Skill triggert + DSGVO-Hinweis vorab. Code aber mit `us-east-1` (User-Wunsch respektiert). v1.2 Code-Generation-Regel sollte das in Folgeläufen fixen — **noch nicht re-getestet**. |
| 2 | „Sentry für Next.js" | Compliant Output: EU-DSN, `sendDefaultPii: false`, `beforeSend`-Hook, Replay-Masking. ✅ |
| 3 | „OpenAI mit Patient-Allergie-Tickets" | Skill explizit geladen, Art. 9 erkannt, DSFA empfohlen, Pseudonymisierung als Pflicht, Azure OpenAI als EU-Alternative. **Aber Cloud Act nicht erwähnt** → v1.2 sollte das fixen, **noch nicht re-getestet**. |
| 4 | „DigitalOcean Frankfurt → Hetzner Falkenstein" | Skill nicht überschießend: 4 DSGVO-Bullets, dann technische Migrations-Beratung. ✅ |

---

## 3. Offene Punkte (priorisiert + handlungsfertig)

### Re-Tests von v1.2 (15 Min)

In **neuer** Claude-Code-Session (Skill ist bereits in `~/.claude/skills/`), folgende Prompts wiederholen und bewerten:

- [ ] **Re-Test 1** — derselbe Prompt wie Test 1 oben („Node-Skript S3-Upload us-east-1"). Erwartet: Claude zeigt **beide Varianten** im Code (us-east-1 wie gewünscht UND EU-Whitelist-Pattern als Vergleich), nicht nur Hinweis vorab.
- [ ] **Re-Test 3** — derselbe Prompt wie Test 3 oben (OpenAI + Patient-Allergie). Erwartet: Cloud-Act wird mindestens einmal erwähnt, idealerweise mit Verweis auf EU-souveräne Alternative (Stackit / T-Systems / Self-Hosted).
- [ ] Falls v1.2-Verbesserungen NICHT greifen: SKILL.md weiter schärfen (z.B. Decision Tree um Cloud-Act-Schritt 5b ergänzen, der nicht nur als „⚠ ABER" formuliert ist, sondern als eigener Pflicht-Branch).

### Externer Re-Review (optional)

- [ ] Den korrigierten v1.2-Skill nochmal durch **GPT-5.5** und **Gemini** schicken — gleicher Prompt wie damals (siehe Konversations-Historie / Memory). Zweck: Verifikation, dass Brasilien, Supabase-Korrektur, Clerk-Update, OpenAI-Ireland-Klärung wirklich sauber sind.
- [ ] Bei neuen Befunden: in einem v1.3-Patch einarbeiten.
- [ ] **Achtung:** Beim Verfassen des externen Cross-Check-Prompts den SKILL.md-Body **nicht versehentlich doppelt einfügen** (passierte beim ersten Mal, beide externen KIs flaggten „doppelte Tabelle" — das war der Prompt, nicht der Skill).

### Cursor-Adaption

Ziel: Inhalt von `claude/skills/dsgvo-third-country-transfer/` in Cursor's `.cursor/rules/*.mdc`-Format konvertieren.

- [ ] Cursor unterstützt **kein** Progressive Disclosure → die sechs Sub-Dateien müssen in **eine** `.mdc`-Datei pro Thema gemerged werden, oder in eine einzige Big-File-Variante zusammengelegt
- [ ] Cursor-Frontmatter unterscheidet sich: `description`, `globs` (für File-Pattern-Trigger), `alwaysApply: false` — Cursor-Doku konsultieren: <https://docs.cursor.com/en/context/rules>
- [ ] Empfohlene Struktur:

  ```
  cursor/rules/
  ├── dsgvo-third-country-transfer.mdc    # konsolidiert SKILL.md + PROVIDERS.md + Code-Patterns
  ├── dsgvo-roles.mdc                     # ROLES.md
  ├── dsgvo-dpia.mdc                      # DPIA.md
  ├── dsgvo-cloud-sovereignty.mdc         # CLOUD-ACT.md
  ├── dsgvo-eprivacy-tracking.mdc         # EPRIVACY.md
  └── dsgvo-revdsg-ch.mdc                 # CH-REVDSG.md
  ```

- [ ] `cursor/INSTALL.md` schreiben — analog zu `claude/INSTALL.md`
- [ ] `cursor/README.md` aktualisieren (aktuell Platzhalter)
- [ ] Test: in einem Cursor-Projekt installieren und denselben Test-Set fahren wie bei Claude (siehe ROADMAP Abschnitt B). Trigger-Verhalten dokumentieren.

### OpenAI Codex / GitHub Copilot Adaption

- [ ] **Codex CLI**: liest `AGENTS.md` im Projekt-Root, alle Inhalte sind immer geladen → Inhalt MUSS prägnant zusammengefasst sein
- [ ] **GitHub Copilot**: liest `.github/copilot-instructions.md`
- [ ] Strategie: eine konsolidierte Master-`AGENTS.md` mit den **wichtigsten** Regeln aus allen sieben Markdown-Files, ohne die Detail-Tabellen (die wären zu groß)
- [ ] `codex/AGENTS.md` schreiben — Schwerpunkt: Decision Tree, Anbieter-Quick-Ref, Code-Patterns, Cloud-Act-Hinweis. Sub-Dateien müssen ggf. zu Bullet-Points kondensiert werden.
- [ ] `codex/INSTALL.md`
- [ ] `codex/README.md` aktualisieren

### Weitere Skill-Familien

Alle nach demselben Schema: Prototyp → drei Reviews (Sonnet + extern × 2) → Korrekturen → Tests → Veröffentlichung.

- [ ] **`dsgvo-auth-and-logging`** — Audit-Logs, IP-Speicherung, Login-Tracking, Zweckbindung, Speicherbegrenzung, Pseudonymisierung in Logs. Trigger: Auth-Code, Logging-Setup, Sentry-Scrubbing-Fragen, Login-Flow, Session-Management.
- [ ] **`dsgvo-subject-rights`** — Auskunft (Art. 15), Berichtigung (16), Löschung (17), Einschränkung (18), Portabilität (20), Widerspruch (21). Trigger: User-Account-Features, „Konto löschen"-Endpoints, Datenexport-APIs, Auskunfts-Tickets.
- [ ] **`dsgvo-personal-data-storage`** — Verschlüsselung at-rest und in-transit, Aufbewahrungsfristen pro Datenkategorie, Backups + Löschkonzept, Pseudonymisierung-Patterns. Trigger: DB-Schema, S3-Bucket-Konfig, Encryption-Settings, Backup-Strategien.
- [ ] **`nis2-security-baseline`** — NIS2-Umsetzungsgesetz Deutschland (Stand prüfen), Mindestmaßnahmen Art. 21, 24/72h-Meldepflichten, Risikomanagement, Incident-Response. Trigger: Security-relevante Architektur, Auth-Hardening, Backup-Strategien, KRITIS-Sektor-Hinweise.
- [ ] **`dsgvo-employment`** — § 26 BDSG (Beschäftigtendatenschutz), Bewerber-Daten, Performance-Tracking, HR-Tools (Personio, BambooHR, etc.), Krankenakten. Trigger: HR-System-Integrationen, Performance-Monitoring, Bewerber-Tools.
- [ ] **`email-marketing-compliance`** — UWG §7 (Werbung), Double-Opt-In, B2B-Privileg, Werbung vs. Service-Mail, Tracking-Pixel in E-Mails. Trigger: Newsletter-Setup, Mailgun/SendGrid-Konfig, Tracking-URLs in Mails.

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

## 5. Kontext für die nächste Session

Wenn du eine neue Claude-Code-Session öffnest und an diesem Projekt weiterarbeitest, reicht der folgende Prompt:

> Wir arbeiten am Projekt `dsgvo-skills` weiter. Der aktuelle Stand ist v1.2, alles dokumentiert in `<REDACTED-PATH>/ROADMAP.md` (öffentlich) und `<REDACTED-PATH>/INTERNAL.md` (lokal, gitignored). Lies beide Dateien, dann frage mich, an welchem Abschnitt wir weitermachen wollen, und schlage konkrete erste Schritte vor.

Die Datei enthält:

- alle Live-URLs (Repo, Maikblume, Chilikanal)
- Server-Pfade zu Repo und installierten Skills
- Versions-Historie und Inhalt der drei Cross-Reviews
- Test-Ergebnisse mit konkreten verbleibenden Lücken
- Detaillierte To-Dos pro Bereich
- Konventionen und Pflichten für jeden Skill-Beitrag

Damit ist sowohl ein menschlicher Mitwirkender als auch eine neue KI-Session arbeitsfähig, ohne dass du den ganzen Kontext rekonstruieren musst.
