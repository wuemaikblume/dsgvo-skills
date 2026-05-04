# Datensouveränität, Art. 48 DSGVO, US CLOUD Act und BCRs

> Vertiefungsdatei zu `SKILL.md`. Wird gelesen, wenn US-Konzerne in EU hosten, Behördenanordnungen aus Drittländern relevant werden, oder BCRs als Alternative zu SCCs erwogen werden.

## Datensouveränität ≠ Datenresidenz

**Datenresidenz (Data Residency):** Wo liegen die Daten physisch? — z.B. AWS `eu-central-1` Frankfurt.

**Datensouveränität (Data Sovereignty):** Welcher Rechtsordnung unterliegen die Daten? — Hier zählt der **Konzernsitz des Anbieters**, nicht das Server-Rack.

Ein US-Konzern, der in Frankfurt hostet, unterliegt **weiterhin US-Recht**. US-Behörden können die US-Mutter zur Herausgabe zwingen, auch wenn die Bytes in der EU stehen.

## US CLOUD Act (Clarifying Lawful Overseas Use of Data Act, 2018)

- Erlaubt US-Strafverfolgungsbehörden, Daten von **US-Communication Service Providern** anzufordern, **unabhängig vom Speicherort weltweit**.
- Betrifft alle US-Konzerne und US-Tochtergesellschaften, die als CSP gelten (Cloud, Hosting, SaaS, E-Mail, Kommunikation).
- Keine Notwendigkeit zur Information des Datensubjekts; Gag Orders möglich.

**Konkrete Folgen:**
- AWS Frankfurt → US-Behörden können Amazon.com Inc. zur Herausgabe zwingen
- Microsoft Westeurope → analog
- Google Cloud `europe-west3` → analog
- US-SaaS mit EU-Hosting (Cloudflare, Supabase, Clerk, Sentry US-Backend) → analog

## Art. 48 DSGVO — Sperre für Drittland-Anordnungen

> „Jegliches Urteil eines Gerichts oder eines Verwaltungsgerichts und jegliche Entscheidung einer Verwaltungsbehörde eines Drittlands, mit denen von einem Verantwortlichen oder einem Auftragsverarbeiter die Übermittlung oder Offenlegung personenbezogener Daten verlangt wird, kann nur dann anerkannt oder vollstreckbar werden, wenn sie auf eine in Kraft befindliche internationale Übereinkunft [...] gestützt sind."

**Kurz:** Eine US-Anordnung allein ist **keine** Rechtsgrundlage für eine DSGVO-konforme Datenübermittlung. Es braucht zusätzlich:
- Eine Rechtsgrundlage nach Art. 6
- Einen Transfermechanismus nach Art. 44-49
- Praktisch: Rechtshilfeabkommen (MLAT) oder ähnliches internationales Instrument

## EDPB Guidelines 02/2024 (zu Art. 48)

Konkretisieren, wie EU-Anbieter mit ausländischen Behördenanordnungen umgehen sollen:

1. **Anordnung prüfen** — Rechtsgrundlage, Verhältnismäßigkeit
2. **Internationale Vereinbarung suchen** (MLAT, EU-USA-CLOUD-Agreement falls einschlägig)
3. Wenn keine Vereinbarung: **Anordnung kann nicht ohne weiteres befolgt werden** ohne DSGVO-Verstoß
4. **Datenminimierung** und **Nutzerinformation** prüfen (sofern keine Gag Order)
5. **Aufsichtsbehörde konsultieren** bei Unklarheit

**Praxisreflex:** Im DPA mit US-Anbietern Klausel zur Anwendung von Art. 48 DSGVO + Anfechtungspflicht bei US-Anordnungen einfordern.

## Remote Access als Drittlandtransfer

EDPB hat klargestellt: **Reiner Lese-Zugriff aus einem Drittland auf in der EU gespeicherte Daten** ist bereits ein Datentransfer nach Art. 44 DSGVO.

**Konkrete Beispiele:**
- 24/7-Support-Team in Indien greift auf EU-Tickets zu
- US-Admin-Zugriff für Debugging auf EU-Logs
- Outsourcing der DBA-Tätigkeit an US-Dienstleister
- Konzern-IT in den USA mit Zugriff auf EU-Tochter

