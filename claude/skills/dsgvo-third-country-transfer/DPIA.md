# Datenschutz-Folgenabschätzung (DSFA / DPIA, Art. 35 DSGVO)

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, wenn sensible Daten, Profiling, systematisches Tracking oder großvolumige Verarbeitungen im Spiel sind.

## Wann eine DSFA Pflicht ist (Art. 35 Abs. 1)

> „Voraussichtlich hohes Risiko für die Rechte und Freiheiten natürlicher Personen"

Konkret nach **Art. 35 Abs. 3 DSGVO**:

1. **Systematische, umfassende Bewertung** persönlicher Aspekte (Profiling) **mit Rechtsfolgen oder ähnlich erheblicher Beeinträchtigung** (z.B. Scoring, automatisierte Auswahl)
2. **Umfangreiche Verarbeitung sensibler Daten** (Art. 9) oder strafrechtlicher Daten (Art. 10)
3. **Systematische Überwachung öffentlich zugänglicher Bereiche** (z.B. Videoüberwachung im Eingangsbereich, IoT-Sensorik)

## DSK-Muss-Liste (Deutschland)

Die Datenschutzkonferenz hat eine [Liste](https://www.bfdi.bund.de/DE/Fachthemen/Inhalte/DSFA/DSFA-Muss-Liste.html) konkreter Verarbeitungstätigkeiten veröffentlicht, bei denen eine DSFA in Deutschland zwingend ist. Auszug (nicht erschöpfend):

- Großflächige Verarbeitung von Gesundheitsdaten
- Großflächige Verarbeitung biometrischer Daten zur eindeutigen Identifizierung
- Beschäftigtendatenverarbeitung mit erheblichem Eingriff (z.B. heimliche Überwachung)
- Verarbeitung von Daten, die nach Art. 9/10 DSGVO besonders schutzwürdig sind, in großem Umfang
- Profilbildung mit Auswirkungen auf Versicherungs-/Kreditkonditionen
- KI-basierte Entscheidungen mit erheblichen Auswirkungen
- Tracking und Standortdaten von Beschäftigten
- Datenverarbeitung in der Schule/Hochschule mit Auswirkungen auf Bildungschancen

## Trigger im SaaS-/KI-Kontext

Häufige Architektur-Entscheidungen, die eine DSFA-Prüfung auslösen:

| Architektur | DSFA-Risiko |
|-------------|-------------|
| KI-Prompting mit Kundendaten / E-Mails / Tickets (OpenAI, Anthropic) | hoch — Pseudonymisierung Pflicht; bei Gesundheits-/Finanzdaten DSFA zwingend |
| Automatisiertes Bewerber-Scoring | hoch — Art. 22 (automatisierte Entscheidung) + DSFA |
| Behavioral Analytics (Hotjar, FullStory) | mittel-hoch — Session-Recordings mit Tastatureingaben können sensibel sein |
| Großflächiges User-Profiling für Targeting | hoch |
| Standortbasierte Dienste mit Bewegungsprofilen | hoch |
| Video-/Voice-AI (Transkription, Stimm-Analyse) | hoch — ggf. Biometrie nach Art. 9 |
| KI-Training auf Customer-Data | hoch — Joint Controllership prüfen + DSFA |

## Decision Tree

```
1. Verarbeitung enthält Art. 9-Daten (Gesundheit, Biometrie, ...) ?
   ├─ Ja → großflächig? → DSFA Pflicht
   └─ Nein → weiter

2. Profiling mit erheblicher Wirkung (Scoring, Auswahl, Preis) ?
   ├─ Ja → DSFA Pflicht
   └─ Nein → weiter

3. Systematische Überwachung öffentlicher Bereiche ?
   ├─ Ja → DSFA Pflicht
   └─ Nein → weiter

4. Auf der DSK-Muss-Liste?
   ├─ Ja → DSFA Pflicht
   └─ Nein → weiter

5. Mindestens 2 Kriterien aus EDPB WP 248-Liste erfüllt ?
   (Bewertung/Scoring, Automatisierte Entscheidung, Systematische
    Überwachung, sensible Daten, großer Umfang, Datensätze
    kombiniert, vulnerable Personen, neue Technologien,
    Verhinderung von Rechtsausübung)
   ├─ Ja → DSFA Pflicht
   └─ Nein → DSFA optional, aber empfohlen wenn Drittland +
              sensible Daten + KI im Spiel
```

## Inhalt einer DSFA (Art. 35 Abs. 7)

1. **Systematische Beschreibung** der geplanten Verarbeitungsvorgänge und Zwecke
2. **Bewertung der Notwendigkeit und Verhältnismäßigkeit**
3. **Bewertung der Risiken** für die Betroffenen
4. **Geplante Abhilfemaßnahmen** (TOMs, Garantien, Sicherheitsvorkehrungen)

Konsultation des Datenschutzbeauftragten (Art. 35 Abs. 2). Bei verbleibendem hohen Risiko: **Konsultation der Aufsichtsbehörde** vor Beginn der Verarbeitung (Art. 36).

## Praxis-Tipp für KI-Workflows

Wer Claude / OpenAI / Anthropic mit produktiven Customer-Daten füttert:

1. **DSFA-Frage stellen, BEVOR die Pipeline gebaut wird** — nicht nachträglich
2. Pseudonymisierung im Prompt: Klarnamen → Tokens vor API-Versand, Re-Mapping nur lokal
3. **Datenkategorien-Audit:** stehen Gesundheit / Strafdaten / sensible Daten in den Trainingsdaten oder Prompts?
4. **Output-Filterung:** kann das LLM sensible Daten preisgeben?
5. **Logging:** keine Roh-Prompts mit PII in Logs, Sentry, Datadog (siehe `SKILL.md` Code-Patterns)

## Quellen

- DSGVO Art. 35: <https://gdpr-info.eu/art-35-gdpr/>
- DSGVO Art. 36: <https://gdpr-info.eu/art-36-gdpr/>
- DSK Muss-Liste: <https://www.bfdi.bund.de/DE/Fachthemen/Inhalte/DSFA/DSFA-Muss-Liste.html>
- Art. 29-WP / EDPB Guidelines on DPIA (WP 248): <https://ec.europa.eu/newsroom/article29/items/611236>
