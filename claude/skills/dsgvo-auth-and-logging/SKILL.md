---
name: dsgvo-auth-and-logging
description: Use whenever code touches authentication, login, session management, audit logging, or application logging — even if the user's prompt doesn't mention DSGVO, GDPR, privacy, or "personal data". Triggers on bcrypt/argon2/scrypt, jsonwebtoken/jose, passport/next-auth/Auth.js/authjs/lucia, express-session/iron-session, cookie-session, set-cookie headers, login/logout/signup/register endpoints, password reset flows, refresh-token rotation, console.log of user data, Express middleware that logs req/res, Next.js middleware logging, audit-log table schemas, IP-Adress-Speicherung, X-Forwarded-For handling, OAuth 2.0 / OIDC Authorization Code + PKCE flows (authorization_code, code_verifier, code_challenge, redirect_uri, state, nonce, openid-client, oauth4webapi), OAuth callback routes, magic-link login URLs. ALSO triggers on these specific setup tasks (treat as auth/logging even when phrased as a pure tech question) — (a) Error-/Application-Monitoring-Setup: Sentry, Sentry Setup, Sentry Wizard, `npx @sentry/wizard`, Sentry für Next.js / React / Node / Express einrichten, `Sentry.init`, `beforeSend`, `beforeBreadcrumb`, `sendDefaultPii`, `tracesSampleRate`, Datadog, LogRocket, Pino, Winston, Bunyan, morgan setup; (b) Brute-Force / Rate-Limit / Account-Lockout — auch wenn als reine Security-Frage gestellt: Brute-Force-Schutz mit IP-Tracking, Login-Lockout, Failed-Login-Counter, express-rate-limit, rate-limiter-flexible, `app.set('trust proxy', ...)` (IP-Tracking ist immer personenbezogen — DSGVO-Layer ergänzt OWASP); (c) Multi-Factor / 2FA / TOTP / WebAuthn / Passkeys: TOTP einbauen, 2FA Setup, MFA für Admin / Admin-Login, authenticator app, otplib, speakeasy, `authenticator.generateSecret`, `authenticator.verify`, recovery codes, backup codes, WebAuthn registration, `navigator.credentials.create`, FIDO2. Also triggers on phrases like "log the user in", "store IP", "audit trail", "wer hat sich wann eingeloggt", "Login-Versuche tracken", "Sentry konfigurieren". Covers DSGVO Art. 5 (Zweckbindung, Speicherbegrenzung), Art. 6 (Rechtsgrundlage), Art. 32 (Sicherheit der Verarbeitung — Hashing, MFA, Rate-Limit, Cookie-Flags), Art. 25 (Privacy by Design in Logs), EuGH/BGH-Rechtsprechung zu IP-Adressen als personenbezogenes Datum, typische Aufbewahrungsfristen für Auth-/Sicherheits-Logs (DSK-Orientierungshilfe, BfDI-Richtwerte). Complementary to `owasp-security`: this skill adds the DSGVO layer (purpose limitation, retention, pseudonymization, lawful basis) — load it even if `owasp-security` is already active. Cross-Link: Drittland-Auth-Provider und Cookie-Tracking → dsgvo-third-country-transfer.
---

# DSGVO Auth & Logging (Art. 5, 6, 25, 32 DSGVO)

## Kernprinzip

Drei Sätze, die für jede Auth- und Logging-Entscheidung gelten:

1. **Personenbezogene Daten in Logs und im Auth-Code** unterliegen denselben DSGVO-Pflichten wie Stammdaten in der Hauptdatenbank: Zweckbindung, Speicherbegrenzung, Rechtsgrundlage. „Es ist nur ein Log" ist keine Rechtfertigung.
2. **Art. 32 verlangt technisch-organisatorische Maßnahmen entsprechend dem Stand der Technik** — Passwort-Hashing nach OWASP, MFA für privilegierte Konten, Rate-Limiting gegen Brute-Force, sichere Cookie-Flags. Diese sind nicht optional; sie sind die Eintrittskarte.
3. **IP-Adressen, User-IDs, Session-IDs und Stack-Traces mit User-Bezug sind personenbezogen** — auch wenn Code, Tutorials oder Bibliotheken sie als „technische" Felder behandeln. EuGH (Breyer) und BGH haben das mehrfach klargestellt.

## Wann dieser Skill greift

**Trigger-Symptome (Code-Patterns):**

