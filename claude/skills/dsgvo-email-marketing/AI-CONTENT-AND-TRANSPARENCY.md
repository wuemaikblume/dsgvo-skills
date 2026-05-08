# AI-personalisierter Newsletter-Content (EU AI Act Art. 50 + DSGVO Art. 13/22)

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, sobald LLM-/GenAI-APIs Inhalte für werbliche E-Mails erzeugen oder anpassen — Subject-Lines, Body-Variation, dynamische Empfehlungen, AI-generierte Bilder, Konversations-Newsletter. **Wohnsitz** für die AI-Act-Pflichten innerhalb der E-Mail-Marketing-Familie. Norm-Wohnsitz für Drittland-Aspekt der LLM-API selbst (DPF/SCCs für OpenAI, Anthropic, Mistral, Cohere) bleibt `dsgvo-third-country-transfer/PROVIDERS.md`. DSFA-Wohnsitz bleibt `dsgvo-third-country-transfer/DPIA.md`.

## Stichtag

**EU AI Act Art. 50 wird am 2. August 2026 unmittelbar anwendbar** (AI Act in Kraft seit 1. August 2024; Art. 50 Transparenz-Pflichten greifen 24 Monate später). Sanktionen bei Verstoß bis **€ 7,5 Mio. oder 1,5 % weltweiter Konzern-Jahresumsatz** (Art. 99 Abs. 4 lit. c AI Act, je nachdem was höher ist).

## Was Art. 50 für Marketing-E-Mail wirklich bedeutet — und was nicht

**Häufige Fehlannahme nach erstem Lesen:** „Jede AI-generierte Marketing-Mail muss als KI markiert werden." Das stimmt so nicht. Art. 50 differenziert nach Akteur (Provider vs Deployer) und Inhaltstyp. Für typische werbliche Newsletter greift nur ein Teil der Pflichten — andere DSGVO-Pflichten (Art. 13 Transparenz, Art. 22 automatisierte Entscheidung) sind hier oft die schärfere Hürde.

| Art. 50 Absatz | Wer ist verpflichtet | Was | Greift bei Werbe-Newsletter? |
|---|---|---|---|
| **Abs. 1** Interaktions-Transparenz | Provider des AI-Systems | Empfänger informieren, dass er mit AI interagiert | **Ja**, wenn der Newsletter ein interaktives AI-Element hat (Reply-an-AI, eingebetteter Chatbot, Konversations-Newsletter) |
| **Abs. 2** Maschinenlesbare Output-Markierung | Provider des Generative-AI-Systems | Ausgaben (Audio/Bild/Video/Text) maschinenlesbar als künstlich kennzeichnen (z. B. via C2PA, Wasserzeichen) | **Provider-Pflicht** (OpenAI, Anthropic). Deployer darf die Markierung **nicht entfernen**. |
| **Abs. 4 Satz 1** Deepfake-Disclosure | Deployer | Bild/Audio/Video, das Deepfake darstellt, als AI-generiert offenlegen | **Ja**, wenn AI-generierte Bilder mit Personenbezug realistisch wirken. Bei klar stilisierten Illustrationen greift die „offensichtlich"-Ausnahme. |
| **Abs. 4 Satz 2** Text-Disclosure für öffentliches Interesse | Deployer | AI-generierten/manipulierten Text zu **„Themen von öffentlichem Interesse"** offenlegen | **Nein** für klassische Produkt-Werbung. **Ja** für Newsletter mit AI-generierten politischen, gesundheitlichen, journalistischen Inhalten — **es sei denn**, der Inhalt durchläuft menschliche redaktionelle Kontrolle und eine natürliche/juristische Person trägt die Verantwortung. |
| **Abs. 5** Form der Information | beide | Klar und unterscheidbar, spätestens bei erster Interaktion / Exposition | gilt immer dort, wo Abs. 1–4 greift |

**Praktische Konsequenz für 90 % der Marketing-Newsletter:**

