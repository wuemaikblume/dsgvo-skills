# Cursor — Installation

Diese Cursor-Adaption liefert sechs Regeln im `.cursor/rules/`-Format. Alle sind als **Agent Requested**-Rules konfiguriert (`alwaysApply: false`) — Cursor's Agent lädt die Regel nur, wenn die `description` zur Aufgabe passt. Das spart Kontext und vermeidet Fehl-Trigger.

## Option 1 — Pro Projekt (empfohlen)

Die Regeln gehören in das Projekt, in dem sie greifen sollen, damit jedes Team-Mitglied sie automatisch über Git bekommt.

```bash
cd dein-projekt/
mkdir -p .cursor/rules

# Repo klonen oder als Submodule einbinden
git clone https://github.com/wuemaikblume/dsgvo-skills.git /tmp/dsgvo-skills
cp /tmp/dsgvo-skills/cursor/rules/*.mdc .cursor/rules/

git add .cursor/rules/
git commit -m "chore: add DSGVO Cursor rules from dsgvo-skills"
```

Die Regeln werden über die Description vom Cursor-Agent automatisch gezogen, wenn sie passen. Du kannst eine Regel auch jederzeit manuell laden mit `@dsgvo-third-country-transfer` im Chat.

## Option 2 — User-global (Cursor Settings)

Cursor kennt bisher (Stand Mai 2026) keinen offiziellen User-Global-Rules-Ordner. Workaround: die Regeln in einem **persönlichen Cursor-Workspace** unter `.cursor/rules/` ablegen, oder via *Cursor Settings → Rules* als „User Rule" zentral eintragen (kürzere Form, da User Rules ohne `.mdc`-Header arbeiten).

## Verifikation

In einer beliebigen Cursor-Chat-Session:

```
@dsgvo-third-country-transfer
```

Cursor sollte die Regel laden und in der Antwort den Decision Tree für Drittlandtransfer ausgeben.

## Welche Regel wann greift

| Regel | Greift bei |
|-------|------------|
| `dsgvo-third-country-transfer` | AWS us-*, OpenAI, Sentry, Stripe, Avatar-Uploads, jeglicher Drittland-Datenfluss |
| `dsgvo-roles` | AVV-Fragen, Auftragsverarbeiter-Klärung, gemeinsame Verantwortlichkeit |
| `dsgvo-dpia` | sensible Daten (Art. 9), Profiling, KI-Systeme mit Personenbezug, KRITIS |
| `dsgvo-cloud-sovereignty` | US-Konzern als Cloud-Provider, CLOUD-Act-Risiko, BCRs, BYOK |
| `dsgvo-eprivacy-tracking` | Cookies, Tracking-Pixel, Analytics-Setup, Consent Management |
| `dsgvo-revdsg-ch` | Schweizer Kunden/Verantwortliche, EDÖB-Fragen |

## Unterschiede zur Claude-Variante

| Aspekt | Claude Skills | Cursor Rules |
|--------|---------------|--------------|
| Hauptdatei | `SKILL.md` mit Sub-Files (Progressive Disclosure) | `.mdc` mit allem inline |
| Sub-File-Loading | on-demand, eigener Trigger | jede Regel komplett geladen wenn aktiviert |
| Anbieter-Detail | `PROVIDERS.md` (separat) | in `dsgvo-third-country-transfer.mdc` integriert |

Daraus folgt: Cursor lädt pro aktivierter Regel mehr Tokens auf einmal — dafür ist alles in einer Datei, was Cursor ohnehin nicht progressiv nachladen kann.

## Updates

Die Anbieter-Profile, der DPF-Status und die Schrems-III-Lage ändern sich. Quartalsweise gegen <https://github.com/wuemaikblume/dsgvo-skills> aktualisieren — neue Releases werden mit Tag versioniert (`v1.3`, `v1.4`, …).

```bash
cd /tmp/dsgvo-skills && git pull
cp /tmp/dsgvo-skills/cursor/rules/*.mdc dein-projekt/.cursor/rules/
```

## Disclaimer

Diese Regeln sind eine Best-Practice-Sammlung, **keine Rechtsberatung**. Bei Art. 9-Daten, KRITIS-Sektoren, Aufsichtsanfragen oder Bußgeld-Risiken: Anwalt für IT-Recht oder externen Datenschutzbeauftragten konsultieren. Siehe auch [`DISCLAIMER.md`](../DISCLAIMER.md).