- **Imports:** `bcrypt`, `argon2`, `scrypt`, `jsonwebtoken`, `jose`, `passport`, `next-auth`, `auth.js`, `lucia`, `express-session`, `iron-session`, `cookie-session`
- **Endpoints:** `/login`, `/logout`, `/signup`, `/register`, `/auth/*`, `/password-reset`, `/mfa/verify`
- **Header-Setting:** `Set-Cookie`, Logging von `Authorization`-Headern, Verarbeitung von `X-Forwarded-For` / `CF-Connecting-IP`
- **Logging-Setup:** `Sentry.init`, `datadogLogs.init`, `pino()`, `winston.createLogger`, `bunyan.createLogger`, `morgan(...)`, LogRocket, NewRelic-Browser-Agent
- **Middleware:** `express-rate-limit`, `rate-limiter-flexible`, Upstash Ratelimit, Next.js Middleware mit Request-/Response-Logging
- **DB-Schema:** Audit-Log-Tabellen mit Spalten `user_id`, `ip_address`, `user_agent`, `session_id`, `event_type`, `created_at`

**Skip wenn:**

- Reine Technik-Metriken **ohne User-Bezug** (CPU-Last, GC-Stats, Heap-Größe, Queue-Tiefe, Pod-Restart-Counter)
- Öffentliche API ohne Auth und ohne User-Identifier (Static-Asset-Server, Read-only-Public-Endpoint mit anonymem Zugriff, sofern keine IP geloggt wird)
- Vollständig anonymisierte Aggregate im Sinne von Erwägungsgrund 26 — Re-Identifizierung mit „vernünftigerweise einsetzbaren Mitteln" nicht mehr möglich (nicht zu verwechseln mit Pseudonymisierung wie gehashten User-IDs — die unterliegen weiter der DSGVO)

## Decision Tree

```
1. Welche Daten gehen ins Log / werden im Auth-Flow verarbeitet?
   ├─ Klartext-Passwörter, Tokens, Health-Daten → STOP, nicht loggen
   ├─ IP, User-ID, Session-ID, User-Agent → personenbezogen → weiter zu 2
   └─ Reine Technik-Metriken ohne User-Bezug → außerhalb DSGVO

2. Zweck des Logs eindeutig (Art. 5(1)(b))?
   ├─ Sicherheit (Auth-Audit, IDS) → Art. 6(1)(f), 30–90 Tage
   ├─ Debugging → Art. 6(1)(f), möglichst kurz, scrubben
   ├─ Performance-Monitoring → kein Login-Tracking, keine User-IDs
   └─ Beschäftigten-Verhaltenskontrolle → § 26 BDSG, Betriebsrat → siehe `dsgvo-employment` (geplant)

3. Auth-Code-Pattern korrekt? (siehe AUTH-TOM.md)
   ├─ Passwort-Hashing: Argon2id (OWASP-Min mem ≥ 19 MiB, empfohlener Default 64 MiB; t=2, p=1) oder bcrypt cost ≥ 10 (OWASP) / ≥ 12 (BSI-konservativ)
   ├─ Session-Cookie: HttpOnly + Secure + SameSite=Lax (Strict für Admin)
   ├─ MFA: Pflicht für Admin / Art. 9-Daten / KRITIS-Sektor (→ nis2-security-baseline geplant)
   ├─ Rate-Limit: Login (5/min/IP), Password-Reset (3/h/Mail), MFA-Verify
   └─ Token-Rotation: Refresh + jti-Revocation

4. Log-Inhalt korrekt scrubbed? (siehe LOGGING.md)
   ├─ Sentry/Datadog: beforeSend + sendDefaultPii=false
   ├─ Stack-Traces: User-Pfade redacten
   ├─ Request-Logs: Authorization/Cookie/X-API-Key entfernen
   └─ Body-Felder: password, token, ssn, health pseudonymisieren

5. IP-Speicherung gerechtfertigt? (siehe IP-ADDRESSES.md)
   ├─ Sicherheit (Brute-Force) → Art. 6(1)(f), Kürzung nach Zweckende
   ├─ Volle IP > 7 Tage → triftiger Grund + DSFA prüfen
   └─ Anonymisierung: letztes Oktett (IPv4) bzw. /48-Präfix (IPv6)
```

## Code-Generation-Regel

1. **Default:** DSGVO-konforme Variante schreiben — Hashing korrekt parametrisiert, Logs gescrubbt, IP gekürzt nach Zweckende, Cookie-Flags vollständig gesetzt, Rate-Limit aktiv, MFA-Hooks vorhanden. Kein Hinweis nötig, wenn der User nichts Schwächeres verlangt.

