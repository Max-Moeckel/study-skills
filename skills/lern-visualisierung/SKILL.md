---
name: lern-visualisierung
description: Lernbilder zu Aufgaben und Begriffen eines beliebigen Fachs: statische Skizzen (TikZ, matplotlib, Graphviz) als PNG/PDF und interaktive Lernseiten mit Reglern. Ergänzt klausur-tutor.
---

# Lern-Visualisierung

Liefert das passende Bild, wenn ein Begriff oder eine Aufgabe erklärt wird. Fachneutral; Kursangaben (Sprache, Notation, Kursordner) aus `kurs.md`, falls der Skill `klausur-tutor` eingerichtet ist. Lernstand-Pflege macht `klausur-tutor`.

## 1. Lohnt sich ein Bild?

Ja, wenn es Struktur zeigt, die Text schlecht trägt: Lage im Raum, Graphen, Abläufe, Abhängigkeiten, Hierarchien, Zeitachsen, Vergleiche. Nein für reine Definitionen oder Aufzählungen.

## 2. Werkzeug wählen

| Inhalt | Werkzeug | Ausgabe |
|---|---|---|
| Formeln, Funktionsgraphen, Geometrie, mathematische Skizzen | TikZ / pgfplots | PDF + PNG |
| Daten, Verteilungen, numerische Funktionen | matplotlib | PNG (+ SVG) |
| Abläufe, Prüfungsschemata, Bäume, Automaten, Begriffsnetze | Graphviz (`dot`) | PNG + SVG |
| „Was passiert, wenn …“: Parameter ziehen, Regler | interaktive HTML-Seite (Artifact) | Link |
| Schnelle Erklärskizze, die nicht aufbewahrt wird | Inline-Visualisierung im Chat, falls die Umgebung eine hat | inline |

Unklar → statisches Bild für die Aufgabe machen und die interaktive Version in einem Satz anbieten.

Vorher prüfen, was da ist (`which pdflatex dot`, `python3 -c "import matplotlib"`), fehlendes nachinstallieren (`pip install --break-system-packages …`, `apt` nur wenn erlaubt). Geht beides nicht: auf matplotlib ausweichen.

## 3. Didaktik

- **Nicht spoilern:** Bei Aufgaben, die der Nutzer selbst lösen soll, nur die Aufgabenskizze (Gegebenes, Achsen, Punkte). Die Lösungsskizze erst nach seiner Antwort oder auf Wunsch.
- **Ein Bild, eine Kernaussage.** Lieber zwei Teilbilder als ein überladenes.
- Notation, Sprache und Konventionen des Kursmaterials übernehmen. Exakte Werte statt Dezimalzahlen, wo möglich (π/3, √2/2).
- Typische Fallen sichtbar machen (offener vs. geschlossener Randpunkt, Ausnahmefall im Schema, Vorzeichen).
- Zu jedem Bild im Chat: 2–3 Sätze **„Was du im Bild sehen sollst“** und 1–2 kurze Aufgaben, die das Bild benutzen („Lies … ab“, „Zieh … so, dass …“).

## 4. Gestaltung (für alle Werkzeuge gleich)

| Rolle | Farbe |
|---|---|
| `acc` Hauptobjekt | #1F5FBF |
| `wcol` zweites Objekt / Vergleich | #C2410C |
| `res` Ergebnis / Grenzwert / Lösung | #0F7A5A |
| `muted` Hilfsbeschriftung | #6B7280 |
| `gridc` Gitter, Hilfslinien | #D9DDE5 |

Bedeutung nie nur über Farbe: zusätzlich Strichart (durchgezogen/gestrichelt) oder Marker. Weißer Hintergrund bei statischen Bildern.

## 5. Statische Bilder

1. Quelle in `viz/<thema>_<kurz>.<tex|py|dot>` im Arbeitsverzeichnis schreiben.
2. Bauen:
   ```bash
   # TikZ
   pdflatex -interaction=nonstopmode -halt-on-error X.tex > /dev/null; grep "^!" -A3 X.log; pdftoppm -png -r 160 -singlefile X.pdf X
   # matplotlib
   python3 X.py            # speichert X.png (dpi=160) und X.svg
   # Graphviz
   dot -Tpng -Gdpi=160 X.dot -o X.png && dot -Tsvg X.dot -o X.svg
   ```
3. **PNG immer selbst ansehen** und auf überlappende Beschriftungen, abgeschnittene Teile und Lesbarkeit prüfen. Höchstens zwei Korrekturrunden.
4. Ausliefern: PNG zum Ansehen, PDF/SVG zum Drucken, Quelldatei zum Wiederverwenden an den Nutzer schicken. Ist der Kursordner verbunden, zusätzlich nach `<Kursordner>/Visualisierungen/` schreiben.

### Beschriftungen (sonst überlappt es)
- Labels an Achsen und Linien seitlich versetzen, nicht auf die Linie legen.
- Achsentitel am Pfeilende (`below left` / `below right`).
- Formelkästen außerhalb der Figur, Verbindungslinie kreuzt keine anderen Labels.
- Keine weiß hinterlegten Labels über andere Beschriftungen legen; lieber verschieben.
- Bei vielen Marken kleinere Schrift (`\scriptsize`, `fontsize=8`).

