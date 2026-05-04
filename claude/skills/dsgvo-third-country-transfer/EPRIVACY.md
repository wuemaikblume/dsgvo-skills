# ePrivacy / TTDSG — Cookies, Tracking, Pixel

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, wenn Tracking-Pixel, Analytics, Werbe-Tags, Cookies oder LocalStorage-Speicherung im Spiel sind.

## Wichtig: zwei Pflichtenkreise

Wenn ein Drittlandtransfer und ein Tracking-Tool gleichzeitig im Spiel sind, **beide** Regelwerke prüfen:

1. **DSGVO Kapitel V** — Drittlandtransfer (siehe `SKILL.md`)
2. **TTDSG / ePrivacy** — Speicherung/Auslesen auf Endgeräten (Cookies + alles ähnliche)

TTDSG/ePrivacy gilt **unabhängig** davon, ob personenbezogene Daten verarbeitet werden — schon das Setzen eines Cookies kann zustimmungspflichtig sein.

## TTDSG § 25 (Deutschland) / ePrivacy-RL Art. 5 Abs. 3

> „Die Speicherung von Informationen in der Endeinrichtung des Endnutzers und der Zugriff auf Informationen, die bereits in der Endeinrichtung gespeichert sind, sind nur zulässig, wenn der Endnutzer auf der Grundlage von klaren und umfassenden Informationen eingewilligt hat."

**Übersetzt:** Cookies, LocalStorage, IndexedDB, Fingerprinting, Pixel — Pflicht zur **vorherigen Einwilligung** (Opt-in), bevor irgendetwas gesetzt oder ausgelesen wird.

**Ausnahmen (§ 25 Abs. 2 TTDSG):**
- **Technisch unbedingt erforderlich** für die ausdrücklich vom Nutzer gewünschte Funktionalität
  - z.B. Session-Cookie für Login, Warenkorb, CSRF-Token, Sprachwahl
- **Reine Übertragung** über Kommunikationsnetz

**NICHT ausnahme-fähig:**
- Reichweitenmessung (auch wenn anonymisiert)
- Tracking
- Werbung
- A/B-Tests
- Analytics

## Was bedeutet das für SaaS-Tools?

| Tool | TTDSG-Status | DSGVO-Drittland |
|------|--------------|-----------------|
| Google Analytics 4 | Einwilligung Pflicht | + DPF (Google LLC zertifiziert) |
| Google Tag Manager | Einwilligung Pflicht (lädt nicht-essentielle Tags) | + DPF |
| Meta Pixel (Facebook) | Einwilligung Pflicht | + DPF (Meta zertifiziert) |
| Hotjar | Einwilligung Pflicht | + DPF-Status prüfen |
| Mixpanel | Einwilligung Pflicht | + DPF-Status prüfen |
| Amplitude | Einwilligung Pflicht | + DPF (Amplitude zertifiziert) |
| Microsoft Clarity | Einwilligung Pflicht | + Microsoft DPF |
| LinkedIn Insight Tag | Einwilligung Pflicht | + Microsoft DPF |
| Plausible Analytics | **keine Einwilligung Pflicht** (anonymized + EU) | EU-Anbieter |
| Matomo (self-hosted) | meist keine Einwilligung (mit „Do Not Track" und Anonymisierung) | EU |
| Sentry (Error Tracking, sendDefaultPii=false) | technisch erforderlich für Stabilität → vermutlich keine Einwilligung | + Sentry EU oder DPF |
| Stripe (Zahlungsabwicklung) | technisch erforderlich für Vertrag → keine Einwilligung | + DPF |

## Korrekte Implementierung

### 1. Vor Consent: nur essentielle Cookies/Storage

```typescript
// Vor Einwilligung NUR setzen, was technisch erforderlich ist
document.cookie = 'sessionId=...';        // OK (Login-Session)
localStorage.setItem('lang', 'de');       // OK (Sprachwahl)
localStorage.setItem('cart', JSON.stringify(items)); // OK (Warenkorb)

// Diese hier NICHT vor Einwilligung:
// - GA4 / GTM
// - Meta Pixel
// - Hotjar
// - jegliche Marketing-/Tracking-Tools
```

### 2. Consent-Banner bedingt laden

```typescript
// Erst wenn User zugestimmt hat:
function onAnalyticsConsent() {
  // GTM/GA4 erst jetzt initialisieren
  loadGtm();
  // oder
  window.gtag('consent', 'update', {
    analytics_storage: 'granted',
    ad_storage: 'granted',
  });
}
```

### 3. Granulare Einwilligung

Nicht „akzeptieren" als einziger Button — sondern:
- Notwendig (immer aktiv, abgewählt)
- Statistik / Analytics (Opt-in)
- Marketing / Werbung (Opt-in)
- Personalisierung (Opt-in)

**„Akzeptieren" und „Ablehnen" gleich prominent darstellen** — Aufsichtsbehörden verlangen explizit gleiche Sichtbarkeit (DSK, EDPB, BGH-Urteil zu Cookie-Bannern).

### 4. Widerruf jederzeit möglich

- Link „Cookie-Einstellungen" im Footer / Datenschutzerklärung
- Widerruf so einfach wie Erteilung (Art. 7 Abs. 3 DSGVO)

## Häufige Fehler

| Annahme | Realität |
|---------|----------|
| „Wir nutzen IP-Anonymisierung in GA4 — keine Einwilligung nötig" | Falsch — TTDSG verlangt Einwilligung schon für das Setzen des Cookies, unabhängig von Anonymisierung |
| „Der Cookie-Banner erlaubt 'Weiter ohne'" | Wenn „Ablehnen" weniger prominent ist, ist die Einwilligung nicht freiwillig (Art. 4 Nr. 11 DSGVO) |
| „Nur 'Notwendig' aktiv und User klickt OK = wir können Analytics laden" | Nein — User muss Analytics explizit aktiv anklicken |
| „Wir nutzen serverseitiges Tracking, kein Cookie nötig" | Server-Side-GTM speichert immer noch Identifier am Endgerät → Einwilligung |
| „LocalStorage ist kein Cookie, also TTDSG egal" | Falsch — TTDSG gilt für alle Speicherungen auf Endgeräten |

## Aufsichtsbehörden-Praxis (DACH, Stand Mai 2026)

- **DSK** prüft Cookie-Banner aktiv — Abmahnungen häufig
- **österreichische DSB** hat 2022 Google Analytics als unzulässig eingestuft (Schrems-II-bedingt) — DPF-Reform mildert das, aber keine Generalfreigabe
- **CNIL (FR)** verhängt regelmäßig sechsstellige Bußgelder (Google, Amazon, Microsoft)
- **EDÖB (CH)** prüft seit revDSG verstärkt

## Quellen

- TTDSG (Volltext): <https://www.gesetze-im-internet.de/ttdsg/>
- DSGVO Art. 7 (Einwilligung): <https://gdpr-info.eu/art-7-gdpr/>
- ePrivacy-RL 2002/58/EG: <https://eur-lex.europa.eu/eli/dir/2002/58/oj>
- DSK Orientierungshilfe Telemedien (OH Telemedien 2021): <https://www.datenschutzkonferenz-online.de/media/oh/20211220_oh_telemedien.pdf>
- BGH Urteil „Planet49" (28.5.2020): <https://www.bundesgerichtshof.de/SharedDocs/Pressemitteilungen/DE/2020/2020065.html>
- EDPB Guidelines 03/2022 zu Dark Patterns: <https://www.edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-032022-deceptive-design-patterns-social-media_en>
