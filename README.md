# DSGVO Skills für KI-Coding-Tools

Kostenlose Compliance-Pakete für **Claude AI / Claude Code**, **Cursor** und **OpenAI Codex CLI / GitHub Copilot** — speziell für **DACH-Entwickler**, die produktiv mit KI-Coding arbeiten und dabei DSGVO, NIS2 und nationale Datenschutzregeln im Blick behalten müssen.

> **Was ist ein Skill?** Eine Markdown-Datei mit YAML-Frontmatter, die das Tool bei passendem Kontext automatisch lädt. Bei Claude: [offizielle Anthropic-Doku](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview).

## Status (v1.5)

- ✅ **`/claude`** — Anthropic Skills für Claude Code, Claude.ai, Claude API. Zwei Skill-Familien: Drittlandtransfer (+ 5 Vertiefungs-Dateien) und Auth & Logging (+ 3 Vertiefungs-Dateien).
- ✅ **`/cursor`** — Neun `.cursor/rules/*.mdc` Agent-Requested-Rules.
- ✅ **`/codex`** — Konsolidierte `AGENTS.md` (Codex CLI) und `copilot-instructions.md` (GitHub Copilot), beide mit Drittlandtransfer- und Auth/Logging-Sektion.

## Repository-Struktur

```
dsgvo-skills/
├── claude/                          # Anthropic Skills (live)
│   ├── INSTALL.md
│   └── skills/
│       ├── dsgvo-third-country-transfer/
│       │   ├── SKILL.md            # Hauptdatei (Decision Tree, Anbieter-Quick-Ref, Code)
│       │   ├── PROVIDERS.md        # Detail-Profile pro Anbieter
│       │   ├── ROLES.md            # Art. 6/9/26/28 — Controller/Processor/Joint
│       │   ├── DPIA.md             # Art. 35 — Datenschutz-Folgenabschätzung
│       │   ├── CLOUD-ACT.md        # Art. 48 / Cloud Act / BCRs / Datensouveränität
│       │   ├── CH-REVDSG.md        # Schweiz revDSG, Swiss-US DPF
│       │   └── EPRIVACY.md         # TTDSG / Cookies / Tracking-Pixel
│       └── dsgvo-auth-and-logging/
│           ├── SKILL.md            # Decision Tree, Code-Generation-Regel, Cross-Links
│           ├── AUTH-TOM.md         # Art. 32 TOMs: Hashing, Session, Cookies, MFA, Rate-Limit
│           ├── LOGGING.md          # Sentry/Datadog/Pino-Scrubbing, Audit-Log, Retention
│           └── IP-ADDRESSES.md     # IP=personenbezogen (Breyer), Kürzung, X-Forwarded-For
├── cursor/                          # 9 .mdc Rules (Agent Requested)
│   ├── INSTALL.md
│   └── rules/
│       ├── dsgvo-third-country-transfer.mdc   # SKILL + PROVIDERS gemerged
│       ├── dsgvo-roles.mdc
│       ├── dsgvo-dpia.mdc
│       ├── dsgvo-cloud-sovereignty.mdc
│       ├── dsgvo-eprivacy-tracking.mdc
│       ├── dsgvo-revdsg-ch.mdc
│       ├── dsgvo-auth-and-logging.mdc         # SKILL + LOGGING gemerged
│       ├── dsgvo-auth-tom.mdc                 # Hash/Session/Cookie/MFA/Rate-Limit
│       └── dsgvo-ip-addresses.mdc             # IP-Speicherung, Kürzung, Breyer
├── codex/                           # Codex CLI + GitHub Copilot
│   ├── INSTALL.md
│   ├── AGENTS.md                    # für Codex CLI (Projekt-Root)
│   └── copilot-instructions.md      # für .github/copilot-instructions.md
├── SKILL-BOUNDARIES.md              # Skill-Familien-Architektur, Cross-Link-Disziplin
├── ROADMAP.md
├── DISCLAIMER.md
├── LICENSE                          # MIT
└── README.md                        # diese Datei
```

## Verfügbare Skills (Claude)

### `dsgvo-third-country-transfer` (v1.4)

Triggert bei Code- oder Infrastruktur-Entscheidungen, die personenbezogene Daten an Nicht-EU-Dienste übermitteln.

