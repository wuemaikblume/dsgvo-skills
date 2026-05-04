# DSGVO Instructions für OpenAI Codex / Copilot

> 🚧 **In Vorbereitung.** Aktuell verfügbar: nur die Claude-Variante in [`/claude`](../claude/).

Sobald die Claude-Skills Version 1.1 stabil ist und in der Praxis getestet, wird hier eine **Codex-Adaption** als `AGENTS.md` (Codex CLI) und `copilot-instructions.md` (GitHub Copilot) folgen.

## Geplante Inhalte

- `AGENTS.md` — konsolidierte Compliance-Anweisungen für Codex CLI (Projekt-Root)
- `copilot-instructions.md` — `.github/copilot-instructions.md` für Copilot-Workspaces
- ggf. weitere Adapter für Cline, Continue, etc.

## Unterschied zu Claude Skills

Codex liest `AGENTS.md` im Projekt-Root und folgt allen Anweisungen darin **bei jeder Anfrage**. Es gibt **kein Auto-Trigger** auf bestimmte Themen — der gesamte Inhalt wird immer mitgeladen. Für ein 8-Skill-Pack heißt das: alles muss inline und prägnant sein, sonst wird der Kontext zu groß.

## Manueller Workaround heute

Wer heute Codex CLI oder Copilot nutzt, kann die wichtigsten Regeln der Claude-Skills manuell in `AGENTS.md` übernehmen. Volle Triggerlogik wie bei Claude ist nicht möglich.

Issues / PRs für eine native Variante willkommen.
