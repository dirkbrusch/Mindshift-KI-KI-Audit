# Versions-Index

Chronologische Übersicht aller App-Versionen. Vor jeder Überarbeitung wird der aktuelle Stand nach `versions/YYYY-MM-DD_vX.Y_kurzname/` kopiert.

## Konvention
- **Major (vX.0)** — Positionierungs- oder Logikwechsel
- **Minor (vX.Y)** — Feinschliff, Wording, CTA, Layout
- Jede Version enthält eigene `index.html`, `landing.html`, `CHANGELOG.md`

## Versionen

| Datum | Version | Kurzname | Kern-Änderung |
|---|---|---|---|
| 2026-04-25 | v1.0 | baseline | Ausgangsstand vor Positionierungs-Schärfung |
| 2026-04-25 | v1.1 | positioning-sharpening | Führungs-Framing, schärferer CTA „Reality Check", Positionierungs-Anker |

## Rollback
Um zu einer früheren Version zurückzukehren:
```bash
cp versions/<version-ordner>/index.html ./index.html
cp versions/<version-ordner>/landing.html ./landing.html
```
