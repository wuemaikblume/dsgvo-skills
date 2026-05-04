# DSGVO Rules für Cursor

> 🚧 **In Vorbereitung.** Aktuell verfügbar: nur die Claude-Variante in [`/claude`](../claude/).

Sobald die Claude-Skills Version 1.1 stabil ist und in der Praxis getestet, wird hier eine **Cursor-Adaption** als `.cursor/rules/*.mdc` Dateien folgen.

## Geplante Cursor-Rules

- `dsgvo-third-country-transfer.mdc`
- `dsgvo-data-processing-roles.mdc`
- `dsgvo-dpia.mdc`
- `dsgvo-cloud-sovereignty.mdc`
- `dsgvo-eprivacy-tracking.mdc`
- `revdsg-ch.mdc`

## Unterschied zu Claude Skills

Cursor-Rules unterstützen Markdown-Frontmatter mit `description`, `globs` und `alwaysApply`. **Progressive Disclosure** (Sub-Dateien wie bei Claude Skills) wird **nicht unterstützt** — der Inhalt aller Sub-Files wird daher in eine `.mdc` Datei pro Thema gemerged.

## Manueller Workaround heute

Wer heute schon Cursor nutzt, kann den Inhalt der Claude-Skills (`/claude/skills/dsgvo-third-country-transfer/SKILL.md` und Sub-Files) als `.cursor/rules/*.mdc` ablegen. Die Rules-Engine versteht Standard-Markdown.

```bash
# Einfacher Workaround
mkdir -p .cursor/rules
cp ../claude/skills/dsgvo-third-country-transfer/SKILL.md \
   .cursor/rules/dsgvo-third-country-transfer.mdc
```

Issues / PRs für eine native Cursor-Variante willkommen.