**Was abgedeckt ist:**
- DSGVO Kapitel V (Art. 44-49): Adäquanz, SCCs, Transfer Impact Assessment
- 17 Adäquanzländer inkl. UK (bis 27.12.2031), **Brasilien** (seit 10.2.2026)
- EU-US Data Privacy Framework — Verifikation gegen offizielle ITA-Liste, Stichtags-Wissen, Schrems-III-Risiko
- Schweizer Pendant (Swiss-US DPF) — siehe `CH-REVDSG.md`
- 16 typische Anbieter mit Quick-Reference + Detail-Profilen (`PROVIDERS.md`)
- Code-Patterns für AWS, Sentry, OpenAI, Firebase, Datadog, Supabase
- Vertiefungen zu Rollen, DSFA, Cloud Act, ePrivacy

### `dsgvo-auth-and-logging` (v1.0, neu in v1.5)

Triggert bei Code, der Authentifizierung, Login, Session-Management, Audit-Logs, Application-/Error-Logging, Brute-Force-Schutz, MFA oder IP-Speicherung berührt — auch wenn der Prompt rein technisch formuliert ist („Sentry für Next.js einrichten", „Brute-Force mit IP-Tracking", „MFA mit TOTP").

**Was abgedeckt ist:**
- DSGVO Art. 5 (Zweckbindung, Speicherbegrenzung), Art. 6 (Rechtsgrundlage), Art. 25 (Privacy by Design), Art. 32 (Sicherheit der Verarbeitung)
- Argon2id-/bcrypt-Parameter nach OWASP 2024 + BSI TR-02102-1
- Sichere Cookie-Flags (`HttpOnly`, `Secure`, `SameSite`), Session-Timeout-Matrix
- MFA-Empfehlungen (TOTP nach RFC 6238, WebAuthn) inkl. Replay-Schutz, AES-GCM-Secret-Verschlüsselung, Argon2-gehashte Recovery-Codes — KRITIS-Cross-Link zu NIS2 § 30 BSIG
- Rate-Limit-Werte für Login, Password-Reset, MFA-Verify
- Sentry/Datadog/Pino/Winston/Bunyan/morgan-Scrubbing-Patterns (`beforeSend`, `redact`-Paths)
- Audit-Log-Schema mit gestaffelter IP-Retention (volle IP 7 Tage, gekürzt 90 Tage)
- IP-Adressen unter EuGH C-582/14 Breyer + BGH VI ZR 135/13 — Kürzung (/24 IPv4, /48 IPv6), HMAC-Pseudonymisierung, X-Forwarded-For-Trust-Chain
- Code-Generation-Regel: Default DSGVO-konform; bei explizitem Wunsch („bcrypt cost 10", „IP voll loggen") zwei Varianten + Pflicht-Tabelle (Norm / Risiko / Konsequenz)

### Was bewusst NICHT abgedeckt ist (kommt als eigene Skills)

- `dsgvo-subject-rights` — Auskunft, Löschung, Portabilität (Art. 15-22)
- `dsgvo-personal-data-storage` — Verschlüsselung, Aufbewahrungsfristen, Backups
- `nis2-security-baseline` — NIS2-Mindestmaßnahmen, 24/72h-Meldepflichten
- `dsgvo-employment` — § 26 BDSG, Bewerber-Daten, HR-Tools
- `email-marketing-compliance` — UWG §7, Double-Opt-In, Werbung vs. Service-Mail

## Installation (Claude — Kurzform)

### Claude Code

```bash
git clone https://github.com/wuemaikblume/dsgvo-skills.git ~/dsgvo-skills
mkdir -p ~/.claude/skills
cp -r ~/dsgvo-skills/claude/skills/* ~/.claude/skills/
```

### Claude.ai (Pro/Max/Team/Enterprise)

```bash
cd dsgvo-skills/claude/skills
zip -r dsgvo-third-country-transfer.zip dsgvo-third-country-transfer/
# In Claude.ai: Settings → Features → Skills → Upload custom skill
```

Detaillierte Anleitungen: **[claude/INSTALL.md](claude/INSTALL.md)**

## Was diese Skills tun

- **Vor** Architektur-Entscheidungen mit DSGVO-Relevanz prüft Claude Rechtsgrundlage und Transfermechanismus
- **Während** der Code-Generierung weist Claude auf Pflichten hin (TIA, SCCs, AVV, VVT)
- **Bei** typischen Fallen (z.B. Firebase Cloud Functions Default-Region, Sentry US-DSN) korrigiert Claude proaktiv
- **Nach** Implementierung erinnert Claude an Dokumentation (Art. 30 VVT, Art. 13/14 Information)

## Was diese Skills NICHT sind

- ❌ **Keine Rechtsberatung.** Best-Practice-Sammlung auf Basis öffentlicher Quellen (DSGVO-Volltext, BfDI, DSK, EDPB, EuGH, CNIL, ANPD).
- ❌ **Keine Aufsichts-Garantie.** Bei sensiblen Datenkategorien (Art. 9), KRITIS, oder konkreten Aufsichtsanfragen: Anwalt für IT-Recht oder externen Datenschutzbeauftragten.
- ❌ **Keine automatische Aktualisierung.** Rechtslage ändert sich (Schrems-III, NIS2-Umsetzung, neue DSK-Beschlüsse). Stichtag steht in jeder Datei. PRs willkommen.

Vollständiger Disclaimer: [DISCLAIMER.md](DISCLAIMER.md).

## Modell-Hinweis: Opus vs. Sonnet

Trigger-Tests am 2026-05-05 mit dem Skill `dsgvo-auth-and-logging` (sechs Setup-Prompts: bcrypt-Login, Sentry-Setup, Audit-Log mit IP, Brute-Force, MFA TOTP, Negativtest) haben ein klares Muster gezeigt:

- **Claude Opus 4.7** lädt DSGVO-Skills zuverlässig — auch wenn der Prompt rein technisch formuliert ist („Sentry für Next.js einrichten", „Brute-Force-Schutz", „MFA mit TOTP"). 6/6 Tests PASS.
- **Claude Sonnet 4.6** lädt sie nur, wenn explizite DSGVO-Schlüsselwörter im Prompt stehen (IP-Adresse, Audit-Log, etc.). Bei impliziten DSGVO-Anliegen produziert Sonnet Code, der an DSGVO-Bestimmungen vorbeigeht — Wizard-Defaults ohne PII-Scrubbing, IPs roh in Redis-Keys, MFA-Implementierungen ohne Replay-Schutz oder Secret-Verschlüsselung. Description-Schärfungen halfen in 0/3 Re-Runs.

**Praxis-Empfehlung:**

- Wer mit Sonnet entwickelt, sollte bei sicherheits- und datenschutzrelevanten Aufgaben den Skill **explizit** anstoßen, z.B.: „Beachte den Skill `dsgvo-auth-and-logging` und implementiere…" oder im Project-Memory / `CLAUDE.md` festschreiben, dass DSGVO-Skills bei Auth-/Logging-/Monitoring-Tasks zu konsultieren sind.
- Für DSGVO-kritische Code-Reviews ist **Opus** die robustere Wahl.

Diese Einschränkung ist ein Modell-Verhalten, kein Skill-Defekt — die Skills sind unabhängig vom Modell inhaltlich vollständig.

## Mitwirken

- **Feedback / Fehler melden:** GitHub Issues
- **Eigene Skills beisteuern:** Pull Request mit Quellenangaben in jedem Skill
- **Anwalt / DSB / DPO?** Besonders willkommen — bitte Berufsbezeichnung im PR angeben

## Roadmap

Die ausführliche Roadmap mit aktuellen Aufgaben, Versions-Historie, Test-Ergebnissen und Mitwirkungs-Konventionen steht in [ROADMAP.md](ROADMAP.md) — sie dient gleichzeitig als Onboarding-Doku für neue Mitwirkende und als Status-Snapshot für jede Arbeits-Session.

### Skill-Familien (Reihenfolge offen)

| Skill | Status | Inhalt |
|-------|--------|--------|
| `dsgvo-third-country-transfer` | ✅ v1.4 | Drittlandtransfer + 5 Sub-Dateien |
| `dsgvo-auth-and-logging` | ✅ v1.0 | Auth-Code, Audit-Logs, IP-Speicherung, MFA, Sentry-Scrubbing + 3 Sub-Dateien |
| `dsgvo-subject-rights` | ⬜ | Auskunft, Löschung, Portabilität (Art. 15-22) |
| `dsgvo-personal-data-storage` | ⬜ | Verschlüsselung, Aufbewahrungsfristen, Backups |
| `nis2-security-baseline` | ⬜ | Mindestmaßnahmen, 24/72h-Meldepflichten |
| `dsgvo-employment` | ⬜ | § 26 BDSG, Bewerber, HR-Tools |
| `email-marketing-compliance` | ⬜ | UWG §7, Double-Opt-In, Werbung vs. Service |

## Lizenz

[MIT](LICENSE) — frei nutzbar, auch kommerziell. Bitte Quelle nennen, wenn du Skills oder Teile davon verwendest.

## Stichtag

Alle Skills tragen **Stand Mai 2026**. Vor Einsatz aktuellen Stand der zitierten Quellen prüfen — die Markdown-Dateien werden nicht automatisch aktualisiert.