### TikZ-Präambel
```latex
\documentclass[border=8pt]{standalone}
\usepackage{tikz,pgfplots,amsmath,amssymb}
\pgfplotsset{compat=1.18}
\usetikzlibrary{arrows.meta,angles,quotes,calc,positioning}
\usepgfplotslibrary{fillbetween}
\definecolor{acc}{HTML}{1F5FBF}\definecolor{wcol}{HTML}{C2410C}
\definecolor{res}{HTML}{0F7A5A}\definecolor{muted}{HTML}{6B7280}\definecolor{gridc}{HTML}{D9DDE5}
\tikzset{achse/.style={-{Stealth[length=2.2mm]},thin},
         pfeil/.style={-{Stealth[length=2.6mm]},very thick,acc}}
```
pgfplots: `axis lines=middle`; offene Randpunkte `mark=o`, geschlossene `mark=*`; Bänder (z. B. ε-Schlauch) mit `fill between`, `opacity=.12`; ausreißende Kurven mit `restrict y to domain` kappen.

### matplotlib-Grundgerüst
```python
import matplotlib; matplotlib.use("Agg")
import matplotlib.pyplot as plt
C = dict(acc="#1F5FBF", wcol="#C2410C", res="#0F7A5A", muted="#6B7280", gridc="#D9DDE5")
fig, ax = plt.subplots(figsize=(7, 4.2))
ax.grid(color=C["gridc"], lw=.6); ax.spines[["top", "right"]].set_visible(False)
# … plotten mit color=C["acc"] usw.
fig.tight_layout(); fig.savefig("X.png", dpi=160); fig.savefig("X.svg")
```

### Graphviz-Grundgerüst
```dot
digraph G {
  rankdir=TB; bgcolor="white";
  node [shape=box, style="rounded,filled", fillcolor="white", color="#1F5FBF", fontname="Helvetica", fontsize=12];
  edge [color="#6B7280", fontname="Helvetica", fontsize=10];
  // Ergebnis-/Endknoten: color="#0F7A5A"; Ausnahme-/Fehlerpfad: color="#C2410C", style=dashed
}
```

## 6. Interaktive Lernseiten

1. Gibt es einen Skill für Artifact-Gestaltung (z. B. `artifact-design`), **ihn vor dem Schreiben laden** und befolgen. Ohne Artifact-Werkzeug: eine eigenständige `.html`-Datei schicken.
2. **Eine Seite pro Themenblock** („Folgen-Labor“, „Schema-Trainer“). Erst die vorhandenen Artifacts des Nutzers ansehen und eine passende Seite um einen Modus erweitern, statt eine neue anzulegen.
3. Aufbau: Zeichenfläche (bei Geometrie quadratisch), darunter Regler/Optionen, daneben bzw. darunter ein **Protokoll**, das live den Rechen- oder Prüfungsweg mit exakten Werten mitschreibt. Modi als Umschalter oben. Muss auch auf dem Handy (≈ 400 px) ohne horizontales Scrollen funktionieren.
4. Bibliothek: für Mathe JSXGraph (`https://cdn.jsdelivr.net/npm/jsxgraph@1.13.3/distrib/jsxgraphcore.js`), sonst reines JavaScript + SVG. Keine weiteren Abhängigkeiten.

### Technische Regeln (erprobt)
- Zustand (Punkte, n, ε, Grad …) in einem Objekt `S` außerhalb der Zeichnung halten. Bei Eingabe: begrenzen → einrasten (z. B. Winkel auf π/12, Werte auf 0,5) → `S` aktualisieren → `render()`. Grenzen so wählen, dass auch abgeleitete Objekte sichtbar bleiben.
- Regler als HTML `<input type=range>` einmal anlegen und beim Rendern **nicht** ersetzen (nur den Ergebnisteil neu schreiben), sonst bricht das Ziehen ab.
- Farben als CSS-Variablen; Zeichnung bei Moduswechsel, Hell/Dunkel-Wechsel (`matchMedia('(prefers-color-scheme: dark)')`) und Größenänderung (`ResizeObserver`, entprellt) komplett neu aufbauen.
- JSXGraph: CSS ist nicht separat ladbar, deshalb inline `.jxgbox{border:0} .JXG_navigation{display:none!important}`; `JXG.Options.text.display='internal'`; Board mit `keepAspectRatio:true, showNavigation:false, showCopyright:false`, Pan/Zoom aus; Achsen und Gitter selbst zeichnen; vor Neuaufbau `JXG.JSXGraph.freeBoard(board)`.
- Ein-/Ausblenden über `el.hidden` plus `[hidden]{display:none!important}`.
- Browser-Speicher nur für Kleinigkeiten (letzter Modus) und immer in `try/catch`.

### Testen vor dem Veröffentlichen
- Mindestens: Script extrahieren und `node --check`.
- Ist Playwright/Chromium verfügbar: Seite headless laden (CDN-Skripte bei Bedarf per `page.route` auf eine lokale Kopie aus `npm i jsxgraph@1.13.3` umleiten), Screenshots hell bei 1200 px und dunkel bei 400 px, jeden Modus anklicken, Konsolenfehler und `document.documentElement.scrollWidth` prüfen. Eine Korrekturrunde.

Danach veröffentlichen und in einem Satz sagen, was die Seite zeigt und was man ausprobieren soll.
