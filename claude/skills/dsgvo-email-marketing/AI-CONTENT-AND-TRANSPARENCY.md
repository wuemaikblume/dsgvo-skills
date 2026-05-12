# AI-personalisierter Newsletter-Content (EU AI Act Art. 50 + DSGVO Art. 13/22)

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, sobald LLM-/GenAI-APIs Inhalte für werbliche E-Mails erzeugen oder anpassen — Subject-Lines, Body-Variation, dynamische Empfehlungen, AI-generierte Bilder, Konversations-Newsletter. **Wohnsitz** für die AI-Act-Pflichten innerhalb der E-Mail-Marketing-Familie. Norm-Wohnsitz für Drittland-Aspekt der LLM-API selbst (DPF/SCCs für OpenAI, Anthropic, Mistral, Cohere) bleibt `dsgvo-third-country-transfer/PROVIDERS.md`. DSFA-Wohnsitz bleibt `dsgvo-third-country-transfer/DPIA.md`.

## Stichtag

**EU AI Act Art. 50 wird am 2. August 2026 unmittelbar anwendbar** (Verordnung (EU) 2024/1689 in Kraft seit 1. August 2024; Art. 50 Transparenz-Pflichten greifen 24 Monate später, Art. 113 lit. b AI Act).

**Sanktionen bei Verstoß gegen Art. 50: bis € 15 Mio. oder 3 % des weltweiten Konzern-Jahresumsatzes**, je nachdem was höher ist (Art. 99 Abs. 4 lit. g AI Act — Buchstabe g listet explizit „Transparenzpflichten für Anbieter und Betreiber gemäß Artikel 50"). Die niedrigere Stufe von € 7,5 Mio. / 1,5 % aus Art. 99 Abs. 5 betrifft nur falsche Angaben gegenüber Behörden, nicht Art. 50-Verstöße.

**DSGVO bleibt unberührt:** Art. 2 Abs. 7 AI Act stellt klar, dass die DSGVO und die ePrivacy-Richtlinie weitergelten. AI-Act-Compliance ersetzt also keine DSGVO-Pflicht — sie tritt daneben.

## Was Art. 50 für Marketing-E-Mail wirklich bedeutet — und was nicht

**Häufige Fehlannahme nach erstem Lesen:** „Jede AI-generierte Marketing-Mail muss als KI markiert werden." Das stimmt so nicht. Art. 50 differenziert nach Akteur (Provider vs Deployer) und Inhaltstyp. Für typische werbliche Newsletter greift nur ein Teil der Pflichten — andere DSGVO-Pflichten (Art. 13 Transparenz, Art. 22 automatisierte Entscheidung) sind hier oft die schärfere Hürde.

| Art. 50 Absatz | Wer ist verpflichtet | Was | Greift bei Werbe-Newsletter? |
|---|---|---|---|
| **Abs. 1** Interaktions-Transparenz (Erwägungsgrund 132) | Anbieter des AI-Systems | Empfänger informieren, dass er mit AI interagiert — *„es sei denn, dies ist aus Sicht einer angemessen informierten, aufmerksamen und verständigen natürlichen Person aufgrund der Umstände und des Kontexts offensichtlich"* | **Ja**, wenn der Newsletter ein interaktives AI-Element hat (Reply-an-AI, eingebetteter Chatbot, Konversations-Newsletter). Bei sprechender Sender-Adresse (`chatbot@…`/`ai@…`) plus klarem Kontext kann die Offensichtlichkeits-Ausnahme greifen. |
| **Abs. 2** Maschinenlesbare Output-Markierung (Erwägungsgrund 133) | Anbieter des Generative-AI-Systems | Ausgaben (Audio/Bild/Video/Text) maschinenlesbar als künstlich kennzeichnen (Wasserzeichen, Metadaten, kryptografische Methoden, Fingerprints) | **Anbieter-Pflicht** (OpenAI, Anthropic, Mistral). Aus Art. 50 Abs. 2 selbst folgt für Deployer **keine** ausdrückliche „Markierung-nicht-entfernen"-Pflicht — diese Erwartung ergibt sich praktisch aus Abs. 4 (für Deepfakes) sowie aus dem Code of Practice und API-Vertragsbedingungen. |
| **Abs. 4 Satz 1** Deepfake-Disclosure (Erwägungsgrund 134) | Betreiber/Deployer | Bild/Audio/Video, das ein **Deepfake** i. S. d. Art. 3 Nr. 60 ist (ähnelt **wirklichen** Personen, Gegenständen, Orten, Einrichtungen oder Ereignissen *und* erscheint fälschlicherweise echt), als AI-generiert offenlegen | **Selten** bei reiner Werbung. Greift bei realistischen AI-Bildern mit Bezug zu existierenden Personen/Marken/Orten. **Achtung:** fiktiv-fotorealistische „zufriedene Kunden"-Bilder sind grammatikalisch oft *keine* Deepfakes nach Art. 3 Nr. 60 — lösen aber UWG § 5/§ 5a-Irreführungsrisiko aus (siehe Sektion „UWG-Schiene"). |
| **Abs. 4 Satz 2** Text-Disclosure für öffentliches Interesse (Erwägungsgrund 134) | Betreiber/Deployer | AI-generierten/manipulierten Text *„der veröffentlicht wird, um die Öffentlichkeit über Angelegenheiten von öffentlichem Interesse zu informieren"* offenlegen | **Nein** für klassische Produkt-Werbung. **Ja** für Newsletter mit AI-generierten politischen, gesundheitlichen, journalistischen Inhalten — **es sei denn**, der Inhalt durchläuft echte menschliche redaktionelle Kontrolle und eine namentlich benannte natürliche/juristische Person trägt die redaktionelle Letztverantwortung. |
| **Abs. 4 Werke-Ausnahme** | Betreiber/Deployer | Bei „offensichtlich künstlerischem, kreativem, satirischem, fiktionalem oder analogem Werk" beschränkt sich die Pflicht auf eine *„geeignete Offenlegung, die die Darstellung oder den Genuss des Werks nicht beeinträchtigt"* | Eine **separate generelle „offensichtlich künstlich"-Ausnahme für Deepfakes (etwa Cartoons) gibt es im Wortlaut nicht** — die Reduktion gilt für Werke i. d. S. (Kreativ-/Satire-/Fiktion). Cartoon-Stil entzieht ein Bild eher bereits der Deepfake-Definition (kein „echt-Wahrheitsgehalt-Eindruck"), das ist aber ein anderer Argumentationsweg. |
| **Abs. 5** Form der Information | Anbieter + Betreiber | Klar und unterscheidbar, **spätestens zum Zeitpunkt der ersten Interaktion oder Exposition** | gilt immer dort, wo Abs. 1–4 greift |

