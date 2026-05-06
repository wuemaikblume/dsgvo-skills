# DSGVO Instructions für OpenAI Codex CLI / GitHub Copilot

Konsolidierte DSGVO-Compliance-Anweisungen für Tools, die statische Markdown-Files in den System-Prompt laden:

- [`AGENTS.md`](AGENTS.md) — Codex CLI (im Projekt-Root)
- [`copilot-instructions.md`](copilot-instructions.md) — GitHub Copilot (`.github/copilot-instructions.md`)

**Stand:** v1.6, parallel zur Claude- und Cursor-Variante. Inhaltlich kondensiert, weil Codex/Copilot die Datei bei jeder Anfrage komplett mitladen — kein Auto-Trigger, kein Sub-File-Loading.

## Was drin ist

- Decision Tree für Drittlandtransfer (Art. 44-49 DSGVO)
- Anbieter-Quick-Ref für 15 typische SaaS-Provider (DPF-Status, Pflicht-Aktion)
- Code-Patterns: AWS S3 Fail-closed Whitelist, Sentry EU-DSN, Firebase Region zwingend, OpenAI Triangulation, Datadog/Supabase EU
- Code-Generierungs-Regel: bei explizitem US-Wunsch beide Varianten zeigen + Pflicht-Bullets
- US CLOUD Act-Hinweis als eigener Pflicht-Branch bei Art. 9 / KRITIS / § 203 StGB
- Pflicht-Dokumentation (VVT, Art. 30) + häufige Fallen
- Schweiz revDSG, ePrivacy/TTDSG, NIS2, DSFA-Pflichtfälle als Kurzhinweise
- **Auth & Logging (ab v1.5):** Decision Tree für Hashing/Sessions/IP-Logging/Sentry/MFA/Brute-Force, Code-Patterns (Argon2id, Cookie-Flags, IP-Truncation, Rate-Limit, Sentry-Scrubbing), Audit-Log-Schema mit gestaffelter Retention
- **E-Mail-Marketing (ab v1.6):** Decision Tree für UWG § 7 + DSGVO Art. 6/7/21 + TDDDG § 25, Anbieter-Quick-Ref (Mailchimp/Klaviyo/HubSpot/ActiveCampaign/Brevo/CleverReach/Rapidmail/MailerLite mit DPF-IDs), Code-Patterns (DOI-Token, Mail-Template-Klassifikation, Suppression-Hash, RFC 8058 List-Unsubscribe), BGH-Linie zur Service-vs-Werbung-Abgrenzung

## Was bewusst NICHT drin ist

Detaillierte Anbieter-Profile, DSFA-Schwellenanalyse, BCR-Vergleich, Schweiz-Vertiefung — diese liegen ausschließlich im Repo unter [`/claude/skills/dsgvo-third-country-transfer/`](../claude/skills/dsgvo-third-country-transfer/). Bei Bedarf kann der Codex-User die Roh-URL nachladen oder die relevante Sub-Datei projektspezifisch ergänzen.

Hintergrund: Codex/Copilot würden andernfalls bei jeder Anfrage 30+ KB an Compliance-Material mitschleppen, was Token-Budget und Antwort-Latenz unnötig belastet.

## Installation

Siehe [`INSTALL.md`](INSTALL.md). Kurzform:

```bash
# Codex CLI
curl -fsSL https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/AGENTS.md -o AGENTS.md

# GitHub Copilot
mkdir -p .github
curl -fsSL https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/main/codex/copilot-instructions.md -o .github/copilot-instructions.md
```

## Updates

Pinning auf einen Tag empfohlen — DPF-Status, Schrems-III-Lage und Adäquanzbeschlüsse ändern sich:

```bash
curl -fsSL https://raw.githubusercontent.com/wuemaikblume/dsgvo-skills/v1.6/codex/AGENTS.md -o AGENTS.md
```

Releases auf <https://github.com/wuemaikblume/dsgvo-skills/releases>.

## Issues / PRs

Verbesserungen willkommen — speziell Praxis-Berichte, ob Codex/Copilot die Code-Generierungs-Regel (zwei Varianten zeigen) zuverlässig umsetzen oder ob die Anweisung schärfer formuliert werden muss.