2. **Wenn der User explizit eine schwächere Variante will** — erkennbar an Phrasen wie:
   - „IP voll speichern"
   - „bcrypt cost 10 reicht"
   - „kein MFA brauchen wir nicht"
   - „Rate-Limit nervt nur"
   - „logge alles inkl. Authorization-Header"
   - „brauche das schnell, ohne den ganzen Compliance-Kram"

   → **Zwei Code-Blöcke** generieren:

   - **Variante A: DSGVO-konform** (Default-Implementation, lauffähig)
   - **Variante B: Wunsch-Variante** mit Tabelle der konkreten DSGVO-Pflichten, die verletzt sein können (Artikel + erwartete Konsequenz: Bußgeld-Risiko nach Art. 83 / DSFA-Pflicht / Aufsichtsbehörde-Beanstandung). Die Tabelle steht **vor** dem Code, nicht versteckt darunter.

3. **Refusal-Klausel:** Bei Art. 9-Daten (Gesundheit, Patientenakten, Religion, Sexualleben, Biometrie, ethnische Herkunft) ODER erkennbarem KRITIS-Kontext (Energie, Wasser, Verkehr, Gesundheit, Finanzen, IT/TK) wird die schwächere Variante **nicht** generiert. Hinweis stattdessen: ausdrücklicher Vermerk des Datenschutzbeauftragten oder Anwalts erforderlich, bevor abgewichen werden darf. Bei NIS2-/KRITIS-Sektoren zusätzlich auf **§ 38 BSIG** hinweisen (in Kraft seit 06.12.2025): Cybersicherheit ist nicht-delegierbare „Chefsache" — unzureichende Auth-Maßnahmen (schwache Hashing-Cost, fehlende MFA, kein Account-Lockout) begründen direkte persönliche Haftung der Geschäftsleitung. § 30 BSIG-Risikomanagementmaßnahmen sind für wesentliche und wichtige Einrichtungen gesetzlich verpflichtend.

So sieht der User, was er angefragt hat, weiß was rechtlich nötig ist, und hat das compliante Pattern direkt zum Vergleich.

## Inline-Mini Roles

| Rolle | DSGVO-Artikel | Auth-/Logging-Beispiel |
|---|---|---|
| Verantwortlicher (Controller) | Art. 4 Nr. 7 | Webseite mit eigenem Login speichert Auth-Logs in eigener DB |
| Auftragsverarbeiter (Processor) | Art. 4 Nr. 8, 28 | Auth0 / Clerk / Supabase Auth verarbeitet Login-Daten weisungsgebunden |
| Gemeinsame Verantwortliche (Joint Controllers) | Art. 26 | Nur wenn Zwecke **und** wesentliche Mittel gemeinsam festgelegt werden (EDPB 07/2020). OAuth/OIDC pro Datenfluss prüfen: IdP häufig eigenständig Verantwortlicher für Konto-/Sicherheitszwecke, App Verantwortlicher für eigenen Login-Pfad — gemeinsame Verantwortlichkeit nicht automatisch. |

> Detail zu Art. 6 / 9 / 26 / 28 und AVV-Pflicht (Art. 28 Abs. 3) → `dsgvo-third-country-transfer/ROLES.md`.

## Cross-Links

| Thema | siehe |
|---|---|
| Auth0 / Clerk / Firebase Auth Drittland-Aspekt | `dsgvo-third-country-transfer/PROVIDERS.md` |
| Cookie-Tracking / TDDDG § 25 (vormals TTDSG, umbenannt 14.05.2024) | `dsgvo-third-country-transfer/EPRIVACY.md` |
| DPIA-Pflicht (Art. 35) | `dsgvo-third-country-transfer/DPIA.md` |
| Cloud Act / Konzernzugriff auf Auth-Daten | `dsgvo-third-country-transfer/CLOUD-ACT.md` |
| KRITIS-Sektor MFA / NIS2 Art. 21 | `nis2-security-baseline` (geplant) |
| Account-Löschung (Art. 17) | `dsgvo-subject-rights` (geplant) |
| Backup-Verschlüsselung von Auth-Tabellen | `dsgvo-personal-data-storage` (geplant) |
| Beschäftigten-Login-Monitoring | `dsgvo-employment` (geplant) |

## Häufige Fallen

