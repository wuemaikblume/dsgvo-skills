# Logging — was rein darf, was raus muss

> Sub-File zu `dsgvo-auth-and-logging`. Best-Practice, **keine Rechtsberatung**. Stand Mai 2026.

Diese Datei liefert konkrete Scrubbing-Patterns für Sentry, Datadog, Pino, Winston, morgan und Next.js-Middleware sowie Default-Aufbewahrungsfristen und typische Zweckbindungs-Verstöße. Auth-spezifische TOMs (Hashing-Parameter, Cookie-Flags, MFA) wohnen in `AUTH-TOM.md`, IP-Anonymisierung in `IP-ADDRESSES.md`.

## Was loggen, was pseudonymisieren, was nie

| Erlaubt (Klartext) | Pseudonymisieren | Verboten |
|---|---|---|
| Request-ID / Trace-ID (UUID, server-generiert) | User-ID (HMAC-SHA-256 + Server-Salt, siehe Pseudonymisierungs-Pattern unten) | Klartext-Passwörter, auch versehentlich im Body |
| HTTP-Status, HTTP-Methode | Session-ID (gehashed, nicht roher Cookie-Wert) | `Authorization`-Header (Bearer-/Basic-Token) |
| Endpoint-Name **ohne** PII-Pfad-Parameter (`/api/users/:id` statt `/api/users/42`) | IP-Adresse (gekürzt — Verfahren in `IP-ADDRESSES.md`) | `Cookie`-Header und `Set-Cookie` |
| Timestamp, Server-Region, Pod-Name, Build-/Lib-Version | Device-Fingerprint (gehashed mit Salt, nicht roh) | `X-API-Key`, OAuth-Access-/Refresh-Tokens, MFA-Recovery-Codes |
| Reine Technik-Metriken (Latenz, RPS, Heap) | Korrelations-IDs, die ohne Hash rückführbar wären | Body-Felder mit Health/SSN/Bank/Kreditkarte/Religion/Biometrie (Art. 9) |
| Feature-Flag-Namen, A/B-Test-Bucket (ohne User-ID) | E-Mail nur als gehashter lokaler Teil, Domain optional separat | Stack-Trace mit Pfaden wie `/home/<user>/`, `C:\Users\<user>\` |

> **Health-Kontext weit verstehen** (EuGH C-21/23 Lindenapotheke, 04.10.2024): In Apotheken-, Medizin-, Therapie- oder Health-Shop-Apps können bereits Produkt-, Bestell-, Such- oder URL-Daten Art.-9-Bezug haben, wenn aus ihnen Gesundheitsinformationen ableitbar sind. „Bestellte Migräne-Tablette" ist ein Gesundheitsdatum. Audit-Logs in solchen Kontexten besonders strikt scrubben — Endpoint-Pfade ohne Produkt-IDs, Suchanfragen nicht klartext loggen.

## Scrubbing-Snippets

### Sentry (Node.js)

```js
const Sentry = require('@sentry/node');

Sentry.init({
  dsn: process.env.SENTRY_DSN_EU, // de.sentry.io / EU-Hosting
  sendDefaultPii: false,
  beforeSend(event) {
    if (event.request?.headers) {
      delete event.request.headers.authorization;
      delete event.request.headers.cookie;
      delete event.request.headers['x-api-key'];
    }
    event.exception?.values?.forEach((e) => {
      e.stacktrace?.frames?.forEach((f) => {
        if (f.filename) {
          f.filename = f.filename
            .replace(/\/(home|Users)\/[^/]+/g, '/$1/REDACTED')
            .replace(/([A-Za-z]:\\Users\\)[^\\]+/g, '$1REDACTED');
        }
      });
    });
    return event;
  },
});
```

> Der Snippet zeigt nur die Privacy-relevanten Optionen. `tracesSampleRate`, `release`, `environment` und Tracing-Integrationen bewusst weggelassen — diese ergänzt jede App nach Bedarf.
>
> **`@sentry/node` v10+ (OpenTelemetry-Architektur, 2026 industrieller Standard):** `sendDefaultPii: false` blockiert die inferrierte IP-Erfassung auf Error-Event-Ebene. v10 baut nativ auf OpenTelemetry v2 auf — bei aktiviertem Performance-Tracing (Spans/Transactions) muss zusätzlich `beforeSendTransaction` analog zu `beforeSend` implementiert werden, sonst leaken Span-Attribute mit User-IDs, Session-Cookies oder HTTP-Metadaten über verteilte Traces. OTel-Instrumentierungen legen detaillierte HTTP-Daten in Span-Attributen ab, die der Privacy-Filter sonst nicht erreicht.

### Datadog Browser RUM / Logs

```js
import { datadogLogs } from '@datadog/browser-logs';

