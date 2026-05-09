# KI-Team-Audit — Prototyp

Interaktiver Prototyp des 3-Minuten KI-Team-Audits von Mindshift AI.

## Sofort ausprobieren (lokal)

Einfach `index.html` im Browser öffnen (Doppelklick). Kein Server, kein Build, keine Installation nötig.

Funktioniert offline nach dem ersten Laden (React-Bibliotheken werden vom CDN geladen).

## Was der Prototyp kann

- **Vollständiger Flow:** Welcome → C1 → 4 × 3 Likert-Items → C2/C3/C4 → Variantenwahl → Ergebnis
- **Live-Scoring:** alle 4 Dimensions-Scores + Gesamtrisiko
- **SHIFT-Verortung** auf Basis der 7 Mapping-Regeln aus `shift_mapping.json`
- **3 kontextsensitive Empfehlungen** mit Volltext, 3 Schritten, Stolperstein
- **Kontext-Overrides** (z. B. "Angst ohne Substanz", Projektions-Flag)
- **Beide Varianten** (Selbstausfüllung / Gesprächsvorbereitung) umschaltbar
- **Leitfragen** phasensensitiv für Mindshift-Gespräch und Team-Gespräch
- **Phasensensitiver CTA** — je nach SHIFT-Phase anderes Mindshift-Format
- **Mindshift-Design-System** — Navy + Gold, Playfair Display + Inter, matching zur Hauptwebsite

## Was fehlt noch (bewusst)

- PDF-Export der Ergebnisse — ist für die produktive Version geplant
- Terminbuchung (aktuell nur Alert) — verbinden mit Cal.com/Calendly
- Ergebnis-Persistierung für Benchmarks (Supabase) — Phase 2
- E-Mail-Capture + Brevo/Mailchimp-Anbindung — Phase 2
- Analytics / Tracking — vor Live-Gang klären

## Deployment-Optionen

### Option A — Standalone Subdomain (empfohlen als Haupthosting)

`audit.mindshift-ai-consultants.de`

Der `index.html` kann direkt auf jedem Static-Host deployed werden:

- **Vercel:** Ordner `audit_app/` als separates Projekt deployen, Custom Domain einrichten. Kostenlos im Hobby-Plan.
- **Netlify:** Drag-and-Drop des Ordners auf app.netlify.com, Domain verknüpfen.
- **Eigener Server:** `index.html` in ein öffentliches Verzeichnis legen.

### Option B — Integration in die Hauptseite (zusätzlich)

Zwei Wege:

**B1 · Eigene Seite unter `/audit`**
Den Inhalt des `<body>`-Bereichs direkt in die Next.js-Seite übernehmen. Style-Variablen sind bereits kompatibel.

**B2 · Als iframe einbetten**
```html
<iframe src="https://audit.mindshift-ai-consultants.de"
        style="width:100%; min-height:900px; border:0;"
        loading="lazy"
        title="KI-Team-Audit"></iframe>
```
Einfachste Variante. Zwei Deployments, aber nur ein Quelltext zu pflegen.

### Empfehlung

**A + B2** — eigene Subdomain als Single Source of Truth, Einbettung via iframe auf der Hauptseite unter `/audit`. Vorteile:
- Eine Codebase
- Separate Analytics und Conversion-Tracking möglich
- Audit auch direkt teilbar: `mindshift-ai-consultants.de/audit` ODER `audit.mindshift-ai-consultants.de`
- A/B-Tests einfach

## Übergang zu Next.js (Phase 2)

Der Prototyp ist bewusst als Single-File-HTML gebaut, um schnell iterierbar zu sein. Für die produktive Version:

1. Next.js 14 + App Router + TypeScript + Tailwind
2. React-Komponenten aus dem Prototyp in TSX-Module aufteilen
3. Scoring- und Mapping-Logik in `lib/audit-engine.ts` auslagern (unit-testbar)
4. `shift_mapping.json` und Intervention-Katalog als separate JSON-Dateien importieren
5. shadcn/ui für Form-Components
6. Recharts für eine echte Radar-Darstellung (der aktuelle Balken-View funktioniert aber gut)
7. `@react-pdf/renderer` für PDF-Export
8. Supabase für Persistenz + anonyme Benchmarks

## Struktur der Logik (für spätere Refaktorierung)

```
index.html
├── CONTEXT_Q           Die 4 Kontextfragen (C1–C4)
├── DIMENSIONS          Die 4 Dimensionen mit je 3 Likert-Items
├── SHIFT_PHASES        Die 6 SHIFT-Positionen mit Metadaten
├── INTERVENTIONS       16 Empfehlungen (Volltext, Schritte, Stolpersteine)
├── PHASE_INTERVENTIONS Map: Phase → Interventions-IDs (primary/secondary)
├── computeScores()     Likert → Dimensions-Scores + Gesamt
├── mapShift()          7 Regeln, erste passende gewinnt
├── buildRecommendations()  Phase + Kontext → 3 priorisierte Interventionen + Overrides
└── App (React)         Step-basiertes Flow-UI
```

## Bekannte Anpassungen der Inhalte

Nach Qualitätscheck in NotebookLM wurden folgende Empfehlungen nachgeschärft:
- **SV-3:** "Entry Interviews" als Stärken-Dialog für bestehende Teams reformuliert
- **KA-3:** Fokus von "Tool-Auswahl" auf "Use-Case-Auswahl" verschoben (Mittelstands-Realität: IT/GF entscheidet über Tools)
- **FV-2:** Quellenbezug präzisiert auf Welpe DLS Dimension "Transparent Agenda"

