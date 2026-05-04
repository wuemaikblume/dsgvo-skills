# Installation — Claude

> Für Cursor und Codex siehe `../cursor/README.md` bzw. `../codex/README.md` (in Vorbereitung).

## Claude Code (CLI / IDE-Extension)

Skills im Personal-Verzeichnis ablegen:

```bash
# Repo klonen
git clone https://github.com/wuemaikblume/dsgvo-skills.git ~/dsgvo-skills

# Skills nach ~/.claude/skills/ kopieren
mkdir -p ~/.claude/skills
cp -r ~/dsgvo-skills/claude/skills/* ~/.claude/skills/

# Verifizieren — der Skill muss als Verzeichnis mit SKILL.md
# UND mehreren Sub-Dateien (PROVIDERS.md, ROLES.md, ...) erscheinen
ls ~/.claude/skills/dsgvo-third-country-transfer/
# erwartete Ausgabe:
# CH-REVDSG.md  CLOUD-ACT.md  DPIA.md  EPRIVACY.md
# PROVIDERS.md  ROLES.md  SKILL.md
```

Claude Code erkennt Skills beim nächsten Start automatisch.

### Projekt-Skills (statt Personal)

Für ein einzelnes Projekt:

```bash
mkdir -p .claude/skills
cp -r ~/dsgvo-skills/claude/skills/* .claude/skills/
git add .claude/skills && git commit -m "feat: add DSGVO compliance skills"
```

So sind Skills im Team versioniert und für alle Mitwirkenden aktiv.

## Claude.ai (Pro / Max / Team / Enterprise)

Custom Skills sind in den höheren Plänen verfügbar:

1. **ZIP packen:**
   ```bash
   cd dsgvo-skills/claude/skills
   zip -r dsgvo-third-country-transfer.zip dsgvo-third-country-transfer/
   ```
   > Das ZIP enthält **SKILL.md plus alle Sub-Dateien** (PROVIDERS.md, ROLES.md, etc.). Claude.ai lädt die Sub-Dateien on-demand, wenn SKILL.md sie referenziert.

2. **Hochladen:**
   - Claude.ai öffnen → **Settings** → **Features**
   - Bereich „Skills" → **Upload custom skill**
   - ZIP auswählen, beschreiben, speichern

3. **Aktivieren:** Skill ist sofort in neuen Konversationen verfügbar.

> **Hinweis:** Custom Skills auf Claude.ai sind **pro User** sichtbar — sie synchronisieren nicht zwischen Team-Mitgliedern. Jedes Team-Mitglied muss separat hochladen.

## Claude API (eigene Apps)

Für eigene Agents über die Anthropic API:

1. Skill via `/v1/skills` Endpoint hochladen (komplettes Verzeichnis inkl. Sub-Dateien)
2. In jedem Request `skill_id` im `container`-Parameter referenzieren
3. Beta-Header setzen:
   - `code-execution-2025-08-25`
   - `skills-2025-10-02`
   - `files-api-2025-04-14`

Details: [Anthropic Skills API Guide](https://docs.claude.com/en/docs/build-with-claude/skills-guide)

## Update / Deinstallation

```bash
# Update
cd ~/dsgvo-skills && git pull
cp -r ~/dsgvo-skills/claude/skills/* ~/.claude/skills/

# Deinstallation (Claude Code)
rm -rf ~/.claude/skills/dsgvo-*

# Claude.ai: über Settings → Features → Skills entfernen
```

## Verifikation

Nach Installation einen einfachen Trigger-Test fahren:

```
> Schreibe mir bitte ein kleines Node-Skript, das User-Avatare in einen
> AWS S3 Bucket in der Region us-east-1 hochlädt.
```

Mit aktiviertem `dsgvo-third-country-transfer`-Skill sollte Claude:
- Auf Drittlandtransfer hinweisen
- EU-Region (`eu-central-1` o.ä.) vorschlagen — idealerweise mit Whitelist-Pattern
- DPF-Verifikation oder SCC-Pfad erwähnen
- Auf VVT-Eintrag hinweisen
- Bei Bedarf auf weitere Sub-Dateien (z.B. `CLOUD-ACT.md` für US-Behördenrisiko) verweisen

Wenn das nicht passiert: Skill-Verzeichnis prüfen, Claude neu starten, ggf. Frontmatter-Syntax verifizieren.

## Wie die Sub-Dateien funktionieren

Die `SKILL.md` ist die Hauptdatei und wird geladen, wenn Claude den Trigger erkennt. Im Body verweist sie auf weitere Markdown-Dateien (`PROVIDERS.md`, `ROLES.md`, etc.). Claude liest diese Sub-Dateien **nur dann**, wenn sie für den konkreten Anwendungsfall relevant sind — typisch via `bash` und `Read`.

**Vorteil:** Token-effizient. Wer nur eine S3-Region-Frage stellt, kriegt die schlanke `SKILL.md` (~3k Tokens). Wer eine komplexere Cloud-Act-Frage stellt, kriegt zusätzlich `CLOUD-ACT.md` geladen.

Mehr zur Anthropic-Skills-Architektur (Progressive Disclosure): [docs.claude.com](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview).
