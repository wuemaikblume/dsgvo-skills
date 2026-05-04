# Installation

## Claude Code (CLI / IDE-Extension)

Skills im Personal-Verzeichnis ablegen:

```bash
# Repo klonen
git clone https://github.com/<REPO>/dsgvo-skills.git ~/dsgvo-skills

# Skills nach ~/.claude/skills/ kopieren
mkdir -p ~/.claude/skills
cp -r ~/dsgvo-skills/skills/* ~/.claude/skills/

# Verifizieren
ls ~/.claude/skills/
# erwartete Ausgabe: dsgvo-third-country-transfer/  (etc.)
```

Claude Code erkennt Skills beim nächsten Start automatisch. Test:

```
> Wir wollen ein Backend deployen, das Userdaten in einem S3-Bucket speichert.
```

Wenn der Skill greift, sollte Claude proaktiv nach EU-Region und Drittlandtransfer-Rechtsgrundlage fragen.

### Projekt-Skills (statt Personal)

Für ein einzelnes Projekt:

```bash
mkdir -p .claude/skills
cp -r ~/dsgvo-skills/skills/* .claude/skills/
git add .claude/skills && git commit -m "feat: add DSGVO compliance skills"
```

So sind Skills im Team versioniert und für alle Mitwirkenden aktiv.

## Claude.ai (Pro / Max / Team / Enterprise)

Custom Skills sind in den höheren Plänen verfügbar:

1. **ZIP packen:**
   ```bash
   cd dsgvo-skills/skills
   zip -r dsgvo-third-country-transfer.zip dsgvo-third-country-transfer/
   ```

2. **Hochladen:**
   - Claude.ai öffnen → **Settings** → **Features**
   - Bereich „Skills" → **Upload custom skill**
   - ZIP auswählen, beschreiben, speichern

3. **Aktivieren:** Skill ist sofort in neuen Konversationen verfügbar.

> **Hinweis:** Custom Skills auf Claude.ai sind **pro User** sichtbar — sie synchronisieren nicht zwischen Team-Mitgliedern. Jedes Team-Mitglied muss separat hochladen.

## Claude API (eigene Apps)

Für eigene Agents über die Anthropic API:

1. Skill via `/v1/skills` Endpoint hochladen
2. In jedem Request `skill_id` im `container`-Parameter referenzieren
3. Beta-Header setzen:
   - `code-execution-2025-08-25`
   - `skills-2025-10-02`
   - `files-api-2025-04-14`

Details: [Anthropic Skills API Guide](https://docs.claude.com/en/docs/build-with-claude/skills-guide)

## Cursor / GitHub Copilot / andere

Diese Tools unterstützen Anthropic Skills derzeit **nicht nativ**. Workarounds:

### Cursor

Skill-Inhalt in `.cursor/rules/dsgvo-third-country-transfer.mdc` einbinden:

```bash
mkdir -p .cursor/rules
cp ~/dsgvo-skills/skills/dsgvo-third-country-transfer/SKILL.md \
   .cursor/rules/dsgvo-third-country-transfer.mdc
```

Trigger funktioniert ähnlich wie bei Claude Skills (Pattern-basiert).

### Copilot / andere

System-Prompt um die wichtigsten Skill-Regeln ergänzen oder als
Kontext-File mitgeben. Volle Skill-Triggerung wie bei Claude ist nicht
möglich.

## Update / Deinstallation

```bash
# Update
cd ~/dsgvo-skills && git pull
cp -r skills/* ~/.claude/skills/

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
- EU-Region (`eu-central-1` o.ä.) vorschlagen
- DPF-Verifikation oder SCC-Pfad erwähnen
- Auf VVT-Eintrag hinweisen

Wenn das nicht passiert: Skill-Verzeichnis prüfen, Claude neu starten,
ggf. Frontmatter-Syntax verifizieren.