- AI-generierte Subject-Lines, Body-Variationen, Produkt-Empfehlungen, A/B-Test-Texte → **NICHT** vom Art. 50-Markierungspflicht-Teil erfasst (kein „öffentliches Interesse" iSd Abs. 4).
- ABER: DSGVO Art. 13 Transparenz-Pflicht + ggf. Art. 22 automatisierte Entscheidung greifen weiterhin.
- Drittland-Übertragung an LLM-API ist eigene Baustelle (siehe `dsgvo-third-country-transfer/PROVIDERS.md`).

## Decision Tree: Was muss ich für AI-im-Newsletter tun?

```
1. Mail enthält AI-generierten/-modifizierten Inhalt?
   ├─ Nein → Skill greift nicht. Standard-DSGVO/UWG-Pflichten in CONSENT-AND-DOI.md.
   └─ Ja → weiter

2. Welcher Inhaltstyp wurde von AI erzeugt?
   ├─ Subject-Line / Body-Text (klassisch werblich) → DSGVO-Pfad (Schritt 5)
   ├─ Produktbild / Personen-Foto realistisch → Art. 50 Abs. 4 Satz 1 (Deepfake-Pfad, Schritt 3)
   ├─ Newsletter-Artikel zu Gesundheit / Politik / Journalismus → Art. 50 Abs. 4 Satz 2 (Public-Interest-Pfad, Schritt 4)
   └─ Interaktives Element (AI-Chatbot, „antworten Sie um mit KI zu sprechen") → Art. 50 Abs. 1 (Interaktions-Pfad, Schritt 6)

3. Deepfake-Pfad (Bild/Audio/Video):
   ├─ Klar stilisiert / offensichtlich künstlich (Cartoon, abstrakte Illustration)?
   │  └─ „offensichtlich"-Ausnahme greift → keine Disclosure-Pflicht.
   └─ Realistisch wirkend, Personenbezug oder verwechselbar mit echter Aufnahme?
      └─ Pflicht: visueller Disclosure-Hinweis + maschinenlesbare Markierung NICHT entfernen.
         (Provider-seitig kommt C2PA / Wasserzeichen automatisch — Deployer darf nur nicht strippen.)

4. Public-Interest-Text-Pfad:
   ├─ Inhalt durchläuft menschliche redaktionelle Kontrolle, Verantwortung dokumentiert?
   │  └─ Editorial-Control-Ausnahme greift → keine Disclosure-Pflicht.
   └─ Direkt-Versand ohne Review?
      └─ Pflicht: Disclosure im Body („Dieser Text wurde mit KI erstellt").

5. DSGVO-Pfad (auch wenn Art. 50 nicht greift — gilt IMMER bei AI-Personalisierung):
   ├─ Daten werden an LLM-API gesendet (E-Mail, Name, Verhaltens-Historie)?
   │  ├─ DPA mit LLM-Provider abgeschlossen? (Art. 28)
   │  ├─ DPF-Status / SCCs geklärt? (siehe PROVIDERS.md)
   │  ├─ Pseudonymisierung vor Versand wo möglich? (Art. 25)
   │  └─ Im VVT eingetragen? (Art. 30)
   ├─ Privacy Notice nennt AI-Personalisierung explizit als Verarbeitungs-Zweck? (Art. 13 Abs. 1 lit. c + e)
   ├─ Profiling-Charakter vorhanden (segmentierte Empfehlungen)?
   │  └─ Cross-Link DPIA-Schwelle: dsgvo-third-country-transfer/DPIA.md
   └─ Wesentlich beeinträchtigende automatisierte Entscheidung? (Art. 22)
      └─ Edge-Case (z. B. dynamic pricing in Mail) — separate Rechtsgrundlage + Eingreifrecht.

6. Interaktions-Pfad (AI-Chatbot, Reply-to-AI):
   ├─ „Offensichtlich AI" aus Kontext (z. B. der Empfänger hat den Bot selbst eingerichtet)?
   │  └─ Ausnahme nach Art. 50 Abs. 1 greift.
   └─ Standard-Empfänger?
      └─ Pflicht: klarer Hinweis spätestens bei erster Interaktion („Sie schreiben mit einem KI-Assistenten").
```

## Code-Pattern 1: AI-personalisierte Subject-Line (kein Art. 50, aber Art. 13 + Drittland)

Häufigster Fall. Empfänger-spezifische Subject-Line, Body-Personalisierung, Produkt-Empfehlung — alles über LLM. Art. 50 schweigt, weil kommerzielle Werbung kein „öffentliches Interesse" ist. **Drei DSGVO-Pflichten bleiben:**

```ts
// Anti-Pattern: ungeprüft volle PII an LLM
const subject = await openai.chat.completions.create({
  model: 'gpt-4o',
  messages: [{
    role: 'user',
    content: `Schreibe eine Newsletter-Subject-Line für ${recipient.firstName} ${recipient.lastName}
              (E-Mail: ${recipient.email}, letzter Kauf: ${recipient.lastOrder.product},
               Standort: ${recipient.city}). Thema: ${campaign.theme}.`
  }]
});
// → US-Drittlandtransfer voller Personendaten ohne Zweck-Notwendigkeit
// → keine Pseudonymisierung (Art. 25)
// → AI-Personalisierung wahrscheinlich nicht im Privacy Notice → Art. 13-Verstoß
```

```ts
// Pattern: Datenminimierung + Pseudonymisierung + Privacy-Notice-Cross-Check
import { hasConsent, isInPrivacyNotice } from '@/compliance';

async function generatePersonalizedSubject(recipient: Recipient, campaign: Campaign) {
  // 1. Privacy-Notice-Cross-Check (zur Build-/Deploy-Zeit, nicht je Request)
  if (!isInPrivacyNotice('ai_personalization_marketing')) {
    throw new Error('AI-Personalisierung nicht in der aktuellen Datenschutzerklärung — Art. 13 verletzt');
  }
  // 2. Consent-Granularität (separate Einwilligung über Newsletter-Consent hinaus, EDPB 05/2020)
  if (!hasConsent(recipient, 'ai_personalization')) {
    return campaign.defaultSubject;
  }
  // 3. Pseudonymisierung — nur Verhaltens-Token an LLM, keine direkten Identifier
  const segmentToken = await pseudonymize({
    interestCategory: recipient.interestCategory,
    lastEngagementBucket: recipient.lastEngagementBucket, // z. B. "0-7d", "8-30d"
    purchaseRange: recipient.purchaseRange,               // z. B. "small", "medium", "vip"
  });
  // 4. Drittland-Disziplin: dokumentierter Provider mit DPA + DPF/SCCs (siehe PROVIDERS.md)
  const subject = await openai.chat.completions.create({
    model: 'gpt-4o',
    messages: [{
      role: 'user',
      content: `Schreibe eine Newsletter-Subject-Line für Empfänger-Profil ${segmentToken}.
                Kampagnen-Thema: ${campaign.theme}. Maximal 60 Zeichen.`
    }],
  });
  // 5. Audit-Log (Art. 30 VVT-Pflicht): wann, welcher Profil-Token, welches Modell, ohne Klartext-PII
  await logAiPersonalization({
    campaignId: campaign.id,
    recipientHash: recipient.hashedId,
    profileToken: segmentToken,
    model: 'gpt-4o',
    timestamp: new Date(),
  });
  return subject.choices[0].message.content;
}
```

## Code-Pattern 2: Footer für AI-personalisierte Mail (Best Practice, nicht Pflicht)

Auch wenn Art. 50 Abs. 4 für reine Werbung **nicht** greift, ist ein knapper Hinweis im Footer Best Practice — er stärkt die Transparenz nach Art. 13 DSGVO und schützt vor Aufsichts-Beanstandungen, die KI-Personalisierung im Marketing zunehmend in den Blick nehmen. Beispiel-Formulierung:

```html
<!-- Footer-Block, optional aber empfohlen bei AI-personalisiertem Inhalt -->
<p style="font-size: 11px; color: #666; line-height: 1.4;">
  Diese E-Mail wurde teilweise mit Künstlicher Intelligenz personalisiert
  (Subject-Line und Produkt-Empfehlungen). Details, welche Daten dafür
  verarbeitet werden, finden Sie in unserer
  <a href="https://example.de/datenschutz#ai-personalisierung">Datenschutzerklärung</a>.
  Sie können der AI-Personalisierung jederzeit widersprechen
  (<a href="https://example.de/preferences">Präferenz-Center</a>),
  ohne den Newsletter abzubestellen.
</p>
```

Wichtig:
- Hinweis ist **kein Substitut** für die Privacy-Notice-Aktualisierung (Art. 13).
- Wenn Empfänger der AI-Personalisierung widerspricht, muss die Verarbeitung sofort ausgesetzt werden (Art. 21 Abs. 2 DSGVO — Werbe-Widerspruch ist absolut).
- Granulares Opt-Out: AI-Personalisierung getrennt vom Newsletter-Empfang abwählbar.

## Code-Pattern 3: Interaktiver AI-Chatbot in der Mail (Art. 50 Abs. 1 — Pflicht)

Beispiel: Newsletter mit „Antworten Sie auf diese Mail, um mit unserem KI-Assistenten zu sprechen" oder eingebetteter Reply-Widget.

```html
<!-- Pflicht-Disclosure im Body, klar sichtbar VOR der ersten Interaktion -->
<div style="border-left: 3px solid #c41e1e; padding: 12px; background: #fafafa;">
  <strong>Hinweis:</strong> Wenn Sie auf diese E-Mail antworten, schreiben Sie
  mit einem KI-Assistenten (kein menschlicher Mitarbeiter). Ihre Antwort wird
  vom KI-System verarbeitet und beantwortet. Möchten Sie stattdessen mit einem
  Menschen sprechen, wenden Sie sich an
  <a href="mailto:support@example.de">support@example.de</a>.
</div>
```

```ts
// Server-Pattern: Reply-Webhook erkennt AI-Adressat, antwortet mit weiterer Disclosure
async function handleIncomingReply(email: ParsedEmail) {
  if (email.to.endsWith('@ai.example.de')) {
    const aiResponse = await chatWithAI(email.body, email.from);
    await sendEmail({
      to: email.from,
      subject: `Re: ${email.subject}`,
      body: `[Diese Nachricht wurde von einem KI-Assistenten verfasst]

${aiResponse}

— KI-Assistent von Beispiel GmbH
Möchten Sie mit einem Menschen sprechen? support@example.de`
    });
  }
}
```

Pflicht-Elemente nach Art. 50 Abs. 1 + Abs. 5:
- **Klar und unterscheidbar** (visuelle Hervorhebung, nicht in Fließtext versteckt)
- **Spätestens bei erster Interaktion** (also im Hinweis-Body, nicht erst in der KI-Antwort)
- Sprache: in der Sprache des Empfängers (DACH = Deutsch)

## Code-Pattern 4: AI-generiertes Bild im Newsletter (Art. 50 Abs. 4 Satz 1 — Deepfake-Pfad)

Trifft zu, wenn ein AI-generiertes Bild realistisch wirkt und Personenbezug hat — z. B. KI-Foto eines „Test-Kunden", KI-erzeugtes Werbebild mit künstlichem Model.

```ts
// Pattern: Deepfake-Disclosure als Caption + Provider-Markierung NICHT entfernen
async function embedAiImage(imageUrl: string, isRealistic: boolean, hasPersons: boolean) {
  // 1. C2PA-Manifest beim Download NICHT strippen (lossy compression vermeiden)
  const image = await fetchPreservingMetadata(imageUrl);

  // 2. Wenn realistisch + Personenbezug → Disclosure-Pflicht
  const disclosureRequired = isRealistic && hasPersons;
  return {
    html: `
      <figure>
        <img src="${image.cid}" alt="${image.alt}" />
        ${disclosureRequired ? `
          <figcaption style="font-size: 11px; color: #666;">
            Bild mit Künstlicher Intelligenz erzeugt. Die abgebildete Person ist nicht real.
          </figcaption>
        ` : ''}
      </figure>
    `,
    metadata: image.c2paManifest, // Original-Markierung erhalten
  };
}
```

Anti-Pattern, das schon mehrere Marken in EU-Aufsichts-Beschwerden gebracht hat: AI-Bild eines „zufriedenen Kunden mit Testimonial-Zitat", ohne Disclosure. Das ist Deepfake-Charakter (echter Eindruck + Personenbezug) **und** UWG-§-3-Irreführung — doppelte Front.

## DSFA-Trigger durch AI-Personalisierung

AI-Personalisierung verschärft die Profiling-Schwelle aus EDPB WP 248 (Kriterien-Katalog für DSFA-Pflicht). Wenn auch nur eines der folgenden Merkmale erfüllt ist, ist eine DSFA-Voraberhebung zwingend:

- AI-Personalisierung kombiniert Verhaltens-, Standort- und Kauf-Historie
- Massen-Versand (> 100.000 Empfänger) mit AI-Personalisierung
- AI-Personalisierung enthält Empfehlungen mit Auswirkung auf Versicherungs-/Kredit-/Job-Entscheidungen (selten im Newsletter, kommt bei B2B-Lead-Scoring vor)
- AI-Personalisierung verarbeitet Art. 9-Daten (Gesundheits-Newsletter, Pharma, Religion, Sexualität)
- AI-Personalisierung gegenüber Kindern (< 16 Jahre, Art. 8 DSGVO) — siehe `CONSENT-AND-DOI.md` Sektion „Kinder-Newsletter"

Vollständige DSFA-Mechanik (Inhalts-Anforderungen Art. 35 Abs. 7, Aufsichts-Konsultation Art. 36) in `dsgvo-third-country-transfer/DPIA.md`.

## Pre-Send-Checkliste für AI-personalisierte Mailings

Vor jedem Versand-Job, der Empfänger-Daten an einen LLM weitergibt, sieben Fragen abklären:

| # | Frage | Norm | wo geprüft |
|---|---|---|---|
| 1 | Hat der LLM-Provider DPA + DPF-Status oder SCCs? | Art. 28 + Kap. V | `dsgvo-third-country-transfer/PROVIDERS.md` |
| 2 | Welche Empfänger-Daten gehen wirklich an den LLM (Datenminimierung)? | Art. 5 Abs. 1 lit. c, Art. 25 | Code-Review vor Deploy |
| 3 | Pseudonymisierung implementiert (Token statt direkter Identifier)? | Art. 25 | siehe Pattern 1 |
| 4 | AI-Personalisierung im Privacy Notice (Art. 13 Abs. 1 lit. c + e + Abs. 2 lit. f)? | Art. 13 | Datenschutzerklärung-Audit |
| 5 | Eigene granular-abwählbare Einwilligung für AI-Personalisierung? | Art. 6 Abs. 1 lit. a + Art. 7 + EDPB 05/2020 | `CONSENT-AND-DOI.md` |
| 6 | Im VVT eingetragen mit Verarbeitungs-Zweck „AI-Personalisierung Marketing"? | Art. 30 | VVT-Eintrag |
| 7 | Greift einer der Art. 50-Pfade (interaktiv / Deepfake-Bild / Public-Interest-Text)? | AI Act Art. 50 | Decision Tree oben |

## Code of Practice — Stand und Bedeutung

Die Europäische Kommission veröffentlicht den **Code of Practice on Marking and Labelling of AI-Generated Content** als **freiwilliges Compliance-Werkzeug**. Stand Mai 2026: erster Entwurf vom 17. Dezember 2025, weiterer Entwurf März 2026, finale Fassung erwartet **Juni 2026** — also vor dem Stichtag 2. August 2026.

Der Code nennt keine spezifische Technologie (kein C2PA-Zwang), sondern fordert „wirksame, interoperable, robuste und zuverlässige" Lösungen. C2PA ist die derzeit reifste Implementierung der Manifest-Schicht; daneben werden unsichtbare Wasserzeichen und Fingerprinting-Verfahren empfohlen. Wer den Code beachtet, kann die Compliance gegenüber Aufsichten leichter belegen — er muss es aber nicht.

Maintainer-Empfehlung: für Werbe-Newsletter ist der Code **kein Muss**, weil der Markierungs-Pflichtfall (Abs. 2 Provider, Abs. 4 Deployer) selten direkt zutrifft. Wer KI-Bilder ausliefert oder einen interaktiven Newsletter baut, sollte beim finalen Code (Juni 2026) reinschauen.

## Zusammenfassung in einem Satz

**Für 90 % der Marketing-Newsletter ist die AI-Act-Markierungs-Pflicht ein Schreckgespenst — die echten Pflichten kommen aus DSGVO Art. 13 (Transparenz im Privacy Notice), Art. 28 + Kap. V (DPA + DPF/SCCs für die LLM-API) und ggf. Art. 22 (Edge-Case automatisierte Entscheidung). Art. 50 wird relevant bei interaktiven AI-Elementen (Abs. 1), realistischen AI-Bildern mit Personenbezug (Abs. 4 Satz 1) und AI-Texten zu Themen öffentlichen Interesses ohne redaktionelle Kontrolle (Abs. 4 Satz 2).**

## Cross-Links

| Thema | siehe |
|---|---|
| LLM-Provider Drittland-Status (OpenAI, Anthropic, Mistral, Cohere) | `dsgvo-third-country-transfer/PROVIDERS.md` |
| DSFA-Mechanik bei Profiling / KI / Drittland | `dsgvo-third-country-transfer/DPIA.md` |
| Cloud Act für US-LLM-Anbieter (auch bei EU-Region) | `dsgvo-third-country-transfer/CLOUD-ACT.md` |
| Granulare Einwilligung „separates Häkchen für AI-Personalisierung" | `CONSENT-AND-DOI.md` Sektion „Bedingungen einer wirksamen Einwilligung" |
| Tracking-Pixel als eigene Verarbeitung (kein AI-Thema, aber gleiche Logik der Trennung) | `TRACKING-IN-MAIL.md` |
| Art. 21-Werbe-Widerspruch absolut + AI-Personalisierungs-Widerspruch | `UNSUBSCRIBE-AND-RETENTION.md` |
| Confirm-Mail darf KEIN AI-personalisierter Inhalt werden (Service-Mail-Klassifikation) | `SERVICE-VS-MARKETING.md` |
| Log-Scrubbing der LLM-API-Aufrufe (PII in Prompts) | `dsgvo-auth-and-logging/LOGGING.md` |

## Quellen

- [EU AI Act Art. 50 (artificialintelligenceact.eu)](https://artificialintelligenceact.eu/article/50/) — exakter Wortlaut, deutsche und englische Fassung
- [EU AI Act offizielle Konsolidierung (eur-lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Verordnung (EU) 2024/1689 vom 13.06.2024, Inkrafttreten 01.08.2024, Art. 50 anwendbar 02.08.2026
- [Code of Practice on AI-Generated Content (digital-strategy.ec.europa.eu)](https://digital-strategy.ec.europa.eu/en/policies/code-practice-ai-generated-content) — Status-Seite, Entwürfe, Zeitplan
- [Commission publishes first draft Code of Practice (17.12.2025)](https://digital-strategy.ec.europa.eu/en/news/commission-publishes-first-draft-code-practice-marking-and-labelling-ai-generated-content) — Entwurfs-Beleg
- [DSK-Orientierungshilfe „KI und Datenschutz" (Juni 2025)](https://www.datenschutzkonferenz-online.de/orientierungshilfen.html) — DACH-Auslegung der DSGVO-Pflichten bei KI
- [BfDI KI-Handreichung 2025](https://www.bfdi.bund.de/SharedDocs/Downloads/DE/DokumenteBfDI/Dokumente-allg/2025/Handreichung-KI.pdf) — Bundes-Sicht
- [DSGVO Art. 13, Art. 22, Art. 35 (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679)
- [EDPB Guidelines 05/2020 on Consent](https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-052020-consent-under-regulation-2016679_en) — Granularität separater Einwilligung
- [EDPB WP 248 — DSFA-Schwellen](https://ec.europa.eu/newsroom/article29/items/611236) — Profiling-Kriterium

## Stand & Maintainer-Hinweis

**Stand:** Mai 2026, v1.0 dieser Sub-Datei (eingeführt mit `dsgvo-email-marketing` v1.7).

**PRE-TAG-HINWEIS:** Vor dem Tag `v1.7` muss diese Datei extern fakten-gegengeprüft werden (Tiefenrecherche-Tool: Perplexity Deep Research, Gemini Deep Research, oder vergleichbar). Insbesondere zu verifizieren:
- Definitive Anwendbarkeit Art. 50 Abs. 4 Satz 2 auf kommerzielle Newsletter (DACH-Aufsichts-Position kann strenger sein als der reine Wortlaut)
- Code-of-Practice-Finalfassung Juni 2026 — Inhalt und ggf. konkrete Tech-Anforderungen
- Sanktionshöhe Art. 99 Abs. 4 lit. c (€ 7,5 Mio. / 1,5 % vs. höhere Stufen)
- DSK-Veröffentlichungen 2026 zur AI-Act-Auslegung im Marketing-Kontext
- Fragebogen / Umsetzungs-Hinweise von Bayerisches Landesamt für Datenschutzaufsicht (BayLDA), LfDI BW, HmbBfDI

**Disclaimer:** Best-Practice-Sammlung, keine Rechtsberatung. Bei Massen-AI-Personalisierung, Health-/Finanz-Marketing mit AI, B2C-AI-Chatbot-Newsletter mit Kindern als Zielgruppe: Anwalt + Datenschutzbeauftragter VOR Live-Schaltung.
