# IP-Adressen unter DSGVO

> Sub-File zu `dsgvo-auth-and-logging`. Best-Practice, **keine Rechtsberatung**. Stand Mai 2026.

Diese Datei liefert die Rechtslage zur IP-Adresse als personenbezogenes Datum (EuGH/BGH), Speicherdauer-Empfehlungen, lauffähige Anonymisierungs-Snippets für IPv4 und IPv6, X-Forwarded-For-Sicherung hinter Reverse Proxy, einen GeoIP-Drittland-Hinweis sowie die Sonderfälle Brute-Force-Erkennung und Beschäftigten-IP. Was generell in Logs darf bzw. nicht, regelt `LOGGING.md`; die Auth-Code-Pfade (Hashing, Cookies, Rate-Limit-Werte) wohnen in `AUTH-TOM.md`.

## Rechtliche Einordnung

- **EuGH C-582/14 Breyer (19.10.2016):** Eine dynamische IP-Adresse ist für den Webseitenbetreiber dann personenbezogen, wenn ihm rechtliche Mittel zur Verfügung stehen, mithilfe derer er die betroffene Person über Dritte (typisch: Access-Provider via Behörden- oder Zivilrechtsanfrage) identifizieren kann. Volltext: <https://curia.europa.eu/juris/document/document.jsf?docid=184668>.
- **BGH VI ZR 135/13 (16.05.2017):** Folgeurteil zu Breyer auf nationaler Ebene — bestätigt die relative Theorie: IP ist personenbezogen, wenn der Betreiber realistisch zur Identität gelangen kann. Recherchierbar bei juris.bundesgerichtshof.de unter Aktenzeichen `VI ZR 135/13`; Direktlink: <https://juris.bundesgerichtshof.de/cgi-bin/rechtsprechung/document.py?Gericht=bgh&Art=en&nr=78475>.
- **EuGH C-319/22 Gesamtverband / Scania (09.11.2023):** Bestätigt die relative Theorie auch für andere Identifier (FIN-Fahrgestellnummer): Pseudonyme sind personenbezogen, sobald der Verantwortliche realistisch zur Identität gelangen kann. Direkt anwendbar auf Hash- und Pseudonym-IDs in Auth-Logs.
- **EuGH C-604/22 IAB Europe (07.03.2024):** TC-String aus Cookie-Consent ist personenbezogen, wenn er mit IP korrelierbar ist. Verschärft Linie zu IP + Identifier-Kombinationen — relevant für Audit-Logs mit User-ID + IP.
- **EuGH C-446/21 Schrems / Meta (04.10.2024):** Datenminimierung (Art. 5(1)(c)) als harte Schranke gegen aggregierte personenbezogene Daten ohne zeitliche Begrenzung — relevant für Auth-/Sicherheits-Log-Aufbewahrung („zeitlich unbegrenzt und ohne Unterscheidung nach Art" verboten).
- **EuGH C-413/23 P EDSB / SRB (04.09.2025):** Wegweisende Bestätigung der **relativen Theorie** des Personenbezugs für Pseudonymisierung. Pseudonymisierte Daten — etwa kryptografisch gehashte User-IDs in Audit-Logs — können für den **externen Empfänger** (z.B. einen Cloud-Logging-Anbieter) als anonym gelten, sofern dieser keine rechtlichen oder praktischen Mittel zur Re-Identifikation besitzt. Für den Verantwortlichen, der den Salt hält, bleiben sie personenbezogen. Diese Differenzierung entschärft DSGVO-Risiken bei externen Log-Aggregatoren und Drittlandtransfers signifikant — vorausgesetzt, die Pseudonymisierung ist architektonisch sauber getrennt (Salt im eigenen Secret-Manager, Empfänger sieht nur den Hash).
- **DSK-Position:** IP-Adressen gelten grundsätzlich als personenbezogen, auch hinter NAT — Carrier-Grade-NAT macht keine wirksame Anonymisierung, da Provider-seitige Zuordnungstabellen die Re-Identifizierung erlauben.
- **CNIL Délibération n° 2021-122 (14.10.2021):** Default-Speicherdauer für Verbindungs-Logs **6 Monate bis 1 Jahr**, in begründeten Fällen bis 3 Jahre. Die häufig zitierten „max. 6 Monate" sind die Untergrenze des Korridors, nicht der Maximalwert. Quelle: <https://www.cnil.fr/fr/la-cnil-publie-une-recommandation-relative-aux-mesures-de-journalisation>.

> Praxis-Konsequenz: Jede IP-Speicherung braucht eine Rechtsgrundlage nach Art. 6 DSGVO und einen klaren Zweck. „Wir loggen, falls wir's mal brauchen" reicht nicht.

## Speicherdauer-Empfehlung

Diese Tabelle gibt **IP-spezifische** Default-Werte; allgemeine Auth-/Application-Log-Fristen stehen in `LOGGING.md`.

| Zweck | Default | Maximum mit Begründung | Rechtsgrundlage |
|---|---|---|---|
| Sicherheit (Brute-Force-Erkennung) | 7 Tage | 30 Tage | Art. 6(1)(f) |
| Access-Log (HTTP-Anfragen) | 7 Tage | 90 Tage | Art. 6(1)(f), nur mit konkretem Sicherheits-Konzept |
| Audit-Log mit IP | nur wenn IP für den Zweck zwingend | 90 Tage | Art. 6(1)(f) bzw. (1)(c) |
| Rechnungs-/Vertragsbeleg | gem. HGB-/AO-Frist | 10 Jahre (HGB) | Art. 6(1)(c) — nur die IP, die im Beleg steht, NICHT zusätzliche Logs |
| Marketing/Analytics | nicht ohne Anonymisierung | – | Art. 6(1)(a) Einwilligung erforderlich |

> Faustregel für Auth-Logs: Volle IP > 7 Tage = triftiger Grund schriftlich + DSFA prüfen (siehe `dsgvo-third-country-transfer/DPIA.md`).

## Anonymisierungs-Code

**Node.js / JavaScript:**

```js
const net = require('net');

function anonymizeIPv4(ip) {
  // letztes Oktett auf 0
  return ip.replace(/^(\d+\.\d+\.\d+)\.\d+$/, '$1.0');
}
// '192.168.42.123' → '192.168.42.0'

function anonymizeIPv6(ip) {
  // /48-Präfix behalten (erste 3 Hex-Gruppen), Rest auf 0
  // Robust gegen komprimierte Form (`::`), die in Production-Logs der Normalfall ist.
  if (ip.includes('.')) return ip; // IPv4-mapped IPv6 — separat über anonymizeIPv4
  const [head, tail = ''] = ip.split('::');
  const headParts = head ? head.split(':') : [];
  const tailParts = tail ? tail.split(':') : [];
  const fill = Array(8 - headParts.length - tailParts.length).fill('0');
  const full = [...headParts, ...fill, ...tailParts];
  return full.slice(0, 3).join(':') + '::';
}
// '2001:db8:abcd:1234:5678:9abc:def0:1234' → '2001:db8:abcd::'
// '2001:db8::1'                            → '2001:db8:0::'
// 'fe80::1'                                → 'fe80:0:0::'

// Top-Level-Wrapper: Familie via net.isIP bestimmen.
// NIE den Original-String stillschweigend durchreichen.
function anonymizeIp(ip) {
  if (typeof ip !== 'string') return null;
  const family = net.isIP(ip); // 4, 6 oder 0
  if (family === 4) return anonymizeIPv4(ip);
  if (family === 6) {
    // IPv4-mapped (`::ffff:1.2.3.4`) und IPv4-embedded vorab abfangen —
    // sonst leakt die volle IPv4 ungekürzt durch. Cloud-LBs / Node-`http`-Server
    // exponieren `req.socket.remoteAddress` häufig in dieser Form.
    const m = ip.match(/^(?:::ffff:|[0-9a-fA-F:]*:)((?:\d+\.){3}\d+)$/);
    if (m) return ip.replace(m[1], anonymizeIPv4(m[1]));
    return anonymizeIPv6(ip);
  }
  return null;
}
```

> Für Production-Code mit hohem IPv6-Aufkommen: Library `ipaddr.js` oder `ip-address` verwenden — die Implementierungen oben sind Demo-Patterns, nicht voll RFC-5952-konform (kanonische Form, Zero-Compression). Bei High-Throughput-Szenarien (Millionen Logs/Min) sind String-Split + Array-Manipulation kostspielig (Garbage Collection, CPU); kompilierte Bindings oder Bit-Shift-Implementierungen (z.B. `ipaddr.js` intern) sind dort sinnvoller.

**Python:**

```python
import ipaddress

def anonymize(ip_str):
    ip = ipaddress.ip_address(ip_str)
    if isinstance(ip, ipaddress.IPv4Address):
        return str(ipaddress.ip_network(f"{ip}/24", strict=False).network_address)
    return str(ipaddress.ip_network(f"{ip}/48", strict=False).network_address)
```

> /48 statt /64 ist konservativer: Ein /64-Subnetz wird heute oft an einen einzelnen Endkunden vergeben, ein /48 typisch an einen ISP-Pool. Für Anonymisierung im Sinne von Erwägungsgrund 26 zählt der größere Pool.

> GA4 setzt automatisch `_anonymizeIp` für IPv4 (letztes Oktett auf 0). Reicht **NICHT** als „echte" Anonymisierung im Sinne von Erwägungsgrund 26, weil Google die ursprüngliche IP zur Geolokalisierung nutzt, bevor sie im Storage gekürzt wird. DSK hat das mehrfach klargestellt — siehe `dsgvo-third-country-transfer/EPRIVACY.md` für den Tracking-Aspekt.

## X-Forwarded-For-Handling

Spoofing-Risiko: Jeder Client kann beliebige `X-Forwarded-For`-Header senden. Wenn der Server jedem Header vertraut, rotiert ein Angreifer einfach Werte und umgeht Brute-Force- oder Rate-Limit-Logik komplett. Nur Header von **bekannten** Reverse Proxies dürfen ausgewertet werden.

**Express:**

```js
// Nur loopback, Linklocal, private Netze als Trusted Proxy
app.set('trust proxy', ['loopback', 'linklocal', 'uniquelocal']);

// Oder: konkrete Cloudflare-CIDRs
// app.set('trust proxy', ['173.245.48.0/20', '103.21.244.0/22', /* ... */]);

// Danach req.ip enthält die wirklich-vertrauenswürdige IP
```

**Next.js / Vercel-Edge:**

```ts
import { NextRequest } from 'next/server';

export function getClientIp(request: NextRequest): string {
  // Cloudflare bevorzugen (signed)
  const cf = request.headers.get('cf-connecting-ip');
  if (cf) return cf;
  // Vercel-Edge / Vercel signiert XFF, [0] ist die echte Client-IP.
  // Self-Hosted ohne signierenden Proxy: split(',') rückwärts ab dem trusted hop nehmen.
  const xff = request.headers.get('x-forwarded-for');
  if (xff) return xff.split(',')[0].trim();
  return request.headers.get('x-real-ip') ?? 'unknown';
}
```

> Hinter Cloudflare immer `CF-Connecting-IP` bevorzugen (signed). Bei `X-Forwarded-For` nur die erste IP **nach** trusted proxy nehmen, nicht die erste IP überhaupt — sonst übernimmt jeder Client die Kontrolle über das vermeintliche Identifikations-Feld. RFC 7239 (`Forwarded:`-Header) löst das sauberer als `X-Forwarded-For`, ist in der Praxis aber seltener konfiguriert.

## GeoIP-Anbieter

- **MaxMind GeoIP2** (US) — bei Cloud-API-Variante: Drittland-Transfer prüfen. Self-hosted GeoLite2-DB-Download = **kein** Drittland-Transfer (DB liegt lokal, einzige Verbindung ist der periodische DB-Update).
- **IP2Location** (US) — analog: Cloud-API = Drittland, lokale DB-Lite = kein Transfer.
- **ipapi.co** (US) — reine Cloud-API, immer Drittland.
- **ipinfo.io** (US) — analog.

> Für DACH-Projekte ohne Drittland-Transfer: MaxMind GeoLite2 oder IP2Location-Lite lokal vorhalten und periodisch updaten. Detail zur Anbieter-Wahl und SCC-Lage: `dsgvo-third-country-transfer/PROVIDERS.md`.

## Sonderfall: Brute-Force-Erkennung

- **Rechtsgrundlage:** Art. 6(1)(f) berechtigtes Interesse (Schutz des eigenen Systems und der Konten der Nutzer).
- **Speicherdauer:** so kurz wie möglich nach Erreichen des Schutzzwecks — typisch 7 Tage Pattern-Erkennungsfenster, danach Anonymisierung oder Löschung.
- **Granularität:** Volle IP nur im aktiven Erkennungsfenster, danach gekürzt. Counter und Pattern (z.B. „IP X hatte 142 Fehlversuche") können auch nach Anonymisierung auf gekürzter IP weiterlaufen.

**Anti-Pattern:** „Wir loggen IP für ein Jahr, falls jemand später angreift" — Verstoß gegen Art. 5(1)(c) Datenminimierung. Brute-Force- und Credential-Stuffing-Angriffe werden in Stunden bis Tagen erkannt, nicht in Monaten. Die historische IP von vor sechs Monaten hat keinen operativen Nutzen mehr.

Cross-Link: Konkrete Rate-Limit-Werte (Login 5/min/IP, Password-Reset 3/h/Mail usw.) in `AUTH-TOM.md`.

## Sonderfall: Beschäftigten-IP

Wenn IP-Tracking direkt oder indirekt einem Beschäftigten zugeordnet werden kann — etwa über die Login-Tabelle, ein internes Netz mit fester IP-Vergabe oder VPN-Logs:

- **§ 26 BDSG:** Verarbeitung personenbezogener Daten Beschäftigter ist zulässig, soweit erforderlich für Begründung, Durchführung oder Beendigung des Beschäftigungsverhältnisses oder zur Aufdeckung von Straftaten — letzteres nur bei konkretem, dokumentiertem Verdacht, nicht ins Blaue hinein.
- **Mitbestimmung:** Betriebsrat nach § 87 Abs. 1 Nr. 6 BetrVG bei jeder technischen Einrichtung, die zur Verhaltens- oder Leistungsüberwachung von Beschäftigten geeignet ist — VPN-/Proxy-/Auth-Logs fallen typisch darunter.
- **Anti-Pattern:** VPN-IP-Logs als allgemeines „Sicherheits-Log" deklarieren, faktisch aber für Mitarbeiter-Tracking (Anwesenheits-, Pausen-, Produktivitätskontrolle) nutzen — Zweckänderung ohne neue Rechtsgrundlage und ohne Mitbestimmung.

Cross-Link: `dsgvo-employment` (geplant).

## Quellen

- [EuGH C-582/14 Breyer (curia.europa.eu)](https://curia.europa.eu/juris/document/document.jsf?docid=184668)
- [BGH VI ZR 135/13 (16.05.2017)](https://juris.bundesgerichtshof.de/cgi-bin/rechtsprechung/document.py?Gericht=bgh&Art=en&nr=78475)
- [EuGH C-319/22 Gesamtverband / Scania (09.11.2023)](https://curia.europa.eu/juris/liste.jsf?num=C-319/22)
- [EuGH C-604/22 IAB Europe (07.03.2024)](https://curia.europa.eu/juris/liste.jsf?num=C-604/22)
- [EuGH C-446/21 Schrems / Meta (04.10.2024)](https://curia.europa.eu/juris/liste.jsf?num=C-446/21)
- [EuGH C-413/23 P EDSB / SRB (04.09.2025)](https://curia.europa.eu/juris/liste.jsf?num=C-413/23)
- [DSK-Orientierungshilfen (datenschutzkonferenz-online.de)](https://www.datenschutzkonferenz-online.de/orientierungshilfen.html)
- [CNIL Délibération 2021-122 (Mesures de journalisation)](https://www.cnil.fr/fr/la-cnil-publie-une-recommandation-relative-aux-mesures-de-journalisation)
- [RFC 7239 (Forwarded HTTP Header)](https://www.rfc-editor.org/rfc/rfc7239)
- [BDSG § 26 Volltext](https://www.gesetze-im-internet.de/bdsg_2018/__26.html)
