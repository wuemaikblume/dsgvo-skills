# DSGVO Skills für KI-Coding-Tools

Kostenlose Compliance-Pakete für **Claude AI / Claude Code**, **Cursor** und **OpenAI Codex CLI / GitHub Copilot** — speziell für **DACH-Entwickler**, die produktiv mit KI-Coding arbeiten und dabei DSGVO, NIS2 und nationale Datenschutzregeln im Blick behalten müssen.

> **Was ist ein Skill?** Eine Markdown-Datei mit YAML-Frontmatter, die das Tool bei passendem Kontext automatisch lädt. Bei Claude: [offizielle Anthropic-Doku](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview).

## Status (v1.8)

- ✅ **`/claude`** — Anthropic Skills für Claude Code, Claude.ai, Claude API. Drei Skill-Familien: Drittlandtransfer (+ 5 Vertiefungs-Dateien), Auth & Logging (+ 3 Vertiefungs-Dateien) und E-Mail-Marketing (+ 6 Vertiefungs-Dateien — inkl. `AI-CONTENT-AND-TRANSPARENCY.md` für EU AI Act Art. 50). v1.8 erweitert E-Mail-Marketing um WhatsApp/Messenger, Drittland-Outbound (CAN-SPAM/PECR/CASL/Spam Act), Kinder-Newsletter risikobasiert, Soft-Bounce als DSGVO-Pattern.
- ✅ **`/cursor`** — Fünfzehn `.cursor/rules/*.mdc` Agent-Requested-Rules.
- ✅ **`/codex`** — Konsolidierte `AGENTS.md` (Codex CLI) und `copilot-instructions.md` (GitHub Copilot), beide mit Drittlandtransfer-, Auth/Logging- und E-Mail-Marketing-Sektion (inkl. AI-Act Art. 50 + v1.8-Erweiterungen).

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
│       ├── dsgvo-auth-and-logging/
│       │   ├── SKILL.md            # Decision Tree, Code-Generation-Regel, Cross-Links
│       │   ├── AUTH-TOM.md         # Art. 32 TOMs: Hashing, Session, Cookies, MFA, Rate-Limit
│       │   ├── LOGGING.md          # Sentry/Datadog/Pino-Scrubbing, Audit-Log, Retention
│       │   └── IP-ADDRESSES.md     # IP=personenbezogen (Breyer), Kürzung, X-Forwarded-For
│       └── dsgvo-email-marketing/
│           ├── SKILL.md            # Decision Tree, Top-3-Code-Smells, Cross-Links
│           ├── CONSENT-AND-DOI.md  # Art. 6/7 Marketing-Kontext, DOI-Flow, Confirm-Mail
│           ├── UWG-7.md            # UWG § 7 (DE) + AT TKG § 174 + CH UWG Art. 3 I o
│           ├── TRACKING-IN-MAIL.md # Open-Pixel, Click-Redirect, externe Bilder
│           ├── UNSUBSCRIBE-AND-RETENTION.md  # One-Click, RFC 8058, Suppression-Hash
│           ├── SERVICE-VS-MARKETING.md       # BGH-Linie, Mail-Template-Klassifikation
│           └── AI-CONTENT-AND-TRANSPARENCY.md # EU AI Act Art. 50 + DSGVO + UWG-Schiene (neu v1.7)
├── cursor/                          # 14 .mdc Rules (Agent Requested)
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
│       ├── dsgvo-ip-addresses.mdc             # IP-Speicherung, Kürzung, Breyer
│       ├── dsgvo-email-marketing.mdc          # SKILL + CONSENT-AND-DOI gemerged
│       ├── dsgvo-uwg-7.mdc                    # UWG § 7 DE/AT/CH
│       ├── dsgvo-tracking-in-mail.mdc         # Open-Pixel, Click-Redirect, externe Bilder
│       ├── dsgvo-unsubscribe-and-retention.mdc # One-Click + RFC 8058
│       ├── dsgvo-service-vs-marketing.mdc     # BGH-Linie, Mail-Template-Klassifikation
│       └── dsgvo-ai-content-marketing.mdc     # EU AI Act Art. 50 + DSGVO + UWG (neu v1.7)
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

### `dsgvo-email-marketing` (v1.2, erweitert in v1.8)

Triggert bei werblicher E-Mail (Newsletter, Drip, Re-Engagement, Win-Back, Lead-Magnet) und an der Schnittstelle Service-Mail-vs-Werbung. Newsletter-Provider-SDKs (Mailchimp, Klaviyo, HubSpot, ActiveCampaign, Brevo, CleverReach, Rapidmail, MailerLite), DOI-Endpoints, Tracking-Pixel-Code, List-Unsubscribe-Header. **Seit v1.7:** AI-personalisierter Newsletter-Content (LLM-Subject-Lines, AI-Bilder, Reply-an-AI-Chatbots). **Seit v1.8:** WhatsApp/Messenger-Marketing, Drittland-Outbound (CAN-SPAM/PECR/CASL/Spam Act), Kinder-Newsletter risikobasiert, Soft-Bounce als DSGVO-Pattern.