datadogLogs.init({
  clientToken: process.env.DD_TOKEN,
  site: 'datadoghq.eu', // EU-Hosting
  forwardErrorsToLogs: true,
  beforeSend: (log) => {
    if (log.http?.headers) {
      delete log.http.headers.authorization;
      delete log.http.headers.cookie;
    }
    return true;
  },
});
```

### Pino (Node.js)

```js
const pino = require('pino');

const logger = pino({
  redact: {
    paths: [
      'req.headers.authorization',
      'req.headers.cookie',
      'req.headers["x-api-key"]',
      '*.password',
      '*.token',
      '*.refreshToken',
      'body.ssn',
      'body.creditCard',
    ],
    censor: '[REDACTED]',
  },
});
```

> Pino-Wildcards (`*.password`) durchsuchen das Log-Objekt auf tieferen Ebenen rekursiv, verursachen aber einen massiven **Performance-Overhead bis zu 50%** bei JSON-Serialisierung gegenüber expliziten Pfaden (~2%) — der AST muss dynamisch traversiert werden. Zudem matchen sie das Top-Level-Feld (`password` direkt im Root) systembedingt **nicht**. **Best Practice:** Top-Level-Felder explizit ohne Wildcard listen (`'password'`), Wildcards sparsam einsetzen, tiefe Pfade explizit auflisten (`'body.user.password'`). Bei 3+ Verschachtelungsebenen weisen Wildcards in `fast-redact` bekannte Bugs auf ([Issue pinojs/redact#19](https://github.com/pinojs/redact/issues/19)) — explizites Pathing zwingend, sonst DSGVO-relevante Datenlecks.

### Winston (Node.js)

```js
const winston = require('winston');

const sensitiveFilter = winston.format((info) => {
  // Shallow-Clone, weil JS Objekte per Referenz übergibt: ohne Clone würde
  // der Filter das Original-Objekt mutieren und nachgelagerte Verwendungen
  // (Metriken, Antwort-Formatierung) beschädigen.
  const cloned = { ...info, headers: info.headers ? { ...info.headers } : undefined };
  const SENSITIVE = ['password', 'token', 'refreshToken', 'authorization', 'cookie'];
  for (const key of SENSITIVE) {
    if (cloned[key]) cloned[key] = '[REDACTED]';
  }
  if (cloned.headers?.authorization) cloned.headers.authorization = '[REDACTED]';
  return cloned;
});

const logger = winston.createLogger({
  format: winston.format.combine(sensitiveFilter(), winston.format.json()),
});
```

### morgan (Express Access-Log)

```js
const morgan = require('morgan');

// Pfad ohne Query-String loggen — Query-Parameter enthalten in der Praxis
// Reset-Tokens, Magic-Link-Tokens, OAuth-State, E-Mail-Adressen oder Tracking-IDs.
morgan.token('safe-path', (req) => {
  try {
    return new URL(req.originalUrl || req.url, 'http://local').pathname;
  } catch {
    return req.path || '[invalid-url]';
  }
});

morgan.token('request-id', (req) => req.headers['x-request-id'] || '-');

