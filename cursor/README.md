# DSGVO Rules für Cursor

Vierzehn **Agent Requested**-Rules im `.cursor/rules/*.mdc`-Format, die Cursor's Agent automatisch lädt, wenn DSGVO-Themen relevant werden — auch ohne dass der User „DSGVO" oder „GDPR" tippt.

**Stand:** v1.6, parallel zur Claude-Variante. Inhaltlich identisch, technisch an Cursor's Rules-Engine angepasst.

## Inhalt

| Regel | Inhalt |
|-------|--------|
| [`dsgvo-third-country-transfer.mdc`](rules/dsgvo-third-country-transfer.mdc) | Decision Tree für Art. 44-49, Anbieter-Profile, Code-Patterns, EU-Whitelist |
| [`dsgvo-roles.mdc`](rules/dsgvo-roles.mdc) | Controller / Processor / Joint (Art. 6, 9, 26, 28) |
| [`dsgvo-dpia.mdc`](rules/dsgvo-dpia.mdc) | DSFA-Pflicht, Schwellenanalyse, DSK-Muss-Liste |
| [`dsgvo-cloud-sovereignty.mdc`](rules/dsgvo-cloud-sovereignty.mdc) | Datensouveränität, Art. 48, US CLOUD Act, BCRs, EU-Alternativen |
| [`dsgvo-eprivacy-tracking.mdc`](rules/dsgvo-eprivacy-tracking.mdc) | TTDSG §25, Cookies, Tracking-Pixel, Consent |
| [`dsgvo-revdsg-ch.mdc`](rules/dsgvo-revdsg-ch.mdc) | Schweiz revDSG, Swiss-US DPF, EDÖB |
| [`dsgvo-auth-and-logging.mdc`](rules/dsgvo-auth-and-logging.mdc) | Auth-Code, Login/Logout, Audit-Logs, Sentry/Datadog/Pino-Scrubbing, Art. 5/6/25/32 |
| [`dsgvo-auth-tom.mdc`](rules/dsgvo-auth-tom.mdc) | Hash-Parameter (argon2id/bcrypt), Cookie-Flags, Session-Timeouts, MFA, Rate-Limits |
| [`dsgvo-ip-addresses.mdc`](rules/dsgvo-ip-addresses.mdc) | IP=personenbezogen (Breyer), Kürzung, X-Forwarded-For, Retention-Bänder |
| [`dsgvo-email-marketing.mdc`](rules/dsgvo-email-marketing.mdc) | Newsletter / Marketing-Mail / DOI-Flow / Confirm-Mail / BGH-Linie / Master-Rule + CONSENT-AND-DOI gebündelt |
| [`dsgvo-uwg-7.mdc`](rules/dsgvo-uwg-7.mdc) | UWG § 7 (DE) + AT TKG § 174 + CH UWG Art. 3 I o, Bestandskunden-Privileg, Cold-B2B inkl. LinkedIn-DM |
| [`dsgvo-tracking-in-mail.mdc`](rules/dsgvo-tracking-in-mail.mdc) | Open-Pixel, Click-Redirect, externe Bilder als Code-Pattern (TDDDG § 25 + DSK 2021) |
| [`dsgvo-unsubscribe-and-retention.mdc`](rules/dsgvo-unsubscribe-and-retention.mdc) | One-Click-Unsubscribe (RFC 8058), Suppression-Hash, Bounce, Aufbewahrungsfristen, Re-Permission |
| [`dsgvo-service-vs-marketing.mdc`](rules/dsgvo-service-vs-marketing.mdc) | Mail-Template-Klassifikation Service vs Werbung (BGH I ZR 218/07, VI ZR 225/17, I ZR 164/09, OLG Hamm) |

## Installation

Siehe [`INSTALL.md`](INSTALL.md). Kurzform:

```bash
git clone https://github.com/wuemaikblume/dsgvo-skills.git /tmp/dsgvo-skills
mkdir -p dein-projekt/.cursor/rules
cp /tmp/dsgvo-skills/cursor/rules/*.mdc dein-projekt/.cursor/rules/
```

## Unterschied zur Claude-Variante

Cursor unterstützt kein **Progressive Disclosure** wie Claude Skills. Daher sind die Anbieter-Profile aus `PROVIDERS.md` direkt in `dsgvo-third-country-transfer.mdc` integriert. Die anderen fünf Sub-Themen bleiben als eigene Regeln, damit Cursor sie kontextspezifisch laden kann.

Frontmatter-Schema (Cursor):

```yaml
---
description: <Trigger-Beschreibung — agent-readable>
globs:
alwaysApply: false
---
```

`alwaysApply: false` + starke `description` ist der Cursor-Pendant zu Anthropics auto-loadenden Skills.

## Issues / PRs

Verbesserungen willkommen — speziell:

- Praxis-Berichte: triggert eine Regel zu oft / zu selten?
- Branchenspezifische Ergänzungen (KRITIS, HealthTech, FinTech)
- DPF-Status-Updates für einzelne Anbieter

Repo: <https://github.com/wuemaikblume/dsgvo-skills>