**Pflichten:**
- Im VVT als separaten Transfer dokumentieren
- Transfermechanismus prüfen (DPF, SCCs)
- Bei sensiblen Daten: Zugriff auf EU-Personal beschränken (z.B. Microsoft EU Data Boundary mit Customer Lockbox)

## Binding Corporate Rules (BCRs) — Art. 46 Abs. 2 lit. b/c

**Was sind BCRs?** Konzernweite Datenschutzregeln, die von einer EU-Aufsichtsbehörde **genehmigt** werden und intrakonzernlichen Drittlandtransfer legitimieren.

**Wann sinnvoll?**
- Multinationaler Konzern mit dauerhaften, vielfältigen Drittlandtransfers
- SCCs sind zu starr / zu zahlreich
- Hohe Compliance-Reife im Konzern

**Beispiele für genehmigte BCRs:**
- IBM, Salesforce, Cisco, SAP, Bosch, Siemens, Deutsche Bank etc.

**Warum für KMU selten relevant?**
- Genehmigungsverfahren dauert 1-3 Jahre
- Erfordert eigene EU-Tochter und konzernweite Compliance-Struktur
- Hohe Ressourcen-Investition

**Aber:** Wenn ein US-SaaS-Anbieter BCRs hat (z.B. Salesforce), nutzt er diese als Transfermechanismus — der Verantwortliche muss das wissen und im VVT korrekt eintragen.

## Was tun, wenn echter Cloud-Act-Schutz nötig ist?

**EU-Souveräne-Cloud-Optionen:**

| Anbieter | Status |
|----------|--------|
| OVHcloud (FR) | EU-Konzern, kein Cloud Act |
| Hetzner (DE) | EU-Konzern, kein Cloud Act |
| IONOS (DE) | EU-Konzern, kein Cloud Act |
| Scaleway (FR) | EU-Konzern, kein Cloud Act |
| Stackit (DE) | EU-Konzern, kein Cloud Act |
| **T-Systems Sovereign Cloud** | EU-Konzern, kein Cloud Act |
| Microsoft Azure mit „Sovereign Cloud" / Customer Lockbox | nur partielle Mitigation, US-Mutter bleibt |
| AWS European Sovereign Cloud (in Aufbau) | nur partielle Mitigation |
| Gaia-X-Stack (in Aufbau) | EU-souverän geplant |

**Architektur-Empfehlung bei Cloud-Act-Sensitivität:**
- Erste Wahl: EU-Anbieter ohne US-Konzernzugehörigkeit
- Zweite Wahl: clientseitige Verschlüsselung mit BYOK in EU-KMS, sodass US-Anbieter nur Chiffrate sehen
- Dritte Wahl: Pseudonymisierung vor US-Versand

## Häufige Fehler

| Annahme | Realität |
|---------|----------|
| „eu-central-1 = vollständig EU-souverän" | Cloud Act bleibt; US-Mutter kann zur Herausgabe gezwungen werden |
| „Customer Lockbox löst das Cloud-Act-Problem" | Lockbox erschwert MS-Mitarbeiter-Zugriff, nicht aber rechtliche Anordnungen gegen MS Corp |
| „BCRs sind nichts für uns" | Korrekt für KMU-Nutzung. Aber: Hauptanbieter-BCRs als Transfergrundlage akzeptieren ist möglich |
| „Support-Zugriff aus Indien ist OK weil EU-Daten" | Ist Drittlandtransfer nach Art. 44 — Mechanismus + Doku Pflicht |
| „Wenn FBI fragt, müssen wir eh herausgeben" | Art. 48 + EDPB 02/2024: nicht ohne MLAT/internationale Vereinbarung; sonst DSGVO-Verstoß |

## Quellen

- DSGVO Art. 48: <https://gdpr-info.eu/art-48-gdpr/>
- US CLOUD Act (offiziell): <https://www.justice.gov/criminal/criminal-oia/cloud-act-resources>
- EDPB Guidelines 02/2024 on Art. 48 (data transfers based on international agreements): <https://www.edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-022024-article-48-gdpr_en>
- BCRs Approved List (EU-Kommission): <https://ec.europa.eu/newsroom/just/items/612053/>
- Gaia-X: <https://gaia-x.eu/>
