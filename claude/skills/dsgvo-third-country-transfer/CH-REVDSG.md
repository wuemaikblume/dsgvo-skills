# Schweiz — revDSG, Swiss-US DPF, EDÖB

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, wenn Kunden/Nutzer in der Schweiz betroffen sind oder ein Schweizer Verantwortlicher ein US-Tool einsetzt.

## revDSG — das neue Schweizer Datenschutzgesetz

- In Kraft seit **1. September 2023**
- Vollständige Bezeichnung: revidiertes Bundesgesetz über den Datenschutz
- **Aufsichtsbehörde:** Eidgenössischer Datenschutz- und Öffentlichkeitsbeauftragter (EDÖB)
- Bußgelder: bis **CHF 250.000 für natürliche Personen** (nicht Unternehmen direkt — Unternehmensbußen nur, wenn Identifikation der verantwortlichen Person zu aufwendig)

## EU- vs. Schweizer Adäquanz — beidseitig

- **EU → Schweiz:** Adäquanzbeschluss seit 2000 (EU-Kommission), bestätigt 2024 unter DSGVO
- **Schweiz → EU:** Schweiz erkennt EU/EEA als angemessen an
- → Datentransfer Schweiz ↔ EU **ohne zusätzliche Garantien** möglich

**Praxisreflex:** Ein Schweizer KMU, das Daten in Frankfurt hostet, hat keinen Drittlandtransfer-Pflichtenkreis im EU-CH-Verhältnis.

## Schweiz → USA: Swiss-US Data Privacy Framework

- **Eigene** Schweizer Adäquanz für die USA, parallel zum EU-US DPF
- **Inkrafttreten:** 15. September 2024
- **Verfahren:** US-Anbieter müssen sich für **Swiss-US DPF** zusätzlich zertifizieren — die EU-US-Zertifizierung allein reicht für Schweizer Daten **nicht**
- **Liste:** auf [dataprivacyframework.gov/list](https://www.dataprivacyframework.gov/list) — Filter „Swiss-US"

**Wichtige Konsequenz:** Wenn ein Schweizer Verantwortlicher einen US-Anbieter einsetzt, muss er prüfen, ob dieser **auch unter Swiss-US DPF** zertifiziert ist. Viele US-Anbieter haben beide Zertifizierungen, aber nicht alle.

## SCCs für Schweiz

- Die EU-Standardvertragsklauseln (2021/914) sind vom **EDÖB für die Schweiz anerkannt** — mit Anpassungen (z.B. Verweis auf revDSG statt nur DSGVO; EDÖB als Aufsichtsbehörde)
- EDÖB hat eine **Anpassungs-Anleitung** veröffentlicht („FAQ zu den neuen EU-SCCs")
- Praxis: SCCs einsetzen + Schweizer Anpassungen vereinbaren

## Decision Tree für Schweizer Daten

```
1. Verantwortlicher und Nutzer in der Schweiz?
   ├─ Anbieter in Schweiz/EU/EEA → kein Drittlandtransfer
   ├─ Anbieter in den USA:
   │     a) Auf Swiss-US DPF gelistet (Active)?
   │        ├─ Ja → erlaubt
   │        └─ Nein → SCCs (mit CH-Anpassung) + TIA
   └─ Anbieter in anderem Drittland:
        a) Schweizer Adäquanzliste prüfen
           (überschneidet sich, aber nicht identisch mit EU-Liste)
        └─ ansonsten SCCs (CH-Anpassung) + TIA
```

## Unterschiede zur DSGVO (kurzform)

| Thema | DSGVO | revDSG |
|-------|-------|--------|
| Anwendungsbereich | EU/EEA | Schweiz |
| Bußgeld-Adressat | Unternehmen direkt | primär natürliche Personen |
| Bußgeldhöhe | bis 4 % Konzernumsatz | bis CHF 250.000 (Personen) |
| Profiling-Definition | weit | weit, aber „Profiling mit hohem Risiko" gesondert geregelt |
| DSFA-Pflicht | Art. 35 | Art. 22 revDSG (ähnlich) |
| Bestellung DSB | bei bestimmten Schwellen | freiwillig, aber Beratungspflicht |
| Verzeichnis Verarbeitungstätigkeiten | Art. 30 | Art. 12 revDSG |
| Meldepflicht Datenpanne | 72h | „so rasch als möglich" |
| Rechte der Betroffenen | Art. 15-22 | sehr ähnlich |

## Praxistipps

1. **Schweizer Verantwortliche** sollten US-Anbieter **immer** auf beide Zertifizierungen prüfen (EU-US + Swiss-US DPF)
2. **EU-Verantwortliche mit CH-Nutzern**: revDSG ist anwendbar, aber Adäquanz EU→CH erleichtert den Datenfluss
3. **Datenschutzerklärung** sollte revDSG explizit erwähnen, wenn Schweizer Nutzer adressiert werden
4. **EDÖB-Konsultation** kostet nichts — bei Unsicherheit nutzen

## Quellen

- revDSG (Volltext): <https://www.fedlex.admin.ch/eli/cc/2022/491/de>
- EDÖB Internationale Datenübermittlung: <https://www.edoeb.admin.ch/edoeb/de/home/datenschutz/handel-und-wirtschaft/uebermittlung-ins-ausland.html>
- EDÖB FAQ zu neuen EU-SCCs: <https://www.edoeb.admin.ch/edoeb/de/home/datenschutz/handel-und-wirtschaft/uebermittlung-ins-ausland/scc.html>
- Swiss-US DPF: <https://www.dataprivacyframework.gov/program-articles/Switzerland>
- Gegenseitiger Adäquanz EU↔CH: <https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en>
