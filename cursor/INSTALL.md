# Cursor — Installation

Diese Cursor-Adaption liefert vierzehn Regeln im `.cursor/rules/`-Format. Alle sind als **Agent Requested**-Rules konfiguriert (`alwaysApply: false`) — Cursor's Agent lädt die Regel nur, wenn die `description` zur Aufgabe passt. Das spart Kontext und vermeidet Fehl-Trigger.

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
| `dsgvo-auth-and-logging` | Auth-Code (bcrypt/argon2/JWT), Login-Endpoints, Sentry/Datadog/Pino-Setup, Audit-Log mit IP, Brute-Force-Schutz, MFA/TOTP/WebAuthn |
| `dsgvo-auth-tom` | Konkrete TOM-Parameter (Hash-Cost, Session-Timeouts, Cookie-Flag-Matrix, MFA-Pflichtfälle, Rate-Limit-Werte) |
| `dsgvo-ip-addresses` | IP-Speicherung, X-Forwarded-For, IP-Kürzung (/24, /48), Pseudonymisierung, Retention-Bänder |
| `dsgvo-email-marketing` | Newsletter / Marketing-Mail-Code (Mailchimp/Klaviyo/HubSpot/Brevo/CleverReach/Rapidmail), DOI-Flow, Confirm-Mail, Tracking-Pixel, Unsubscribe (Master-Rule mit CONSENT-AND-DOI gebündelt) |
| `dsgvo-uwg-7` | UWG § 7 (DE), AT TKG § 174, CH UWG Art. 3 I o, Bestandskunden-Privileg, Cold-B2B-Mailing inkl. LinkedIn-DM |
| `dsgvo-tracking-in-mail` | Open-Tracking-Pixel, Click-Redirect, externe Bilder im Mail-Template (TDDDG § 25 + DSK 2021) |
| `dsgvo-unsubscribe-and-retention` | One-Click-Unsubscribe (RFC 8058), List-Unsubscribe-Header, Suppression-Hash, Bounce, Aufbewahrung, Re-Permission |
| `dsgvo-service-vs-marketing` | Mail-Template-Klassifikation (transactional vs marketing), BGH-Linie zu Cross-Sell + Bewertungs-Bitte + Confirm-Mail-Werbungsfreiheit |

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
