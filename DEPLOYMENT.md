# Deployment: audit.mindshift-ai-consultants.de

Komplette Subdomain-Anwendung nach dem Einladungs-Prinzip: **keine Preise, keine Pakete, nur Methodik-Tiefe und persönliche Einladung**.

## Struktur der Auslieferung

```
audit_app/
├── landing.html       ← Einstiegsseite (Hero, Methodik, Dirk, Einladung, FAQ)
├── index.html         ← Der Quick-Check (Audit-Tool selbst)
└── DEPLOYMENT.md      ← dieses Dokument
```

## Dateizuordnung auf der Subdomain

Die Landingpage ist **`landing.html`**, der Audit ist **`index.html`**. Für die Subdomain-Deployment:

| URL | Datei |
|-----|-------|
| `audit.mindshift-ai-consultants.de/` | `landing.html` (umbenennen zu `index.html` oder Rewrite-Regel) |
| `audit.mindshift-ai-consultants.de/check` | der Audit selbst (bisheriger `index.html`) |

**Empfohlener Umgang beim Deploy:**

1. `landing.html` → `index.html` (im Zielsystem)
2. `index.html` (Audit) → `check.html` (im Zielsystem)
3. Links in beiden Dateien anpassen:
   - In `landing.html`: alle `href="index.html"` → `href="check.html"` oder `href="/check"`
   - In `check.html`: fürs "Zurück zur Übersicht" ggf. Link auf `/`

## Deployment-Optionen

### Vercel (empfohlen)

Schnellste Variante. Kostenlos im Hobby-Plan.

1. GitHub-Repo für `audit_app/` anlegen
2. Vercel-Account mit GitHub verbinden
3. Neues Project → Repo auswählen → Framework: "Other"
4. Build-Setting: *keine Build-Command* (Static Deploy)
5. Output-Directory: `/` (root)
6. Domain-Konfiguration:
   - Vercel → Project → Settings → Domains
   - Custom Domain hinzufügen: `audit.mindshift-ai-consultants.de`
   - Bei Strato/IONOS (oder wo auch immer die Hauptdomain liegt): CNAME-Record setzen auf `cname.vercel-dns.com`
7. HTTPS automatisch durch Vercel

### Netlify

Fast identisch. Drag-and-Drop des Ordners auf app.netlify.com, Domain verknüpfen.

### Eigener Webserver

`landing.html` und `check.html` in ein öffentliches Webverzeichnis legen. Reverse-Proxy-Regel für die Subdomain:

```nginx
server {
  listen 443 ssl;
  server_name audit.mindshift-ai-consultants.de;

  root /var/www/audit;
  index index.html;

  location / {
    try_files $uri $uri/ =404;
  }
}
```

### Integration in die Hauptseite (optional, zusätzlich)

Auf `mindshift-ai-consultants.de/audit`:

```html
<iframe src="https://audit.mindshift-ai-consultants.de"
        style="width:100%; min-height:3200px; border:0;"
        loading="lazy"
        title="KI-Team-Audit"></iframe>
```

Für Responsive sollte `min-height` dynamisch gesetzt werden (z. B. via `postMessage` aus dem iFrame), aber für V1 reicht ein großzügiger Fixwert.

## Offene technische Punkte für Go-Live

### 1. Terminbuchung verknüpfen

Aktuell zeigt der "Gespräch vereinbaren"-Button ein `alert()`. Vor Go-Live:

- Cal.com-Account für Dirk Brusch einrichten
- Event-Type: "30 Min · Kennenlern-Gespräch · kostenfrei"
- In `landing.html` und `index.html` (Audit-CTA) den Link aktualisieren:
  ```html
  <a href="https://cal.com/dirk-brusch/kennenlerngespraech" class="btn-gold">...</a>
  ```

### 2. Dirk-Portrait einfügen

Aktuell ist das Portrait ein Navy-Block mit Initialen "DB". Für Go-Live ersetzen durch:

```html
<div class="dirk-portrait reveal">
  <img src="dirk.jpg" alt="Dirk Brusch" style="width:100%; height:100%; object-fit:cover; border-radius: var(--radius-lg);">
  <div class="badge">Dirk Brusch</div>
</div>
```

Foto-Anforderungen: Querformat okay (wird auf 4/5 zugeschnitten), hochauflösend (min 800×1000), ehrliche Tonalität.

