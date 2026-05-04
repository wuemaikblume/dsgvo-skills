# DSGVO Skills für KI-Coding-Tools

Kostenlose Compliance-Pakete für **Claude AI / Claude Code** (heute), **Cursor** und **OpenAI Codex** (geplant) — speziell für **DACH-Entwickler**, die produktiv mit KI-Coding arbeiten und dabei DSGVO, NIS2 und nationale Datenschutzregeln im Blick behalten müssen.

> **Was ist ein Skill?** Eine Markdown-Datei mit YAML-Frontmatter, die das Tool bei passendem Kontext automatisch lädt. Bei Claude: [offizielle Anthropic-Doku](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview).

## Status

- ✅ **`/claude`** — Anthropic Skills für Claude Code, Claude.ai, Claude API. Eine Skill-Familie verfügbar (Drittlandtransfer + 5 Vertiefungs-Dateien).
- 🚧 **`/cursor`** — Adaption auf `.cursor/rules/*.mdc` geplant.
- 🚧 **`/codex`** — Adaption auf `AGENTS.md` (Codex CLI) und `copilot-instructions.md` geplant.

## Repository-Struktur

```
dsgvo-skills/
├── claude/                          # Anthropic Skills (live)
│   ├── INSTALL.md
│   └── skills/
│       └── dsgvo-third-country-transfer/
│           ├── SKILL.md            # Hauptdatei (Decision Tree, Anbieter-Quick-Ref, Code)
│           ├── PROVIDERS.md        # Detail-Profile pro Anbieter
│           ├── ROLES.md            # Art. 6/9/26/28 — Controller/Processor/Joint
│           ├── DPIA.md             # Art. 35 — Datenschutz-Folgenabschätzung
│           ├── CLOUD-ACT.md        # Art. 48 / Cloud Act / BCRs / Datensouveränität
│           ├── CH-REVDSG.md        # Schweiz revDSG, Swiss-US DPF
│           └── EPRIVACY.md         # TTDSG / Cookies / Tracking-Pixel
├── cursor/                          # Platzhalter — kommt
├── codex/                           # Platzhalter — kommt
├── DISCLAIMER.md
├── LICENSE                          # MIT
└── README.md                        # diese Datei
```

## Verfügbare Skills (Claude)

### `dsgvo-third-country-transfer` (v1.1)

Triggert bei Code- oder Infrastruktur-Entscheidungen, die personenbezogene Daten an Nicht-EU-Dienste übermitteln.

**Was abgedeckt ist:**
- DSGVO Kapitel V (Art. 44-49): Adäquanz, SCCs, Transfer Impact Assessment
- 17 Adäquanzländer inkl. UK (bis 27.12.2031), **Brasilien** (seit 10.2.2026)
- EU-US Data Privacy Framework — Verifikation, Stichtags-Wissen, Schrems-III-Risiko
- Schweizer Pendant (Swiss-US DPF) — siehe `CH-REVDSG.md`
- 16 typische Anbieter mit Quick-Reference + Detail-Profilen (`PROVIDERS.md`)
- Code-Patterns für AWS, Sentry, OpenAI, Firebase, Datadog, Supabase
- Vertiefungen zu Rollen, DSFA, Cloud Act, ePrivacy

**Was bewusst NICHT abgedeckt ist:**
- NIS2 (kommt als eigener Skill)
- Beschäftigtendatenschutz § 26 BDSG (kommt)
- Newsletter-Recht / UWG §7 (kommt)

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

## Mitwirken

- **Feedback / Fehler melden:** GitHub Issues
- **Eigene Skills beisteuern:** Pull Request mit Quellenangaben in jedem Skill
- **Anwalt / DSB / DPO?** Besonders willkommen — bitte Berufsbezeichnung im PR angeben

## Roadmap

Die ausführliche Roadmap mit aktuellen Aufgaben, Versions-Historie, Test-Ergebnissen und Mitwirkungs-Konventionen steht in [ROADMAP.md](ROADMAP.md) — sie dient gleichzeitig als Onboarding-Doku für neue Mitwirkende und als Status-Snapshot für jede Arbeits-Session.

### Skill-Familien (Reihenfolge offen)

| Skill | Status | Inhalt |
|-------|--------|--------|
| `dsgvo-third-country-transfer` | ✅ v1.1 | Drittlandtransfer + 5 Sub-Dateien |
| `dsgvo-auth-and-logging` | ⬜ | Audit-Logs, IP-Speicherung, Login-Tracking, Zweckbindung |
| `dsgvo-subject-rights` | ⬜ | Auskunft, Löschung, Portabilität (Art. 15-22) |
| `dsgvo-personal-data-storage` | ⬜ | Verschlüsselung, Aufbewahrungsfristen, Backups |
| `nis2-security-baseline` | ⬜ | Mindestmaßnahmen, 24/72h-Meldepflichten |
| `dsgvo-employment` | ⬜ | § 26 BDSG, Bewerber, HR-Tools |
| `email-marketing-compliance` | ⬜ | UWG §7, Double-Opt-In, Werbung vs. Service |

## Lizenz

[MIT](LICENSE) — frei nutzbar, auch kommerziell. Bitte Quelle nennen, wenn du Skills oder Teile davon verwendest.

## Stichtag

Alle Skills tragen **Stand Mai 2026**. Vor Einsatz aktuellen Stand der zitierten Quellen prüfen — die Markdown-Dateien werden nicht automatisch aktualisiert.
