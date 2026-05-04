# DSGVO Skills für Claude

Kostenlose Compliance-Skills für [Claude AI](https://claude.ai), [Claude Code](https://claude.com/claude-code) und kompatible Tools — speziell für **DACH-Entwickler**, die produktiv mit KI-Coding arbeiten und dabei DSGVO, NIS2 und nationale Datenschutzregeln im Blick behalten müssen.

> **Was ist ein Skill?** Eine Markdown-Datei mit YAML-Frontmatter, die Claude bei passendem Kontext automatisch lädt. Mehr dazu in der [offiziellen Anthropic-Doku](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview).

## Status

🚧 **Work in Progress** — derzeit ein Skill verfügbar (`dsgvo-third-country-transfer`), weitere folgen iterativ.

Geplante Skills:
- ✅ `dsgvo-third-country-transfer` — Drittlandtransfer (Art. 44-49 DSGVO, DPF, SCCs, TIA)
- ⬜ `dsgvo-personal-data-storage` — Speicherung personenbezogener Daten (Region, Verschlüsselung, Aufbewahrung)
- ⬜ `dsgvo-cookies-tracking` — TTDSG, Consent, Tracking
- ⬜ `dsgvo-auth-and-logging` — Audit-Logs, IP-Speicherung, Zweckbindung
- ⬜ `dsgvo-subject-rights` — Auskunft, Löschung, Portabilität (Art. 15-20)
- ⬜ `dsgvo-dpia-trigger` — Wann ist DSFA Pflicht (Art. 35)
- ⬜ `nis2-security-baseline` — Mindestmaßnahmen, Meldepflichten
- ⬜ `email-marketing-compliance` — UWG §7, Double-Opt-In

## Installation

### Claude Code (lokal)

```bash
git clone https://github.com/<REPO>/dsgvo-skills.git
cp -r dsgvo-skills/skills/* ~/.claude/skills/
```

Skills werden beim nächsten Start automatisch erkannt.

### Claude.ai (Pro/Max/Team/Enterprise)

1. Skill-Ordner als ZIP packen: `cd skills && zip -r dsgvo-third-country-transfer.zip dsgvo-third-country-transfer/`
2. In Claude.ai: **Settings → Features → Skills → Upload custom skill**
3. ZIP hochladen

Detaillierte Anleitung in [INSTALL.md](INSTALL.md).

### Cursor / andere Tools

Cursor unterstützt Anthropic Skills derzeit nicht nativ. Workaround: SKILL.md-Inhalt in `.cursor/rules/` einbinden oder als System-Prompt nutzen.

## Was diese Skills tun

Sie ergänzen Claudes Standardverhalten um DACH-spezifisches Compliance-Wissen:

- **Vor** Code-Vorschlägen, die DSGVO-Relevanz haben (z.B. US-Cloud-Region, Drittland-API), prüft Claude die Rechtsgrundlage.
- **Während** Architektur-Entscheidungen weist Claude auf Pflichten hin (TIA, SCCs, AVV).
- **Nach** Implementierung erinnert Claude an Dokumentation (VVT nach Art. 30).

## Was diese Skills NICHT sind

- ❌ **Keine Rechtsberatung.** Best-Practice-Sammlung, basierend auf öffentlichen Quellen (BfDI, DSK, EDPB, EuGH-Rechtsprechung).
- ❌ **Keine Garantie für Aufsichtskonformität.** Bei sensiblen Datenkategorien (Art. 9 DSGVO), KRITIS-Bezug oder konkreten Aufsichtsanfragen: Anwalt oder externer Datenschutzbeauftragter.
- ❌ **Keine automatische Aktualisierung.** Rechtslage ändert sich (Schrems III, NIS2-Umsetzung, neue DSK-Beschlüsse). Pull Requests willkommen.

Vollständiger Disclaimer: [DISCLAIMER.md](DISCLAIMER.md).

## Mitwirken

- **Feedback / Fehler melden:** GitHub Issues
- **Eigene Skills beisteuern:** Pull Request mit Quellenangaben in jedem Skill
- **Anwalt / DSB / DPO?** Besonders willkommen — bitte Berufsbezeichnung im PR angeben

## Lizenz

[MIT](LICENSE) — frei nutzbar, auch kommerziell. Bitte Quelle nennen.

## Stand

Alle Skills sind mit **Stichtag** versehen (z.B. „Stand Mai 2026"). Vor Einsatz aktuellen Stand der zitierten Quellen prüfen — die Markdown-Dateien werden nicht automatisch aktualisiert.