### 3. Impressum / Datenschutz

Footer-Links für Impressum und Datenschutz zeigen aktuell auf `#`. Vor Go-Live: verknüpfen mit den rechtssicheren Seiten der Hauptdomain oder eigene Seiten anlegen.

Datenschutz-Hinweis konkret: Der Quick-Check läuft client-side — es werden keine Daten übertragen. Das sollte im Datenschutz-Text explizit stehen, um das "anonym"-Versprechen zu untermauern.

### 4. Scarcity-Zahl dynamisieren

"Aktuell 4 freie Slots" ist statisch. Optionen:

- **Einfach:** Manuelle Pflege — Zahl alle 2 Wochen aktualisieren
- **Mittel:** Cal.com-API abfragen für verfügbare Slots
- **Ehrlich bleiben:** Zahl nur ändern, wenn sie sich wirklich ändert — Scarcity-Signale, die offensichtlich statisch sind, schaden Vertrauen

### 5. Analytics

Aktuell keine Messung. Vor Go-Live integrieren:

- **Plausible.io** oder **Fathom** (DSGVO-konform, keine Cookies) — einfacher JS-Snippet
- Mindest-Events: Landingpage-Views, Quick-Check-Starts, Quick-Check-Completions, Variant-Choice, CTA-Clicks
- Conversion-Kette: Landing → Start → Complete → Gespräch-Button-Click

### 6. Open-Graph-Bild

Für schöne Link-Vorschau (LinkedIn, Twitter etc.):

```html
<meta property="og:title" content="KI-Reife-Analyse · Mindshift AI" />
<meta property="og:description" content="Wo steht Ihr Team wirklich im KI-Wandel? 3-Minuten-Audit für Führungskräfte im Mittelstand." />
<meta property="og:image" content="https://audit.mindshift-ai-consultants.de/og-image.png" />
<meta property="og:url" content="https://audit.mindshift-ai-consultants.de/" />
<meta name="twitter:card" content="summary_large_image" />
```

OG-Image (1200×630): Navy-Hintergrund, Gold-Akzent, großer Schriftzug "KI-Reife-Analyse · Mindshift AI".

### 7. Mobile-Testen

Die Seite ist responsive gebaut, aber vor Go-Live auf echten Geräten prüfen:

- iPhone Safari (die häufigste LinkedIn-Browser-Umgebung bei B2B-Lesern)
- iPad Portrait (Board-Meeting-Read)
- Android Chrome

## Was diese Version konsequent vermeidet

| Vermieden | Stattdessen |
|-----------|------------|
| Preisschilder auf der Subdomain | "Kein Preisschild" explizit als Statement |
| Paketstrukturen | Einladung zum Gespräch |
| Verkaufs-CTA ("Jetzt kaufen") | "Gespräch vereinbaren" |
| Marketing-Buzzwords | Methodik-Tiefe, belegt |
| Testimonial-Sammlung | Methodische Glaubwürdigkeit durch Quellen |
| Feature-Liste | Was der Nutzer in 3 Minuten bekommt |

## Nach Go-Live — was getrackt werden sollte

Damit die 1000-Simulations-Verteilung gegen Realität validiert wird:

1. **Tatsächliche Phasen-Verteilung** der echten Nutzer (Erwartung laut Simulation: 61 % SENSE)
2. **Conversion-Funnel:** Landing → Start → Complete → Gespräch-Klick → Termin gebucht
3. **Exit-Punkte:** Wo brechen Nutzer ab? Besonders kritisch: vor dem Abschluss des Quick-Checks
4. **Gesprächs-Output:** Wie viele der gebuchten Gespräche führen zu einem Mandat?

Erste Kalibrierungsrunde nach ~50–100 echten Durchläufen. Erwartung: Stärkste Hebel zeigen sich zuerst am CTA-Click-Rate auf den unterschiedlichen SHIFT-Phasen.

## Version

**v1.0 Deployment-ready** — 2026-04-24

Alle Engine-Tests bestanden (10/10 Quick, 50/50 Deep, 41/41 Interventions reachable in 1000-Case-Simulation). Landingpage in Mindshift-Design-System, Einladungs-Framing ohne Preise, Methodik-Transparenz prominent.