14 von 16 Empfehlungen ohne Beanstandung, 0 Risikofälle.

## v0.2 — Zweistufige Struktur (2026-04-23)

Erweitert um Deep-Check-Modus:

- **Welcome-Screen** mit Mode-Wahl zwischen Quick-Check (3 Min, bisheriges Verhalten) und Deep-Check (12-15 Min)
- **8 neue Dimensionen** für Deep-Check:
  - A5 Entscheidungsangst (Hampel — CYA, Perfektions-Paralyse)
  - B1 Eigenverantwortung (Bischoff)
  - B2 Chancenbewusstsein (Bischoff)
  - C1 Strategische Klarheit (McKinsey/BCG/Gartner/MIT)
  - C2 ROI & Wertnachweis (SHIFT-Framework)
  - C3 Daten & Compliance (EU AI Act, DSGVO)
  - C4 Governance & Ownership (inkl. Mitbestimmung/Betriebsrat)
  - C5 Lernarchitektur (Safe-to-Fail, Just Culture, Ambidextrie)
- **Block-Scores** — 3 Profile (A Risiken, B Ressourcen, C Struktur) parallel zu den Einzel-Dimensionen
- **E-Mail-Gate** (optional, kein Zwang) vor dem Ergebnis bei Deep-Check
- **Alle 12 Dimensionen im Diagnoseprofil** statt nur 4
- **Deutscher Kontext** besonders berücksichtigt (CYA-Logik, Zögerungsstarre, Sozialpartnerschaft)

### Noch offen (v0.3)
- Interventions-Katalog auf die 8 neuen Dimensionen erweitern (aktuell zeigt Deep-Check die 16 bestehenden Interventionen — funktioniert, aber passt noch nicht immer 1:1 zum neuen Barrier Point bei Block C)
- SHIFT-Mapping auf Block-Scores ausdehnen (aktuell basiert Mapping auf den 4 Quick-Check-Dimensionen)
- Impact/Effort-Matrix als optionales Output-Element
- Echte PDF-Generierung

## v0.3 — Kompetenz- und Ergebniswirkung (2026-04-23)

Dieser Release schärft die Wahrnehmung des Audits — wie kompetent es wirkt und wie stark die Handlungsenergie beim Nutzer ausgelöst wird.

### Kompetenzwirkung

- **Quellenangaben aufgewertet:** Statt "Quelle: Grant" jetzt "Adam Grant (Wharton) · Task Significance-Forschung"
- **Methodik-Box am Ergebnis-Ende:** Prominentes Navy-Element mit Zahlen (Dimensionen, Items, Empfehlungen, Quellen) + aufklappbares Detail-Quellenverzeichnis. Wechselt je nach Mode.
- **Peer-Benchmark-Element:** Zeigt dem Nutzer, wie er sich im Vergleich einordnet (aktuell mit indikativen Richtwerten; wird mit echten Daten ersetzt)

### Ergebniswirkung

- **Priorität 1 visuell aufgewertet:** Größeres Card, Gold-Akzent, "Priorität 1 · Ihr stärkster Hebel"-Badge, verdoppelte Typografie
- **7/30/90-Tage-Zeitplan INTEGRIERT** in Priorität-1-Empfehlung (nicht mehr separat)
- **Navy "Warum gerade diese"-Box** bei Priorität 1 — erklärt die Phasen/Kontext-Logik dahinter
- **CTA-Brücke vor dem CTA:** Zeigt in 3 Schritten, wie ein 30-Min-Gespräch mit Mindshift abläuft — beseitigt "was passiert wenn ich klicke"-Unsicherheit
- **Footer-Quellenliste entfernt** — stattdessen in Methodik-Box integriert

### Deep-Check echte Empfehlungen (Welle 2.6)

- **24 neue Interventionen** für die 8 neuen Dimensionen:
  - A5 Entscheidungsangst: EA-1 Just Culture · EA-2 Pre-Mortem · EA-3 Cost of Delay sichtbar
  - B1 Eigenverantwortung: EV-1 Use-Case-Paten · EV-2 Delegations-Matrix · EV-3 Lösungsrituale
  - B2 Chancenbewusstsein: CB-1 Augmentation-Storys · CB-2 Explorationstage · CB-3 Peer-Inspiration
  - C1 Strategische Klarheit: SK-1 Nordstern-Workshop · SK-2 Max 3 Use Cases · SK-3 Fachbereichs-Sponsor
  - C2 ROI: RO-1 Baseline messen · RO-2 ROI-Dashboard · RO-3 Abbruchkriterien
  - C3 Daten/Compliance: DC-1 KI-Register · DC-2 AI Literacy · DC-3 Shadow-AI-Amnestie
  - C4 Governance: GV-1 Betriebsrat · GV-2 KI-Beauftragter · GV-3 Integration in Compliance
  - C5 Lernarchitektur: LA-1 Fehlerbudgets · LA-2 AAR · LA-3 70/20/10 Ambidextrie
- **Block-sensitive Empfehlungs-Auswahl** im Deep-Check: Top-Risiko je Block A/B/C, bis zu 5 Empfehlungen statt 3

## Version

v0.3 — 2026-04-23