// Whitelist-basiertes Format — keine Header-Spread-Liste mit „alles außer Auth/Cookie".
// Header wie referer, x-forwarded-for, user-agent oder eigene App-Header
// können selbst PII enthalten.
app.use(morgan(':method :safe-path :status :response-time ms rid=:request-id'));
```

### Next.js Middleware

```ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  // Whitelist-basiert — kein Spread aller Header, weil referer / user-agent /
  // x-forwarded-for / eigene App-Header selbst PII enthalten können.
  // `request.nextUrl.pathname` lässt searchParams bewusst weg.
  console.log(
    JSON.stringify({
      method: request.method,
      path: request.nextUrl.pathname,
      requestId: request.headers.get('x-request-id') ?? undefined,
      timestamp: new Date().toISOString(),
    }),
  );
  return NextResponse.next();
}
```

Bunyan (Node) bzw. LogRocket (Browser-Session-Replay) lassen sich analog absichern: Bunyan über eigene Serializers (`req`, `err`) mit Header-Whitelist, LogRocket über `requestSanitizer` / `responseSanitizer` und das Privacy-Mode-Attribut `data-private` auf sensiblen DOM-Elementen.

## Aufbewahrungsfristen (Richtwerte für Auth- und Application-Logs)

| Log-Typ | Default | Maximum | Begründung / Quelle |
|---|---|---|---|
| Auth-Audit (Login Success/Fail, Passwort-Reset, MFA-Verify) | 30–90 Tage | 180 Tage | DSK-Orientierungshilfe; Sicherheits-Reaktionsfenster |
| Application Error (Sentry, Stack-Traces) | 14 Tage | 30 Tage | Debugging-Zeitfenster ohne Daueraufbewahrung |
| Access Log (HTTP-Anfragen) | 7 Tage | 90 Tage | nur mit konkreter Sicherheitsbegründung über 7 Tage |
| Sicherheits-Vorfall (aktiv) | bis Aufklärung | – | Beweissicherung; danach archivieren oder löschen |
| Performance-Metriken (ohne User-Bezug) | beliebig | – | außerhalb DSGVO bei vollständiger Anonymisierung |

> Aufbewahrungsfristen anderer Datenkategorien (HGB-Belege 10 Jahre, AO-Steuerunterlagen, TKG-Verbindungsdaten) gelten **nicht** automatisch für Auth-Logs — siehe `dsgvo-personal-data-storage` (geplant).

## Security-Event-Taxonomie

OWASP Top 10 A09 (2025) listet Authentifizierungs-, Autorisierungs- und Session-Management-Fehler explizit als log-relevant. Mindest-Set für Auth-Audit-Logs:

- `login.success` / `login.failure` (mit Grund: wrong-password, mfa-failed, account-locked)
- `mfa.challenge_issued` / `mfa.success` / `mfa.failure`
- `password.reset_requested` / `password.reset_completed` / `password.changed`
- `session.created` / `session.revoked` / `session.refreshed`
- `token.reuse_detected` (= Refresh-Token-Diebstahl-Detection)
- `account.locked` / `account.unlocked` / `account.role_changed`
- `data.export_started` / `data.export_completed` (Art. 20 Portabilität)
- `account.deleted` (Art. 17 Recht auf Löschung)

Diese Event-Liste ist auch die Basis für **Datenpanne-Trigger nach Art. 33/34 DSGVO**: bei `token.reuse_detected`, `password.exposed_in_logs`, `account.role_changed_unauthorized` oder kompromittiertem Admin-Konto Incident-Bewertung anstoßen — 72-Stunden-Frist bis Aufsichtsbehörde-Meldung. Detail: geplanter `nis2-security-baseline`-Skill.

## Audit-Log-Integrität

Vertraulichkeit (Scrubbing, Pseudonymisierung) ist nur die halbe Miete. Bei kritischen Anwendungen — Gesundheitsdaten Art. 9, Finanzsektor unter DORA, KRITIS / NIS2 — ist auch die **Integrität** der Audit-Logs gefordert (Beweissicherung, forensische Nachvollziehbarkeit, Art. 33 DSGVO Meldepflicht). Ein Angreifer mit Server-Zugriff könnte sonst Logs nachträglich manipulieren oder selektiv löschen, um laterale Bewegungen zu verschleiern.

**Architektur-Bausteine:**

- **Append-only Speicherung** + getrennte Schreib-/Leserechte. Logs nicht in derselben DB-Rolle wie Anwendungsdaten.
- **WORM / Object-Lock** für archivierte Security-Logs — z.B. AWS S3 Object Lock im Compliance-Mode, Azure Blob Immutable Storage. Drittland-Aspekt: `dsgvo-third-country-transfer/PROVIDERS.md`.
- **HMAC-Chain** für laufende Audit-Logs: jeder Log-Eintrag enthält den HMAC-Hash des vorherigen Eintrags — jede nachträgliche Manipulation bricht die Kette.
- **Server-seitiger Zeitstempel** statt Client-Timestamp — sonst kann der Client (oder ein Angreifer) historische Einträge fälschen.
- **Lösch-/Retention-Jobs dokumentiert** und auditierbar separat geloggt.

```js
const crypto = require('crypto');

// HMAC-Chain: jeder Eintrag hängt am Hash des vorherigen.
// Bei Manipulation einzelner Einträge bricht die Kette an dieser Stelle.
function appendAuditLog(prevHash, entry) {
  const data = JSON.stringify({ ...entry, prev: prevHash, ts: new Date().toISOString() });
  const hash = crypto.createHmac('sha256', process.env.AUDIT_LOG_KEY).update(data).digest('hex');
  return { data, hash };
}

