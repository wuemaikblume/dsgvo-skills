# Codex CLI / GitHub Copilot — Installation

Diese Adaption liefert zwei Dateien:
- [`AGENTS.md`](AGENTS.md) — für **Codex CLI** (im Projekt-Root abzulegen)
- [`copilot-instructions.md`](copilot-instructions.md) — für **GitHub Copilot** (unter `.github/copilot-instructions.md`)

Inhaltlich nahezu identisch — nur der einleitende Hinweis nennt das jeweilige Tool.

## Wichtig: Always-on, keine Trigger

Anders als Claude Skills oder Cursor Rules lädt Codex CLI **`AGENTS.md` bei jeder Anfrage komplett mit** — es gibt keinen Auto-Trigger pro Thema. Daher ist die Datei bewusst kompakt gehalten (~370 Zeilen, mit Auth/Logging-Sektion ab v1.5) und enthält nur die kritischen Regeln. Detail-Profile (PROVIDERS.md), DSFA-Schwellenanalyse, Cloud-Act-Tiefenmaterial usw. liegen ausschließlich im [Repo](https://github.com/wuemaikblume/dsgvo-skills) — für vertieftes Nachschlagen.

GitHub Copilot lädt `.github/copilot-instructions.md` ähnlich permanent für den Workspace.

## Codex CLI — Installation

```bash
cd dein-projekt/

# Direkt aus dem Repo holen
curl -fsSL \
  https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/AGENTS.md \
  -o AGENTS.md

# Oder als Submodule / git clone, falls du Updates über git pull willst
# git clone https://github.com/wuemaikblume/dsgvo-skills.git /tmp/dsgvo-skills
# cp /tmp/dsgvo-skills/codex/AGENTS.md ./AGENTS.md

git add AGENTS.md
git commit -m "chore: add DSGVO compliance instructions for Codex CLI"
```

Wenn dein Projekt bereits eine `AGENTS.md` hat: den Inhalt dieser Datei dort einfügen — am besten als eigenen Abschnitt `## DSGVO-Compliance` oder gleich vorne, damit er nicht in projektspezifischen Anweisungen untergeht.

## GitHub Copilot — Installation

```bash
cd dein-projekt/
mkdir -p .github

curl -fsSL \
  https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/copilot-instructions.md \
  -o .github/copilot-instructions.md

git add .github/copilot-instructions.md
git commit -m "chore: add DSGVO compliance instructions for GitHub Copilot"
```

Aktiviere zusätzlich in den Repo-Settings: *Settings → Code & automation → Copilot → Custom instructions* — dort wird die Datei automatisch erkannt, sobald sie im Default-Branch liegt.

## Verifikation (Codex CLI)

In einer Codex-Session, in einem Verzeichnis mit `AGENTS.md`:

```
> Schreib mir ein Node.js-Skript, das User-Avatare in einen S3-Bucket
  in us-east-1 hochlädt.
```

Erwartung: Codex zeigt **beide Varianten** (us-east-1 wie angefragt + EU-Whitelist-Pattern), nennt DPF-Pflichten als Bullet-Liste, weist auf CLOUD Act bei sensiblen Daten hin.

Tritt das nicht ein: prüfen, ob `AGENTS.md` wirklich im aktiven CWD liegt und ob die Codex-Version `~/codex` aktuell ist (`codex --version`).

## Verifikation (Copilot)

In VS Code mit aktiviertem Copilot Chat, im Workspace mit `.github/copilot-instructions.md`:

```
@workspace Schreib einen S3-Upload für User-Avatare in us-east-1.
```

Erwartung: gleiches Verhalten wie oben. Falls Copilot die Instructions ignoriert: prüfen, ob die Datei im Default-Branch ist und ob *Custom instructions* in Repo-Settings aktiviert wurden.

## Updates

DPF-Status, Schrems-III-Lage und Adäquanzbeschlüsse ändern sich. Quartalsweise gegen das Repo aktualisieren:

```bash
# Codex
curl -fsSL \
  https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/AGENTS.md \
  -o AGENTS.md
git diff AGENTS.md

# Copilot
curl -fsSL \
  https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/copilot-instructions.md \
  -o .github/copilot-instructions.md
git diff .github/copilot-instructions.md
```

Releases sind getaggt (`v1.3`, `v1.4`, …). Für reproduzierbare Stände auf einen Tag pinnen:

```bash
curl -fsSL \
  https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/v1.3/codex/AGENTS.md \
  -o AGENTS.md
```

## Was diese Variante NICHT liefert

Codex und Copilot lesen statische Markdown-Dateien — kein Tool-Use, kein Sub-File-Loading, kein Live-DPF-Lookup. Daher:

- **Detaillierte Anbieter-Profile** (16 Anbieter mit AVV-Spezifika) → siehe [`/claude/skills/dsgvo-third-country-transfer/PROVIDERS.md`](../claude/skills/dsgvo-third-country-transfer/PROVIDERS.md)
- **DSFA-Schwellenanalyse + DSK Muss-Liste** → [`DPIA.md`](../claude/skills/dsgvo-third-country-transfer/DPIA.md)
- **Cloud Act + BCR-Vergleich** → [`CLOUD-ACT.md`](../claude/skills/dsgvo-third-country-transfer/CLOUD-ACT.md)
- **Schweiz revDSG / EDÖB** → [`CH-REVDSG.md`](../claude/skills/dsgvo-third-country-transfer/CH-REVDSG.md)
- **TTDSG / ePrivacy / Cookies** → [`EPRIVACY.md`](../claude/skills/dsgvo-third-country-transfer/EPRIVACY.md)
- **Rollen Art. 6/9/26/28** → [`ROLES.md`](../claude/skills/dsgvo-third-country-transfer/ROLES.md)

Bei Bedarf in Codex direkt die Roh-URL nachladen lassen, oder die relevante Sub-Datei kopieren und projektspezifisch in `AGENTS.md` einfügen.

## Disclaimer

Diese Anweisungen sind eine Best-Practice-Sammlung, **keine Rechtsberatung**. Siehe auch [`DISCLAIMER.md`](../DISCLAIMER.md).
