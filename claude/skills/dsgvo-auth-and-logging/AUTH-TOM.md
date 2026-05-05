# Auth — Technische und organisatorische Maßnahmen (Art. 32 DSGVO)

> Sub-File zu `dsgvo-auth-and-logging`. Best-Practice, **keine Rechtsberatung**. Stand Mai 2026.

Diese Datei liefert konkrete TOM-Parameter für den Auth-Pfad: Hashing-Algorithmen mit Memory-/Cost-Werten, Session-Strategien mit Timeouts, Cookie-Flag-Matrix, MFA-Pflichtfälle, Rate-Limit-Werte und das Refresh-Token-Rotation-Pattern. Scrubbing-Patterns für Logs wohnen in `LOGGING.md`, IP-Anonymisierung (Kürzung, X-Forwarded-For-Sicherung) in `IP-ADDRESSES.md`.

## Passwort-Hashing

OWASP-Empfehlungen (Stand 2024) für Server-Login.

**Argon2id** — erste Wahl:

- **OWASP-Mindeststandard** (Stand 2026): `m=19 MiB, t=2, p=1` (alternativ `m=46 MiB, t=1, p=1`). Mehrere kleinere Profile zulässig, solange die Verifizierung ≥ ~250 ms bleibt.
- **Empfohlener Default für Server-Login mit ausreichend RAM:** `m=64 MiB, t=2, p=1` — konservativ über dem OWASP-Minimum, gibt Reserve für künftige Hardware-Beschleunigung.
- Bei mobilen Apps mit lokaler Verifikation auf dem Gerät reicht das OWASP-Minimum von 19 MiB.
- `t` (Time / Iterations) = 2
- `p` (Parallelism) = 1
- Salt 16 Byte zufällig, **von der Lib** generiert — nicht selbst konstruieren, nicht deterministisch ableiten.
- Argon2-Hash ist self-contained (Format `$argon2id$v=19$m=…,t=…,p=…$salt$hash`) — keine separate Salt-Spalte in der DB nötig, ein einziges `VARCHAR(128)` reicht.

```js
const argon2 = require('argon2');

const hash = await argon2.hash(password, {
  type: argon2.argon2id,
  memoryCost: 65536, // 64 MiB
  timeCost: 2,
  parallelism: 1,
});

// Login-Verify:
const ok = await argon2.verify(hash, candidatePassword);
```

**bcrypt** — Fallback, wenn Argon2id nicht verfügbar:

- OWASP-Mindeststandard: cost ≥ 10. Konservativer Default für Server-Login: cost ≥ 12 (≈ 250 ms auf modernem Server-Core).
- **bcrypt 72-Byte-Grenze:** Eingaben länger als 72 Byte werden stillschweigend abgeschnitten. Lange Passphrasen oder Pre-Hashing nötig — sonst sind alle Passwörter mit gleichem 72-Byte-Präfix äquivalent. Argon2id hat diese Grenze nicht.
- Migrations-Pfad bei bestehender bcrypt-DB: bei jedem erfolgreichen Login mit bcrypt verifizieren, dann mit Argon2id neu hashen, neuen Hash speichern, alten Hash überschreiben.

```js
const bcrypt = require('bcrypt');
const hash = await bcrypt.hash(password, 12);
const ok = await bcrypt.compare(candidatePassword, hash);
```

**scrypt** — nur Legacy / FIPS-Anforderungen: `N = 2^17`, `r = 8`, `p = 1`.

**Anti-Patterns:**