**Praktische Konsequenz für die Mehrzahl typischer Marketing-Newsletter:**

- AI-generierte Subject-Lines, Body-Variationen, Produkt-Empfehlungen, A/B-Test-Texte → **NICHT** vom Art. 50-Markierungspflicht-Teil erfasst (kein „öffentliches Interesse" iSd Abs. 4 Satz 2; kein Deepfake; keine AI-Interaktion).
- ABER: DSGVO Art. 13 Transparenz-Pflicht + ggf. Art. 22 automatisierte Entscheidung greifen weiterhin.
- Drittland-Übertragung an LLM-API bleibt eigene DSGVO-Kapitel-V-Baustelle (siehe `dsgvo-third-country-transfer/PROVIDERS.md`), unabhängig vom AI Act (Art. 2 Abs. 7).
- **Zweite Front: UWG.** Selbst wenn Art. 50 nicht greift, sind irreführende AI-Inhalte (z. B. fiktive Testimonials ohne Hinweis) wettbewerbsrechtlich abmahnbar — siehe Sektion „UWG-Schiene".

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

3. Deepfake-Pfad (Bild/Audio/Video) — Definition Art. 3 Nr. 60 ist eng:
   ├─ Bezug zu wirklichen Personen/Gegenständen/Orten/Einrichtungen/Ereignissen
   │  UND erscheint fälschlicherweise als echt/wahrheitsgemäß?
   │  ├─ Ja → Pflicht nach Art. 50 Abs. 4 Satz 1: sichtbare Offenlegung
   │  │       + Provider-Markierung (C2PA, Wasserzeichen) NICHT entfernen.
   │  └─ Werke-Ausnahme prüfen: Teil eines offensichtlich künstlerischen/kreativen/
   │     satirischen/fiktionalen/analogen Werks?
   │     └─ Pflicht reduziert sich auf geeignete Offenlegung, die das Werk
   │        nicht beeinträchtigt (Fußnote, Bildnachweis).
   ├─ Klar stilisiert (Cartoon, abstrakte Illustration, offenkundig künstlich)?
   │  └─ Bereits nicht Deepfake i. S. d. Art. 3 Nr. 60 (kein Echtheits-Eindruck) →
   │     keine Art.-50-Pflicht.
   └─ Vollständig fiktive, aber fotorealistische Person ohne Bezug zu wirklicher Person
      (z. B. „zufriedener Kunde" als KI-Modell)?
      ├─ Streng grammatikalisch: kein Deepfake (kein „bestehendes" Vorbild).
      └─ ABER: UWG § 5/§ 5a Irreführung über echte Kundenerfahrung — eigene Front,
         siehe Sektion „UWG-Schiene". Best Practice: trotzdem offenlegen.

4. Public-Interest-Text-Pfad:
   ├─ Text mit dem Ziel veröffentlicht, die Öffentlichkeit über Angelegenheiten
   │  öffentlichen Interesses zu informieren (Politik, Gesundheit, Sicherheit,
   │  Grundrechte, gesellschaftlich relevante Debatten)?
   │  ├─ Nein → klassische Werbung, kein Art. 50 Abs. 4 Satz 2.
   │  └─ Ja → weiter
   ├─ Editorial-Control-Ausnahme (strikt auszulegen):
   │  - namentlich benannter Verantwortlicher (natürliche/juristische Person),
   │  - dokumentierte manuelle Vorab-Prüfung pro Inhalt,
   │  - redaktionelle Verantwortung wird ausdrücklich übernommen.
   │  ├─ Erfüllt → keine Disclosure-Pflicht.
   │  └─ NICHT erfüllt durch reines Template-Approval oder bloße Stichprobenkontrolle
   │     bei massenhafter Personalisierung.
   └─ Direkt-Versand ohne echte Review?
      └─ Pflicht: Disclosure im Body („Dieser Text wurde mit KI erstellt").

5. DSGVO-Pfad (auch wenn Art. 50 nicht greift — gilt IMMER bei AI-Personalisierung):
   ├─ Daten werden an LLM-API gesendet (E-Mail, Name, Verhaltens-Historie)?
   │  ├─ DPA mit LLM-Provider abgeschlossen? (Art. 28)
   │  ├─ DPF-Status / SCCs geklärt? (siehe PROVIDERS.md)
   │  ├─ Datenminimierung + risikoadjustierte Pseudonymisierung? (Art. 5 lit. c, Art. 25, Art. 32)
   │  └─ Im VVT eingetragen? (Art. 30)
   ├─ Privacy Notice nennt AI-Personalisierung als eigenen Verarbeitungs-Zweck
   │  oder zumindest hinreichend konkret (Art. 13 Abs. 1 lit. c + e + Abs. 2 lit. f)?
   ├─ Profiling-Charakter vorhanden (segmentierte Empfehlungen)?
   │  └─ Cross-Link DPIA-Schwelle: dsgvo-third-country-transfer/DPIA.md
   └─ Wesentlich beeinträchtigende automatisierte Entscheidung? (Art. 22)
      └─ Edge-Case (z. B. dynamic pricing in Mail, Kredit-/Versicherungs-Scoring) —
         separate Rechtsgrundlage + Eingreifrecht.

6. Interaktions-Pfad (AI-Chatbot, Reply-to-AI):
   ├─ „Offensichtlich AI" aus Kontext für eine angemessen informierte,
   │  aufmerksame und verständige Person?
   │  ├─ Sprechende Sender-Adresse `chatbot@…`/`ai@…` + klarer Kontext (Empfänger
   │  │  hat Bot selbst eingerichtet, Reply-Button heißt „Frag den KI-Assistenten")
   │  │  → Ausnahme nach Art. 50 Abs. 1 letzter Halbsatz kann greifen.
   │  └─ Standard-Sender, human-emulierender Bot, kein klarer Hinweis →
   │     Ausnahme greift NICHT. Eng auslegen.
   └─ Pflicht-Fall:
      ├─ Hinweis spätestens zur ersten Interaktion oder Exposition (Abs. 5).
      ├─ Praxis: sichtbarer Disclosure in der ausgehenden Mail VOR dem Reply-Button
      │  (informierte Entscheidung) und/oder erste AI-Antwort beginnt mit
      │  „Diese Nachricht wurde von einem KI-Assistenten verfasst".
      └─ Bei human-emulierenden Bots: zusätzlicher Body-Disclaimer empfohlen.
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
  // 3. Pseudonymisierung — risikoadjustiert (Art. 25/32 DSGVO): bei umfangreicher
  //    oder sensibler Personalisierung Pflicht-nah, sonst stark empfohlen.
  //    Praxis: nur Verhaltens-Token an LLM, keine direkten Identifier.
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
- **Spätestens zur ersten Interaktion oder Exposition** (Abs. 5) — also im Hinweis-Body der ausgehenden Mail, nicht erst in der KI-Antwort
- Sprache: in der Sprache des Empfängers (DACH = Deutsch)

**Alternative über die Offensichtlichkeits-Ausnahme (Art. 50 Abs. 1 letzter Halbsatz):** Wenn die Sender-Adresse durchgehend `chatbot@…` oder `ai@…` lautet und der gesamte Touchpoint (Button-Label, Newsletter-Erklärung) konsequent „KI-Assistent" benennt, kann die Pflicht zur expliziten Disclosure entfallen. Eng auslegen — bei human-emulierendem Bot, generischer Sender-Adresse oder bei vulnerablen Empfängergruppen (Kinder, ältere Menschen) bleibt der explizite Hinweis Pflicht. Im Zweifel: Body-Disclaimer setzen.

## Code-Pattern 4: AI-generiertes Bild im Newsletter (Art. 50 Abs. 4 Satz 1 + UWG)

**Erste Frage: Greift Art. 3 Nr. 60 überhaupt?** Deepfake-Definition: KI-erzeugter/-manipulierter Inhalt, der **wirklichen** Personen, Gegenständen, Orten, Einrichtungen oder Ereignissen ähnelt und einer Person fälschlicherweise als echt oder wahrheitsgemäß erscheinen würde. Die meisten KI-Produktbilder mit erfundenem Model sind nach reinem Wortlaut **kein Deepfake** (kein „bestehendes" Vorbild). Trotzdem zwingt das UWG zur Offenlegung, wenn der fiktive Eindruck einen echten Kunden suggeriert — siehe Sektion „UWG-Schiene".

```ts
// Pattern: AI-Bild im Newsletter — Art. 50 (Deepfake) + UWG (Testimonial-Irreführung)
type AiImageContext = {
  resemblesRealPersonOrPlace: boolean;  // wirkliche Person/Marke/Ort nachgeahmt?
  evokesEchtheitseindruck: boolean;     // wirkt es wie eine echte Aufnahme?
  isObviouslyArtistic: boolean;         // Werke-Ausnahme (Cartoon, Satire, Fiktion)?
  suggestsCustomerTestimonial: boolean; // erweckt es Eindruck eines echten Kunden?
};

async function embedAiImage(imageUrl: string, ctx: AiImageContext) {
  // 1. Provider-Markierung NICHT entfernen (C2PA-Manifest, Wasserzeichen).
  //    Vermeide lossy compression auf Pixel-Ebene ohne Manifest-Re-Embedding.
  const image = await fetchPreservingMetadata(imageUrl);

  // 2. Art. 50 Abs. 4 Satz 1 — Deepfake i. S. d. Art. 3 Nr. 60?
  const isDeepfake = ctx.resemblesRealPersonOrPlace && ctx.evokesEchtheitseindruck;

  // 3. Werke-Ausnahme (Abs. 4 letzter Unterabsatz)?
  //    Reduziert die Pflicht auf geeignete, werkschonende Offenlegung — entfällt nicht ganz.
  const reducedToArtisticDisclosure = isDeepfake && ctx.isObviouslyArtistic;

  // 4. UWG-Front (§ 5/§ 5a) — unabhängig vom AI Act.
  //    Fiktiver „zufriedener Kunde" ohne Disclosure ist Irreführung; mit der Umsetzung
  //    der Omnibus-Richtlinie sind Fake-Testimonials/Bewertungen in der Schwarzen Liste.
  const uwgDisclosureRequired = ctx.suggestsCustomerTestimonial;

  const needsDisclosure = isDeepfake || uwgDisclosureRequired;
  const caption = reducedToArtisticDisclosure
    ? 'KI-erzeugt (künstlerische Darstellung).'
    : 'Bild mit Künstlicher Intelligenz erzeugt. Die abgebildete Person ist nicht real.';

  return {
    html: `
      <figure>
        <img src="${image.cid}" alt="${image.alt}" />
        ${needsDisclosure ? `<figcaption style="font-size: 11px; color: #666;">${caption}</figcaption>` : ''}
      </figure>
    `,
    metadata: image.c2paManifest,
  };
}
```

Anti-Pattern: AI-Bild eines „zufriedenen Kunden mit Testimonial-Zitat", ohne Hinweis. Selbst wenn die Person komplett fiktiv ist und damit aus dem engen Deepfake-Begriff fällt, bleibt es eine Irreführung nach § 5 UWG und potenziell Fake-Testimonial nach der Schwarzen Liste — abmahnbar durch Mitbewerber und Verbraucherschutzverbände.

## UWG-Schiene: Abmahnvektor jenseits der Aufsicht

Eine in der Praxis oft unterschätzte zweite Front: In der deutschen rechtswissenschaftlichen Diskussion (Stand März 2026, u. a. Taylor Wessing, Wettbewerbszentrale) werden die Transparenzpflichten aus Art. 50 AI Act zunehmend als **Marktverhaltensregelungen i. S. d. § 3a UWG** eingestuft. Konsequenz:

- **Direkte Abmahnung durch Mitbewerber und Verbraucherschutzverbände** möglich — unabhängig davon, ob eine Aufsichtsbehörde Art. 50 verfolgt. § 8 Abs. 3 UWG gibt Mitbewerbern, Wettbewerbszentrale und qualifizierten Wirtschafts-/Verbraucherverbänden Klage- und Abmahnbefugnis.
- **Doppelter Schadensvektor:** Aufsichtsbußgeld (bis 15 Mio. EUR / 3 % Konzernumsatz, Art. 99 Abs. 4 lit. g AI Act) **plus** UWG-Abmahnkosten + Unterlassungsanspruch + Schadensersatz.
- **Zusätzlich greifen die UWG-Klassiker unabhängig vom AI Act:**
  - **§ 5 UWG** (Irreführung): AI-generierte Fake-Erfahrungen, Fake-Testimonials, simulierte Reviews.
  - **§ 5a UWG** (Irreführung durch Unterlassen): wesentliche Information „mit KI erzeugt" wird verschwiegen.
  - **Schwarze Liste (Anhang zu § 3 Abs. 3 UWG)** in der Umsetzung der Omnibus-Richtlinie: gefälschte Bewertungen und Empfehlungen sind per se unlauter.

**Faustregel:** Wenn ein AI-generierter Inhalt einen Verbraucher in einer geschäftlichen Entscheidung beeinflusst und seine künstliche Natur nicht offengelegt ist, ist UWG-Risiko da — auch wenn der AI Act schweigt.

Aufsichts-/Berater-Prognosen (Stand Q1/2026) rechnen mit einer **Abmahnwelle ab dem 02.08.2026**, ähnlich wie bei den DSGVO-Cookie-Banner-Abmahnwellen 2021/2022.

## DSFA-Trigger durch AI-Personalisierung

AI-Personalisierung verschärft die Profiling-Schwelle aus EDPB-Guidelines (ex-WP 248, mittlerweile von der EDPB übernommen). Die deutsche DSK-Blacklist nennt unter Nr. 11 den Einsatz von KI zur Bewertung persönlicher Aspekte (Profiling) ausdrücklich als DSFA-pflichtigen Verarbeitungsvorgang. **Es gibt keinen festen Empfänger-Schwellenwert** — die DSFA-Pflicht entsteht aus der Kombination mehrerer Kriterien.

Konkret zwingend prüfen (mind. zwei der folgenden Merkmale → DSFA durchführen):

- AI-Personalisierung kombiniert mehrere Datenquellen (Verhaltens-, Standort-, Kauf-Historie, Inferenzbildung)
- Großer Datenumfang (umfangreiche Personalisierung über große Empfängergruppen — keine starre Zahl, aber im fünf- bis sechsstelligen Bereich typischerweise erfüllt)
- Neue Technologie i. S. d. Art. 35 Abs. 1 DSGVO (LLM-/GenAI-Einsatz qualifiziert sich)
- AI-Personalisierung enthält Empfehlungen mit Auswirkung auf Versicherungs-/Kredit-/Job-Entscheidungen (selten im Newsletter, kommt bei B2B-Lead-Scoring vor)
- AI-Personalisierung verarbeitet Art. 9-Daten (Gesundheits-Newsletter, Pharma, Religion, Sexualität)
- AI-Personalisierung gegenüber Kindern (< 16 Jahre, Art. 8 DSGVO) — siehe `CONSENT-AND-DOI.md` Sektion „Kinder-Newsletter"

Bei KI-Profiling als zentralem Verarbeitungszweck (DSK-Blacklist Nr. 11) ist die DSFA bereits aus diesem Grund allein zwingend. Vollständige DSFA-Mechanik (Inhalts-Anforderungen Art. 35 Abs. 7, Aufsichts-Konsultation Art. 36) in `dsgvo-third-country-transfer/DPIA.md`.

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

Die Europäische Kommission und das AI Office erarbeiten den **Code of Practice on Marking and Labelling of AI-Generated Content** als **freiwilliges Compliance-Instrument** (Rechtsgrundlage: Art. 50 Abs. 7 AI Act). Stand Mai 2026: erster Entwurf am **17. Dezember 2025** veröffentlicht, zweiter Entwurf am **5. März 2026** (Feedback-Frist bis 30. März 2026), finale Fassung erwartet **Juni 2026** — also knapp vor dem Stichtag 2. August 2026.

**Rechtliche Wirkung:** Der Code ist freiwillig. Signatories, die ihn umsetzen, können dies gegenüber Aufsichtsbehörden als Compliance-Demonstration nutzen. Eine klassische „presumption of conformity" wie bei harmonisierten Normen entsteht damit nicht, aber faktisch verschiebt sich die Beweislast: Wer den Code nicht zeichnet, muss eigenständig nachweisen, dass die genutzten Lösungen „wirksam, interoperabel, robust und zuverlässig" sind.

**Technischer Ansatz (Stand 2. Entwurf):** Mehrschichtig (Multi-Layer), nicht ein einzelner Standard.

- Provenienz-Metadaten (C2PA ist die ausgereifteste Implementierung)
- Robuste, imperzeptible Wasserzeichen auf Pixel-/Audio-Ebene
- Fingerprinting/Detection-Logging auf Anbieterseite
- Standardisiertes, visuell wahrnehmbares EU-Piktogramm für Endnutzer (von Betreibern konsistent zu platzieren)

**Maintainer-Empfehlung:** Für Werbe-Newsletter ist der Code **kein Muss**, weil der Markierungs-Pflichtfall (Abs. 2 Anbieter, Abs. 4 Betreiber) selten direkt zutrifft. Wer KI-Bilder ausliefert oder einen interaktiven Newsletter baut, sollte beim finalen Code (Juni 2026) reinschauen — insbesondere wegen des EU-Piktogramms, das praktische Auswirkungen auf den Footer-/Caption-Layout hat.

**Zusätzlich:** Die EU-Kommission hat am **8. Mai 2026** Entwurfsleitlinien zu Art. 50 in öffentliche Konsultation gegeben (separater Prozess zum Code of Practice). Endgültige Auslegung mancher Punkte (z. B. „matters of public interest", Editorial-Control-Schwelle) steht damit noch unter Vorbehalt.

## Zusammenfassung in einem Satz

**Für die Mehrzahl klassischer Marketing-Newsletter ist die AI-Act-Markierungs-Pflicht kleiner als oft befürchtet — die echten Pflichten kommen aus DSGVO Art. 13 (Transparenz im Privacy Notice), Art. 28 + Kap. V (DPA + DPF/SCCs für die LLM-API) und ggf. Art. 22 (Edge-Case automatisierte Entscheidung), sowie aus UWG § 5/§ 5a + Schwarzer Liste (Fake-Testimonials). Art. 50 AI Act selbst wird vor allem relevant bei interaktiven AI-Elementen (Abs. 1), Deepfakes i. S. d. Art. 3 Nr. 60 (Abs. 4 Satz 1) und AI-Texten zu Themen öffentlichen Interesses ohne redaktionelle Kontrolle (Abs. 4 Satz 2). Bußgeld-Obergrenze bei Art. 50-Verstoß: 15 Mio. EUR / 3 % weltw. Konzern-Jahresumsatz (Art. 99 Abs. 4 lit. g).**

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

**EU-Primärrecht und Service-Desk:**

- [Verordnung (EU) 2024/1689 (eur-lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — KI-Verordnung vom 13.06.2024, Inkrafttreten 01.08.2024
- [AI Act Service Desk Art. 50 (EU-Kommission, deutsch)](https://ai-act-service-desk.ec.europa.eu/de/ai-act/article-50) — Transparenzpflichten Anbieter/Betreiber
- [AI Act Service Desk Art. 3 (EU-Kommission, deutsch)](https://ai-act-service-desk.ec.europa.eu/de/ai-act/article-3) — Begriffsbestimmungen inkl. Nr. 60 Deepfake
- [AI Act Service Desk Art. 99 (EU-Kommission, deutsch)](https://ai-act-service-desk.ec.europa.eu/de/ai-act/article-99) — Sanktionen; Art. 50-Verstoß = Abs. 4 lit. g
- [AI Act Service Desk Art. 113 (EU-Kommission, deutsch)](https://ai-act-service-desk.ec.europa.eu/de/ai-act/article-113) — Geltungsbeginn (lit. b: 24-Monats-Frist)
- [AI Act Service Desk Art. 2 (EU-Kommission, deutsch)](https://ai-act-service-desk.ec.europa.eu/de/ai-act/article-2) — Anwendungsbereich + Abs. 7 (DSGVO unberührt)
- Erwägungsgründe 132 (Interaktions-Transparenz), 133 (maschinenlesbare Markierung Anbieter), 134 (Deepfake- + Text-Disclosure Betreiber)

**Code of Practice + Entwurfsleitlinien:**

- [Code of Practice on AI-Generated Content (digital-strategy.ec.europa.eu)](https://digital-strategy.ec.europa.eu/en/policies/code-practice-ai-generated-content) — Status-Seite, Entwürfe, Zeitplan
- [First Draft (17.12.2025)](https://digital-strategy.ec.europa.eu/en/news/commission-publishes-first-draft-code-practice-marking-and-labelling-ai-generated-content)
- Second Draft (05.03.2026, Konsultation bis 30.03.2026) — abrufbar über die Status-Seite
- Entwurfsleitlinien der Kommission zu Art. 50, öffentliche Konsultation seit 08.05.2026 (separater Prozess)

**DACH-Aufsichten:**

- [DSK-Orientierungshilfe „KI und Datenschutz" (06.05.2024)](https://www.datenschutzkonferenz-online.de/media/oh/DSK-Orientierungshilfe_KI_und_Datenschutz.pdf) — Grundsatz-Auslegung
- [DSK-OH „TOMs bei Entwicklung und Betrieb von KI-Systemen" (Juni 2025)](https://www.datenschutzkonferenz-online.de/media/oh/DSK-OH_KI-Systeme.pdf) — Privacy-by-Design-Konkretisierung
- DSK-OH zu RAG-Systemen (Oktober 2025) — Vektordatenbanken + externe LLM-Aufrufe
- [BfDI-Handreichung „KI in Behörden — Datenschutz von Anfang an mitdenken" (22.12.2025)](https://www.bfdi.bund.de/SharedDocs/Downloads/DE/DokumenteBfDI/Dokumente-allg/2025/Handreichung-KI.html)
- LfDI Baden-Württemberg: ONKIDA-Navigator (Orientierungshilfen-Navigator KI & Datenschutz)
- BayLDA: „KI-Checkliste" + Praxis-Check Handel
- EDÖB (Schweiz) + 60 Aufsichten: Gemeinsame Erklärung zu KI-generierten Bildern (23.02.2026)

**EU-Datenschutz:**

- [DSGVO (eur-lex)](https://eur-lex.europa.eu/legal-content/DE/TXT/?uri=CELEX:32016R0679) — insb. Art. 5, 13, 22, 25, 28, 30, 32, 35, 44 ff.
- [EDPB Guidelines 05/2020 on Consent](https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-052020-consent-under-regulation-2016679_en) — Granularität separater Einwilligung
- EDPB-Guidelines zu Profiling und DSFA (ex-WP 248/251, von EDPB übernommen)

**UWG / Wettbewerbsrecht:**

- [§ 5 UWG (gesetze-im-internet.de)](https://www.gesetze-im-internet.de/uwg_2004/__5.html) — Irreführung
- [§ 5a UWG](https://www.gesetze-im-internet.de/uwg_2004/__5a.html) — Irreführung durch Unterlassen
- § 3 Abs. 3 UWG + Anhang (Schwarze Liste) — Fake-Bewertungen/Testimonials per se unlauter (Omnibus-Umsetzung)
- § 3a UWG — Rechtsbruch-Tatbestand bei Verletzung von Marktverhaltensregelungen
- Wettbewerbszentrale: Leitfaden zur KI-Kennzeichnung (Februar 2026)

## Stand & Maintainer-Hinweis

**Stand:** Mai 2026, v1.0 dieser Sub-Datei (eingeführt mit `dsgvo-email-marketing` v1.7).

**Externer Tiefenrecherche-Review durchgeführt (Mai 2026)** mit drei voneinander unabhängigen KI-Tiefenrecherchen (Grok, ChatGPT/GPT-5.5, Gemini Deep Research). Primärquellen-Verifikation gegen AI Act Service Desk der EU-Kommission und EUR-Lex. Wesentliche Korrekturen gegenüber der ursprünglichen Entwurfsfassung:

- Sanktionsnorm: Art. 99 Abs. 4 **lit. g** (€ 15 Mio. / 3 %) statt fälschlich lit. c (€ 7,5 Mio. / 1,5 %)
- Erwägungsgrund-Zuordnung: EG 132 (Interaktion) / 133 (Anbieter-Markierung) / 134 (Betreiber-Disclosure)
- Werke-Ausnahme statt vager „offensichtlich künstlich"-Ausnahme präzise zitiert
- Deepfake-Definition (Art. 3 Nr. 60) strikt am Wortlaut „wirkliche" Personen/Objekte; fiktiv-realistische Personen-Testimonials nicht zwingend Deepfake, aber UWG-irreführend
- UWG-Schiene (§ 3a / § 5 / § 5a / Schwarze Liste) als eigenständige Abmahnvektor-Sektion ergänzt
- Editorial-Control-Ausnahme strikt: namentlich benannte Verantwortung + dokumentierte manuelle Prüfung
- Pseudonymisierung risikoadjustiert formuliert (Art. 25/32), nicht als ausnahmslose Pflicht
- DSFA-Schwelle: keine starre 100k-Empfänger-Zahl, DSK-Blacklist Nr. 11 als zwingender Auslöser bei KI-Profiling
- Code of Practice präziser: Multi-Layer-Ansatz, „Compliance-Demonstration" statt klassischer Konformitätsvermutung, Entwurfsleitlinien 08.05.2026 ergänzt

**Verbleibende offene Punkte (Stand Mai 2026, hier dokumentiert, weil Auslegung in Bewegung):**

- Finale Kommissions-Leitlinien zu Art. 50 (Konsultation 08.05.2026, Endfassung steht aus) — insbesondere zur Auslegung „matters of public interest" und zur Editorial-Control-Schwelle bei massenhaft personalisierten Inhalten
- Code of Practice Final-Fassung Juni 2026 — endgültige technische Marker-Standards
- DACH-Rechtsprechung speziell zu AI-Testimonial-Bildern in Werbemails (keine OLG-Entscheidung mit alleinigem AI-Bezug)
- Genaue quantitative DSFA-Schwelle bei skalierter AI-Personalisierung
- BSI-Empfehlungen zu C2PA und Wasserzeichen-Standards (Stand Mai 2026 keine spezifische Veröffentlichung)

**Disclaimer:** Best-Practice-Sammlung, keine Rechtsberatung. Bei Massen-AI-Personalisierung, Health-/Finanz-Marketing mit AI, B2C-AI-Chatbot-Newsletter mit Kindern als Zielgruppe: Anwalt + Datenschutzbeauftragter VOR Live-Schaltung.