**Was abgedeckt ist:**
- UWG § 7 (DE) inkl. § 7 II Nr. 3 + § 7 III Bestandskunden-Privileg, AT TKG 2021 § 174, CH UWG Art. 3 I o
- DSGVO Art. 6 I a + Art. 7 (Marketing-Einwilligung), Art. 7 IV (Kopplungsverbot), Art. 8 (Kinder), Art. 21 II (Direktwerbungs-Widerspruch)
- TDDDG § 25 (vormals TTDSG) für Tracking-Pixel + Click-Tracking-Einwilligung
- BGH-Linie zur Service-vs-Werbung-Abgrenzung: I ZR 218/07 (DOI-Beweislast), VI ZR 225/17 (Bewertungs-Bitte = Werbung), I ZR 164/09 (Confirm-Mail-Werbungsfreiheit), OLG Hamm 4 U 121/13 (Cross-Sell in Trans-Mail)
- Code-Patterns: DOI-Token + DB-Schema + Confirm-Endpoint, Mail-Template-Klassifikation (transactional vs marketing) mit Send-Guard, Suppression-Hash nach Unsubscribe, RFC 8058 List-Unsubscribe-Header
- Cold-B2B-Mailing: § 7 II Nr. 3 erfasst auch B2B; LinkedIn-/XING-DMs gelten als „elektronische Post" analog BGH I ZR 169/04
- Gmail/Yahoo Sender Requirements (Februar 2024) — DKIM/SPF/DMARC + RFC 8058 Pflicht für Bulk-Sender ≥ 5.000 Mails/Tag
- **Neu in v1.7 (Sub-Datei `AI-CONTENT-AND-TRANSPARENCY.md`):** EU AI Act Art. 50 ab 02.08.2026 anwendbar. Differenzierung Anbieter- vs. Betreiber-Pflichten (Abs. 1 Interaktion, Abs. 2 maschinenlesbare Markierung, Abs. 4 Deepfake + öffentliches Interesse, Werke-Ausnahme). Sanktionen bei Art. 50-Verstoß bis € 15 Mio. / 3 % weltweiter Konzern-Jahresumsatz (Art. 99 Abs. 4 lit. g). Eigene UWG-Schiene (§ 3a / § 5 / § 5a + Schwarze Liste) als Mitbewerber-Abmahnvektor.
- **Neu in v1.8** — vier Cross-Review-Befunde eingearbeitet:
  - **WhatsApp/Messenger-Marketing (UWG-7.md):** OLG Hamm 18 U 154/22 erfasst WhatsApp; Einwilligung pro Kanal; Channel-Follow nur für Channel-Updates; Bulk-Adressbuch-Upload regelmäßig unzulässig; App vs. API über BSP klar trennen.
  - **Drittland-Outbound (UWG-7.md):** Art. 49 ist NICHT der Anker bei Versand an Drittland-Privatempfänger; räumlicher DSGVO-Anwendungsbereich (Art. 3 Abs. 1) plus nationales Anti-Spam-Recht (CAN-SPAM Opt-out, UK PECR Reg. 22, CASL bis 10 Mio. CAD Unternehmen, Spam Act AU 5-Werktage-Frist).
  - **Kinder-Newsletter risikobasiert (CONSENT-AND-DOI.md):** Art. 8 Abs. 2 verlangt „angemessene Anstrengungen unter Berücksichtigung verfügbarer Technik"; EDPB 05/2020 lässt für Low-Risk-Verarbeitung Geburtsjahr + Eltern-Mail-Bestätigung ausdrücklich zu; bei höherem Risiko (Gaming, monetäre Trigger) Stufe 4–5 (Mikro-Zahlung, eID).
  - **Soft-Bounce als DSGVO-Anknüpfung (UNSUBSCRIBE-AND-RETENTION.md):** keine fixe Behörden-Schwelle; dokumentierte interne Policy mit Versionsnummer (typisch 5–10 / 14–30 Tage); Audit-Log mit Provider-Code, Policy-Version, Consent-Ref, Override-Feldern.
- **Tiefenrecherche-Review** für jeden Skill-Patch: Grok + GPT-5.5 + Gemini Deep Research, jeweils gegen Primärquellen (EUR-Lex, AI Act Service Desk, ICO, FTC, CRTC, ACMA, BGH/OLG-Aktenzeichen).

### Was bewusst NICHT abgedeckt ist (kommt als eigene Skills)

- `dsgvo-subject-rights` — Auskunft, Löschung, Portabilität (Art. 15-22)
- `dsgvo-personal-data-storage` — Verschlüsselung, Aufbewahrungsfristen, Backups
- `nis2-security-baseline` — NIS2-Mindestmaßnahmen, 24/72h-Meldepflichten
- `dsgvo-employment` — § 26 BDSG, Bewerber-Daten, HR-Tools

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
| `dsgvo-email-marketing` | ✅ v1.2 (erweitert) | UWG § 7 + DSGVO Art. 6/7/21 + TDDDG § 25 + EU AI Act Art. 50 + WhatsApp/Drittland-Outbound/Kinder-risikobasiert/Soft-Bounce (v1.8) + 6 Sub-Dateien |
| `dsgvo-subject-rights` | ⬜ | Auskunft, Löschung, Portabilität (Art. 15-22) |
| `dsgvo-personal-data-storage` | ⬜ | Verschlüsselung, Aufbewahrungsfristen, Backups |
| `nis2-security-baseline` | ⬜ | Mindestmaßnahmen, 24/72h-Meldepflichten |
| `dsgvo-employment` | ⬜ | § 26 BDSG, Bewerber, HR-Tools |

## Lizenz

[MIT](LICENSE) — frei nutzbar, auch kommerziell. Bitte Quelle nennen, wenn du Skills oder Teile davon verwendest.

## Stichtag

Alle Skills tragen **Stand Mai 2026**. Vor Einsatz aktuellen Stand der zitierten Quellen prüfen — die Markdown-Dateien werden nicht automatisch aktualisiert.