- SHA-256 / MD5 / „salted SHA" → keine Stretching-Funktion, in Sekunden brute-forced.
- PBKDF2 mit < 600.000 Iterationen → unter Stand der Technik (OWASP 2024).
- Eigene Hash-Konstruktion (z.B. SHA-256 in Loop) → niemals.
- Klartext-Speicherung in „Password-Reset-Token-Tabellen" → Reset-Tokens auch hashen (wie Passwörter), Vergleich nur per `crypto.timingSafeEqual`.
- **Composition Rules** (Sonderzeichen-/Zahlen-/Großbuchstaben-Zwang) → laut NIST SP 800-63B-4 ein Anti-Pattern und strikt zu vermeiden („shall not"), siehe Passwort-Policy unten.
- **Periodischer anlassloser Passwortwechsel** (z.B. „alle 90 Tage") → laut NIST SP 800-63B-4 ausdrücklich verworfen, da er die Passwortqualität senkt.

## Passwort-Policy nach NIST SP 800-63B-4

NIST SP 800-63B-4 (Final, 31.07.2025) hat die Passwort-Anforderungen radikal vereinfacht. Stand der Technik nach Art. 32 DSGVO:

- **Mindestlänge:** 15 Zeichen, wenn Passwort der einzige Faktor ist; 8 Zeichen mit verpflichtender MFA.
- **Maximallänge:** mindestens 64 Zeichen zulassen — nicht künstlich beschneiden.
- **Keine Composition Rules** wie „mindestens 1 Sonderzeichen, 1 Zahl, 1 Großbuchstabe". Empirisch belegt produzieren sie vorhersehbare Muster wie `Passwort123!`.
- **Kein periodischer Wechsel** ohne konkreten Kompromittierungsverdacht. Erzwungener Wechsel führt zu inkrementellen Mustern (`Sommer2026!` → `Herbst2026!`).
- **Blocklist-Screening verpflichtend:** neue oder geänderte Passwörter in Echtzeit gegen Datenbanken bekannter kompromittierter Passwörter (z.B. [HaveIBeenPwned-API](https://haveibeenpwned.com/API/v3#PwnedPasswords)) abgleichen, plus wörterbuchbasierte oder kontextspezifische Muster (App-Name, User-E-Mail, Firmenname) ablehnen.
- **Passwortmanager und Copy/Paste nicht blockieren** — die Praxis, das Einfügen in Passwortfelder zu unterbinden, untergräbt das einzige verlässliche Werkzeug für gute Passwörter.

```js
// Beispiel: Blocklist-Check via Have I Been Pwned k-Anonymity API
const crypto = require('crypto');

async function isPasswordPwned(password) {
  const sha1 = crypto.createHash('sha1').update(password).digest('hex').toUpperCase();
  const prefix = sha1.slice(0, 5);
  const suffix = sha1.slice(5);
  const res = await fetch(`https://api.pwnedpasswords.com/range/${prefix}`);
  const text = await res.text();
  return text.split('\n').some(line => line.split(':')[0].trim() === suffix);
}
```

Die Hashing-Lib selbst kümmert sich um Stretching (Argon2id/bcrypt). Die Policy oben kümmert sich um die Eingabe-Qualität — beides nötig.

## Session-Management

Drei Strategien, ein Pattern pro Anwendungstyp.

**Server-Session (Redis/DB)** — Default für Web-Apps mit Cookie-Auth:

- Logout invalidiert sofort durch Eintrag-Löschen.
- Idle-Timeout im Server geprüft, nicht im Cookie.
- Session-ID kryptographisch zufällig (≥ 128 Bit), keine sequenzielle ID.

```js
const session = require('express-session');
const { RedisStore } = require('connect-redis'); // v8+ benannter Export; v7 nutzte `.default`

app.use(session({
  store: new RedisStore({ client: redis }),
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,
    secure: true,
    sameSite: 'lax',
    maxAge: 1000 * 60 * 30, // 30 min Idle
  },
  rolling: true, // Idle-Timeout aktiv
}));
```

**JWT (kurzlebig) + Refresh-Token** — für API + SPA / mehrere Clients:

- Access-Token-Lifetime ≤ 15 Minuten.
- Refresh-Token mit `jti` (JWT-ID) auf Server-Revocation-Liste.
- Logout heißt: alle Refresh-Tokens des Users in Revocation-Liste eintragen.

**Hybrid** — kurzer JWT + Server-Session-Validation: Hochsicherheits-Setups mit zentraler Logout-Pflicht (z.B. Admin-Panels, Banking).

**Timeouts:**

| Kontext | Idle-Timeout | Absolut-Timeout |
|---|---|---|
| Standard-Web | 30 min | 8 h |
| Admin / Hochsicherheit | 15 min | 1 h |
| Art. 9-Daten / KRITIS | 10 min | 30 min |

## Cookie-Flags

| Flag | Wert | Wann |
|---|---|---|
| `HttpOnly` | `true` | immer (verhindert JS-Zugriff, mitigiert XSS) |
| `Secure` | `true` | immer in Production (HTTPS-Pflicht) |
| `SameSite` | `Lax` | Default (CSRF-Schutz, Browser-Navigation OK) |
| `SameSite` | `Strict` | Admin / Hochsicherheit (kein Cross-Origin) |
| `__Host-` Präfix | im Cookie-Namen | wenn ohne Subdomain-Zugriff (verhindert Subdomain-Hijacking) |
| `Domain` | nicht setzen | wenn `__Host-` aktiv |
| `Path` | `/` | außer bewusst eingeschränkt |
| `Max-Age` | < absoluter Session-Timeout | nicht „session-only" für Auth |

> Snippet zeigt den restriktivsten Standard (Single-Host, kein Subdomain-Sharing). Bei Multi-Subdomain-Setups (z.B. `auth.example.com` ↔ `app.example.com`) den `__Host-`-Präfix entfernen und stattdessen `domain: '.example.com'` setzen — `__Host-` schließt Domain-Sharing aus.

```js
res.cookie('__Host-session', sid, {
  httpOnly: true,
  secure: true,
  sameSite: 'lax',
  path: '/',
  maxAge: 1000 * 60 * 30,
});
```

> `SameSite=None` erfordert `Secure`. `SameSite=None` ohne `Secure` wird von Browsern verworfen (RFC 6265bis). Cross-Origin-Cookies nur für bewusste Cross-Site-Auth (z.B. eingebettete Widgets) — sonst `Lax` oder `Strict`.
>
> OAuth-Sonderfall: Bei OAuth-Redirects (Provider → eigene Callback-URL) wird das Session-Cookie unter `Lax` zwar bei GET-Redirects mitgeschickt, aber nicht bei POST-Callbacks. Den OAuth-State-Cookie deshalb explizit `SameSite=None; Secure` setzen, da der Provider auf eigener Domain läuft.

## MFA-Pflichten

MFA ist bei Verarbeitungen mit hohem Risiko nach DSK-Orientierungshilfe nicht nur Empfehlung, sondern notwendige Art.-32-Maßnahme. Für privilegierte Konten ist sie regelmäßig faktische DSGVO-Pflicht — die genaue Schwelle hängt vom konkreten Personenbezug ab:

- **Admin-Konten mit Zugriff auf personenbezogene Daten in relevantem Umfang, Produktivdatenbanken oder Sicherheitskonfigurationen** — MFA als Stand der Technik. Aufsichtsbehörden erwarten sie regelmäßig (DSK-Position Mehrfaktor-Authentifizierung).
- **Konten mit Zugriff auf Art. 9-Daten** (Gesundheit, Patientenakten, biometrische Daten) — Art. 32 in Verbindung mit Art. 9, faktisch Pflicht ohne explizite gesetzliche Einzelvorgabe.
- **KRITIS-Sektor-/NIS2-Konten** — § 30 BSIG (in Kraft seit 06.12.2025) listet MFA bzw. kontinuierliche Authentifizierung ausdrücklich als Risikomanagementmaßnahme; gesetzliche Pflicht für wesentliche und wichtige Einrichtungen. Detail im geplanten `nis2-security-baseline`-Skill.
- **Berufsgeheimnisträger-Daten** (§ 203 StGB: Ärzte, Anwälte, Steuerberater, Therapeuten) — siehe `dsgvo-third-country-transfer/CLOUD-ACT.md` für die Cloud-Aspekte.

Für rein technische Admin-Accounts ohne Personenbezug (z.B. Build-/Deploy-Agent ohne Datenzugriff) ist MFA nicht zwingend Pflicht aus DSGVO, aber bleibt anerkannte TOM gegen Lateral Movement.

**Methoden-Empfehlung:**

- **WebAuthn / FIDO2** (Hardware-Key oder Plattform-Authenticator wie Touch-ID, Windows Hello, Passkeys) — Gold-Standard, Phishing-resistent. Server-Side via `@simplewebauthn/server` (siehe Snippet unten).
- **TOTP RFC 6238** (Authenticator-Apps wie Aegis, 2FAS, Google Authenticator) — Standard, breit kompatibel.
- **SMS** — nur Fallback. NIST SP 800-63B-4 (Final, 31.07.2025) klassifiziert SMS/PSTN als „restricted authenticator" und verlangt: dokumentierter Migrationsplan, User-Information, alternative Methode anbieten, Risk-Assessment. SS7-Angriffe und SIM-Swap sind die treibenden Risiken.
- **E-Mail-OTP** — **nicht** als MFA-Faktor verwenden. NIST SP 800-63B-4 schließt E-Mail für Out-of-Band-Authentifizierung ausdrücklich aus. E-Mail bleibt zulässig für Verifikation, Benachrichtigung oder Account-Recovery, ersetzt aber keinen zweiten Faktor.

**WebAuthn-Server-Pattern** (`@simplewebauthn/server`):

```js
const {
  generateAuthenticationOptions,
  verifyAuthenticationResponse,
} = require('@simplewebauthn/server');

// Login-Schritt 1: Challenge erzeugen, kurzlebig server-side speichern.
const options = await generateAuthenticationOptions({
  rpID: 'app.example.com',
  userVerification: 'required', // KRITIS / Art. 9: 'required', sonst 'preferred'
  allowCredentials: userCredentials.map((c) => ({ id: c.credentialID, type: 'public-key' })),
});
await session.set('current-challenge', options.challenge); // einmalig, mit TTL ≤ 5 min

// Login-Schritt 2: Antwort des Authenticators verifizieren.
const verification = await verifyAuthenticationResponse({
  response: req.body, // Signed Response vom Browser
  expectedChallenge: await session.get('current-challenge'),
  expectedOrigin: 'https://app.example.com',
  expectedRPID: 'app.example.com',
  authenticator: storedAuthenticator, // mit credentialPublicKey, counter
  requireUserVerification: true,
});

if (!verification.verified) throw new Error('WebAuthn-Verifikation fehlgeschlagen');
// Sign-Counter aktualisieren — Clone-Detection bei Counter-Rückschritt
if (verification.authenticationInfo.newCounter <= storedAuthenticator.counter) {
  // potentiell geklonter Authenticator → Account sperren, User informieren
}
```

Pflicht-Aspekte: Challenge **einmalig + kurzlebig** server-side, `expectedOrigin`/`expectedRPID` strikt prüfen, Sign-Counter persistieren und bei Rückschritt auf Clone reagieren. Privacy-Hinweis: gespeicherte `credentialID` + Public Key sind unveränderliche Device-Footprints — bei Account-Löschung mit-löschen.

**Backup-Codes:** 10 Einmalcodes, **Argon2id-gehasht** wie Passwörter, einzeln invalidiert nach Verbrauch. Klartext-Speicherung wäre identisch riskant wie Klartext-Passwörter.

```js
const crypto = require('crypto');
const argon2 = require('argon2');

async function generateBackupCodes() {
  const codes = Array.from({ length: 10 }, () =>
    crypto.randomBytes(8).toString('hex'), // 16 Hex-Chars, 64 Bit (OWASP-Min für 2FA-Recovery)
  );
  const hashes = await Promise.all(codes.map((c) => argon2.hash(c, { type: argon2.argon2id })));
  // codes (Klartext) → einmalig dem User anzeigen
  // hashes → in DB speichern, beim Verbrauch einzeln auf used=true setzen
  return { codes, hashes };
}
```

## Rate-Limit-Werte

| Endpoint | Limit | Pro |
|---|---|---|
| Login | 5 / min | IP |
| Login | 20 / h | Account (verhindert Account-Lockout-DoS via Account-Pivoting) |
| Password-Reset | 3 / h | E-Mail |
| MFA-Verify | 5 / min | Session |
| API-Token-Issue | 10 / min | User |
| Sign-up | 5 / h | IP |

**Lib-Empfehlungen:**

- `express-rate-limit` — einfach, Memory-Store, gut für Single-Instance.
- `rate-limiter-flexible` — Redis-basiert, verteilt-tauglich.
- Upstash Ratelimit — Edge-tauglich (Cloudflare Workers, Vercel Edge).

```js
const { RateLimiterRedis } = require('rate-limiter-flexible');

const loginLimiter = new RateLimiterRedis({
  storeClient: redis,
  keyPrefix: 'login_fail',
  points: 5, // 5 Versuche
  duration: 60, // pro 60 Sekunden
  blockDuration: 60 * 15, // 15 min Sperre nach Limit
});

app.post('/login', async (req, res) => {
  try {
    await loginLimiter.consume(req.ip);
    // ... Login-Logik
  } catch {
    return res.status(429).json({ error: 'Zu viele Versuche, bitte später erneut versuchen.' });
  }
});
```

> Rate-Limit-IP-Erfassung folgt den Regeln aus `IP-ADDRESSES.md` (X-Forwarded-For-Sicherung, Anonymisierung nach Zweckende).

## Account-Lockout / Credential-Stuffing-Defense

Reines IP-Rate-Limiting reicht 2026 nicht mehr aus. Moderne Credential-Stuffing-Angriffe sind „Low-and-Slow" über Bot-Netze: jede einzelne IP probiert nur einen Datensatz, IP-Limits bleiben unauffällig. Schutz braucht **account-basiertes** Lockout zusätzlich:

- **Soft-Lock nach 10 Fehlversuchen pro Account** (über alle IPs hinweg) — temporäre Sperre, danach Login nur per verifiziertem Magic-Link oder erzwungenem Password-Reset, nicht über erneuten Versuch.
- **Progressive Delays** statt harter Sperre: nach 5 Fehlversuchen 5 s Wartezeit, nach 10 Fehlversuchen 30 s, danach 5 min — verlangsamt Brute-Force ohne Account-Lockout-DoS-Vektor (Angreifer könnte sonst fremde Konten durch absichtliche Fehlversuche sperren).
- **Step-up-MFA + CAPTCHA** statt Lockout: bei Verdachtsmuster zweite Faktor-Prüfung oder Challenge erzwingen.
- **Risiko-Signale kombinieren:** IP-Reputation, Geo-Anomalie, Device-Fingerprint, Tageszeit. Lockout greift nur bei Multi-Signal-Verdacht.

Anti-Pattern: **„15 Fehlversuche → Account dauerhaft sperren bis manueller Admin-Reset"** — ist gleichzeitig DoS-Vektor (Angreifer sperrt fremde Accounts gezielt) und Support-Last-Generator.

```js
// Beispiel: account-basiertes Soft-Lock zusätzlich zum IP-Rate-Limit
const accountLimiter = new RateLimiterRedis({
  storeClient: redis,
  keyPrefix: 'login_fail_acc',
  points: 10,        // 10 Fehlversuche pro Account
  duration: 60 * 60, // pro Stunde
  blockDuration: 60 * 30, // 30 min Soft-Lock — danach Magic-Link-Recovery, nicht erneuter Login
});

// Im Login-Handler nach IP-Rate-Limit:
try {
  await accountLimiter.consume(loginEmail.toLowerCase());
} catch {
  await sendMagicLinkRecovery(loginEmail);
  return res.status(429).json({
    error: 'Konto temporär gesperrt. Wir haben dir einen Wiederherstellungs-Link an deine E-Mail geschickt.',
  });
}
```

## Refresh-Token-Rotation

1. Jeder Refresh-Token hat eindeutige `jti` (JWT-ID, UUID).
2. Bei Refresh: alten Token in Revocation-Liste, neuen mit neuer `jti` ausstellen.
3. Wenn ein bereits genutzter Refresh-Token erneut präsentiert wird → ALLE Tokens des Users invalidieren (Token-Diebstahl-Detection).
4. Sliding-Window: max. 14 Tage Re-Login-Frei, dann harte Re-Authentifizierung.
5. Refresh-Token nur über `HttpOnly`-Cookie ausliefern, **nicht** an JavaScript-Code.
6. **Refresh-Token in der DB hashen** — nicht im Klartext speichern. Bei DB-Leak ist das Token sonst direkt nutzbar. Argon2id oder zumindest HMAC-SHA-256 mit Server-Pepper. Das `jti` bleibt im Token; in der DB liegt nur der Hash + Metadaten (User-ID, Ablauf, Revocation-Status).

## Secret-Management

Auth-Code lebt von kryptografischen Secrets: Session-Secret, JWT-Signing-Key, HMAC-Salt für Pseudonymisierung, Backup-Code-Hashes, Refresh-Token-Pepper, OAuth-Client-Secrets. Pattern:

- **Secret-Manager** (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault, Azure Key Vault, Doppler) — nicht in `.env`-Files im Repo, nicht in der DB neben den Logs/Tokens.
- **Rotation:** JWT-Signing-Keys jährlich rotieren mit Overlap-Phase (alter Key noch gültig für Verifikation, neuer für Signing). Session-Secret bei Verdacht sofort rotieren — alle Sessions invalidieren ist akzeptabel.
- **Getrennte Zugriffsrechte:** App-Service-Account hat Read-Only auf den Key, nicht der Dev-Account. Audit-Log auf Secret-Zugriffe mitloggen.
- **Pepper für Passwort-Hashes** (optional, defense-in-depth): zusätzlicher serverseitiger Wert, der vor dem bcrypt/Argon2id-Hashing an das Passwort gehängt wird. Schützt bei DB-Leak, wenn Pepper im Secret-Manager bleibt — Angreifer mit nur DB-Dump hat unvollständige Eingabe.

Drittland-Aspekt der Manager-Wahl (US-basierte Cloud-Anbieter): `dsgvo-third-country-transfer/PROVIDERS.md`.

## Quellen

- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [BSI TR-02102-1 Kryptographische Verfahren (Version 2026-01, mit PQC-Erweiterung)](https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Standards-und-Zertifizierung/Technische-Richtlinien/TR-nach-Thema-sortiert/tr02102/tr02102.html) — Argon2id ist seit BSI TR-02102-1 v2020-01 (24.03.2020) für die passwortbasierte Schlüsselableitung explizit empfohlen, in v2026-01 (23.01.2026) weiterhin primärer Standard. Die hier genannten Parameter (m=64 MiB) übertreffen das OWASP-Minimum und sind uneingeschränkt BSI-konform hinsichtlich des geforderten Sicherheitsniveaus von 120 Bit (relevant für KRITIS-Konformität nach § 8a BSIG).
- [RFC 6238 (TOTP)](https://www.rfc-editor.org/rfc/rfc6238)
- [WebAuthn Spec (W3C)](https://www.w3.org/TR/webauthn-3/)
- [RFC 6265bis (Cookies, SameSite)](https://datatracker.ietf.org/doc/draft-ietf-httpbis-rfc6265bis/)
- [NIST SP 800-63B-4 Final (Digital Identity Guidelines, 31.07.2025)](https://csrc.nist.gov/pubs/sp/800/63/b/4/final)