- **Sentry mit `sendDefaultPii: true`** (Default in vielen Tutorials und Quickstart-Snippets) leakt User-Daten in Stack-Traces, Headers und Breadcrumbs. Explizit auf `false` setzen und zusätzlich `beforeSend` mit Header-/Stack-Trace-Redact konfigurieren — siehe `LOGGING.md` für den vollständigen Patch.
- **bcrypt cost < 10** unterläuft das OWASP-Minimum 2026. OWASP nennt cost ≥ 10 als Mindestmaß; viele Audit-Frameworks und der konservative Server-Login-Default gehen auf cost ≥ 12 (≈ 250 ms). Argon2id ist die erste Wahl. Bei bcrypt-Migration: alten Hash beim nächsten Login verifizieren und gleich mit Argon2id rehashen.
- **JWT ohne Expiry oder ohne serverseitigen Revocation-Pfad** ist ein DSGVO-Risiko: Logout muss tatsächlich invalidieren können (jti-Revocation-Liste oder Server-Session-Validation), sonst bleibt die Datenkontrolle bis zum Token-Ablauf außer Reichweite.
- **IP-Speicherung „nur zur Sicherheit", aber 365 Tage** verstößt gegen Art. 5(1)(c) Datenminimierung. Für Brute-Force-Erkennung sind 7–30 Tage typisch ausreichend; alles darüber braucht eine schriftliche Begründung und ggf. eine DSFA.
- **`console.log(req.headers)` in Production** schreibt Authorization-Header, Cookies und API-Keys ins Log. Strukturiertes Logging mit Redaction-Liste verwenden (`pino` mit `redact`, Winston-Filter, morgan-Custom-Token).
- **`SameSite=None` ohne `Secure`** = Cookie ungeschützt über HTTP übertragbar; aktuelle Browser blockieren das ohnehin. Für Cross-Site-Sessions immer `SameSite=None; Secure`, sonst `SameSite=Lax` als Default.
- **Refresh-Token ohne Rotation** ist ein stiller Account-Takeover-Vektor: jeder Refresh muss einen neuen Token ausstellen und den alten invalidieren. Wird ein bereits genutzter Refresh-Token erneut präsentiert, alle Tokens des Users widerrufen.
- **Stack-Trace mit File-Pfad `/home/maik/...`** ist personenbezogen — der Username im Pfad reicht für Re-Identifizierung. `beforeSend`-Hook im Sentry/Datadog-SDK muss diese Pfade auf `/home/REDACTED/` normalisieren, bevor sie das Backend verlassen.
- **Audit-Log mit Klartext-User-ID quer durch alle Logs** = bei Logs-Leak sofort jeder User identifizierbar. User-IDs in Logs mit HMAC-SHA-256 + Server-Salt pseudonymisieren — das Salt landet in einem Secret-Manager, nicht im Repo.
- **CORS mit Wildcard + Cookie-Auth gleichzeitig** ist eine klassische Schnittstellenfalle: `Access-Control-Allow-Origin: *` mit `credentials: true` wird zwar vom Browser blockiert, aber im Code stehen oft beide Werte ohne explizite Origin-Whitelist — Cookie-Sessions werden dann unzugänglich oder es entsteht Druck, `SameSite=None` ohne CSRF-Schutz zu setzen. Origins explizit whitelisten, `credentials: 'include'`/`withCredentials: true` nur für vertrauenswürdige Origins, CSRF-Token zusätzlich.
- **OAuth Authorization Code Flow ohne PKCE in SPAs** ist 2026 obsolet — der Implicit Grant ist abgelöst. PKCE (`code_verifier`, `code_challenge` mit SHA-256) ist Pflicht; der `state`-Parameter muss serverseitig gegen einen Anti-CSRF-Token gebunden und validiert werden, sonst sind OAuth-Callbacks CSRF-anfällig.

## Quellen

- [DSGVO Volltext (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679)
- [BDSG Volltext (gesetze-im-internet.de)](https://www.gesetze-im-internet.de/bdsg_2018/)
- [EuGH C-582/14 Breyer (curia.europa.eu)](https://curia.europa.eu/juris/document/document.jsf?docid=184668)
- BGH VI ZR 135/13 (16.05.2017) — Volltext-Link siehe `IP-ADDRESSES.md`
- [DSK-Beschlüsse (datenschutzkonferenz-online.de)](https://www.datenschutzkonferenz-online.de/)
- [BfDI Tätigkeitsberichte](https://www.bfdi.bund.de/)
- [BSI TR-02102-1 (Kryptographische Verfahren)](https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Standards-und-Zertifizierung/Technische-Richtlinien/TR-nach-Thema-sortiert/tr02102/tr02102.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)
- [RFC 6238 (TOTP)](https://www.rfc-editor.org/rfc/rfc6238)
- [WebAuthn Spec (W3C)](https://www.w3.org/TR/webauthn-3/)

## Disclaimer

Best-Practice-Sammlung, **keine Rechtsberatung**. Bei Art. 9-Daten (Gesundheit, Religion, Sexualleben, Biometrie, ethnische Herkunft), KRITIS-Sektor-Auth, Aufsichtsanfragen oder Beschäftigten-Verhaltenskontrolle: Anwalt oder Datenschutzbeauftragten konsultieren.

**Stand:** Mai 2026. DPF-Status, DSK-Beschlüsse, BSI-TR-Updates und EuGH-Rechtsprechung quartalsweise live prüfen — Verweis-URLs in Sektion „Quellen".