// Verifikation: Kette von Eintrag 0 bis n durchrechnen, mit gespeicherten Hashes vergleichen.
function verifyChain(entries) {
  let prev = '0'.repeat(64); // Genesis
  for (const e of entries) {
    const expected = crypto.createHmac('sha256', process.env.AUDIT_LOG_KEY).update(e.data).digest('hex');
    if (expected !== e.hash || !e.data.includes(prev)) return false;
    prev = e.hash;
  }
  return true;
}
```

> Der `AUDIT_LOG_KEY` gehört in einen Secret-Manager (nicht in die Log-DB!) — analog zum Pseudonymisierungs-Salt. Bei Leak des Keys ist die Kette zwar weiter konsistent, aber ein Angreifer kann beliebige Einträge fälschen.

## Zweckbindung — Anti-Patterns

- **Auth-Logs für Marketing-Analyse zweitverwerten** → Verstoß gegen Art. 5(1)(b) Zweckbindung. Wenn Marketing-Daten gewollt: separater Datenfluss mit eigener Rechtsgrundlage (Einwilligung).
- **Performance-KPIs aus Login-Tracking ableiten** → Zweckänderung ohne neue Rechtsgrundlage. Login-Erfolg ist ein Sicherheitssignal, kein Performance-Signal.
- **Application-Logs für Mitarbeiter-Verhaltenskontrolle nutzen** → § 26 BDSG, Mitbestimmung des Betriebsrats nach § 87 Abs. 1 Nr. 6 BetrVG; siehe `dsgvo-employment` (geplant).
- **Error-Logs als Daten-Backup für Customer-Support** → Zweckänderung; Support-Fälle gehören in CRM mit eigener Aufbewahrungsfrist, nicht in Sentry.
- **Audit-Logs als KI-Trainingsdaten verwenden** → Zweckänderung nach Art. 5(1)(b) und Art. 6(4): braucht eigene Rechtsgrundlage, bei sensiblen Kategorien zusätzlich Art. 9. Wenn das trainierte Modell später vollautomatisiert über Betroffene entscheidet, kommt Art. 22 ins Spiel. DSFA bei Profiling oder großem Umfang sensibler Daten (→ `dsgvo-third-country-transfer/DPIA.md`).

## Pseudonymisierung von User-IDs

Klartext-User-IDs in Logs sind bei einem Logs-Leak sofort re-identifizierbar — deshalb HMAC-SHA-256 mit serverseitigem Salt:

```js
const crypto = require('crypto');

// Salt im Secret-Manager, NICHT im Repo, NICHT in der Log-DB.
const SALT = process.env.LOG_USER_HASH_SALT; // ≥ 32 Bytes random

function pseudonymizeUserId(userId) {
  return crypto
    .createHmac('sha256', SALT)
    .update(String(userId))
    .digest('hex')
    .slice(0, 16); // 64 Bit; ausreichend für Log-Korrelation bis ~10 Mio. distinct IDs (Birthday-Bound). Bei größeren Beständen 24 Hex-Stellen / 96 Bit nehmen.
}
```

**Anti-Pattern: SHA-256 ohne Salt** → jede User-ID hat denselben Hash über alle Logs hinweg. Bei int-User-IDs lässt sich eine Rainbow-Table in Sekunden bauen.

**Anti-Pattern: MD5 oder SHA-1** → kryptographisch gebrochen, nicht für Pseudonymisierung verwenden. Auch nicht „nur als Korrelations-ID".

**Anti-Pattern: Salt im selben System wie die Logs** → bei einem Leak ist der Salt mit-geleakt. Der Salt gehört in einen Secret-Manager (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault). Drittland-Aspekt der Manager-Wahl: `dsgvo-third-country-transfer/PROVIDERS.md`.

> **EuGH C-413/23 P SRB/EDPS (04.09.2025):** Sauber getrennte Pseudonymisierung kann externe Log-Aggregatoren aus dem DSGVO-Anwendungsbereich nehmen. Wenn der Cloud-Anbieter nur den HMAC-Hash sieht und der Salt im eigenen Secret-Manager bleibt, hat der Anbieter keine vernünftigerweise einsetzbaren Mittel zur Re-Identifikation — die Daten sind für **ihn** anonym, für **dich** als Verantwortlichen weiterhin personenbezogen. Voraussetzung: architektonisch saubere Trennung (Salt nie zusammen mit Logs übermitteln, kein Mapping-Lookup-Service, der dem Anbieter zugänglich ist). Detail: `IP-ADDRESSES.md` Rechtliche Einordnung.

## Quellen

- [DSGVO Art. 5 Volltext (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679#d1e1888-1-1)
- [DSK-Orientierungshilfen (datenschutzkonferenz-online.de)](https://www.datenschutzkonferenz-online.de/orientierungshilfen.html)
- [BfDI Tätigkeitsberichte](https://www.bfdi.bund.de/DE/Service/Taetigkeitsberichte/taetigkeitsberichte_node.html)
- [OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)
- [Sentry Data Filtering Docs](https://docs.sentry.io/platforms/javascript/data-management/sensitive-data/)
- [Pino Redaction Docs](https://github.com/pinojs/pino/blob/master/docs/redaction.md)
