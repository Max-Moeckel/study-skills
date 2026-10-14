---
name: study-visualization
description: "Study visuals for tasks and concepts in any subject: static sketches (TikZ, matplotlib, Graphviz) as PNG/PDF and interactive study pages with sliders. Complements exam-tutor."
---

# Study Visualization

Delivers the right picture when a concept or task is being explained. Subject-neutral; course details (notation, course folder) come from `course.md` if the skill `exam-tutor` has been set up. Progress tracking is done by `exam-tutor`.

**Language:** answer in the language of the user's latest message. Labels in pictures follow the language and notation of the course material (e.g. decimal comma in German material).

## 1. Is a picture worth it?

Yes, when it shows structure that text carries poorly: position in space, graphs, processes, dependencies, hierarchies, timelines, comparisons. No for plain definitions or lists.

## 2. Choosing the tool

| Content | Tool | Output |
|---|---|---|
| Formulas, function graphs, geometry, mathematical sketches | TikZ / pgfplots | PDF + PNG |
| Data, distributions, numerical functions | matplotlib | PNG (+ SVG) |
| Processes, assessment schemes, trees, automata, concept maps | Graphviz (`dot`) | PNG + SVG |
| "What happens if …": dragging parameters, sliders | interactive HTML page (artifact) | link |
| Quick explanatory sketch that won't be kept | inline visualization in the chat, if the environment has one | inline |

Unclear → make a static picture for the task and offer the interactive version in one sentence.

Check what's installed first (`which pdflatex dot`, `python3 -c "import matplotlib"`), install what's missing (`pip install --break-system-packages …`, `apt` only if allowed). If neither works: fall back to matplotlib.

## 3. Didactics

- **No spoilers:** for tasks the user is supposed to solve, draw only the task sketch (givens, axes, points). The solution sketch only after their answer or on request.
- **One picture, one key message.** Two sub-figures beat one overloaded figure.
- Adopt the notation, language and conventions of the course material. Exact values instead of decimals where possible (π/3, √2/2).
- Make typical pitfalls visible (open vs. closed endpoint, exception in a scheme, signs).
- With every picture, in the chat: 2–3 sentences on **"What you should see in the picture"** and 1–2 short tasks that use the picture ("Read off …", "Drag … so that …").

## 4. Styling (same for all tools)

| Role | Colour |
|---|---|
| `acc` main object | #1F5FBF |
| `wcol` second object / comparison | #C2410C |
| `res` result / limit / solution | #0F7A5A |
| `muted` helper labels | #6B7280 |
| `gridc` grid, helper lines | #D9DDE5 |

Never encode meaning by colour alone: add line style (solid/dashed) or markers. White background for static pictures.

## 5. Static pictures

1. Write the source to `viz/<topic>_<short>.<tex|py|dot>` in the working directory.
2. Build:
   ```bash
   # TikZ
   pdflatex -interaction=nonstopmode -halt-on-error X.tex > /dev/null; grep "^!" -A3 X.log; pdftoppm -png -r 160 -singlefile X.pdf X
   # matplotlib
   python3 X.py            # saves X.png (dpi=160) and X.svg
   # Graphviz
   dot -Tpng -Gdpi=160 X.dot -o X.png && dot -Tsvg X.dot -o X.svg
   ```
3. **Always look at the PNG yourself** and check for overlapping labels, clipped parts and legibility. At most two correction rounds.
4. Deliver: send the PNG for viewing, PDF/SVG for printing and the source file for reuse. If the course folder is connected, also write them to `<course folder>/visualizations/`.

### Labels (otherwise they overlap)
- Offset labels on axes and lines to the side; don't place them on the line.
- Axis titles at the arrow tip (`below left` / `below right`).
- Formula boxes outside the figure; the connector line must not cross other labels.
- Don't put white-filled labels on top of other labels; move them instead.
- With many tick labels use a smaller font (`\scriptsize`, `fontsize=8`).

### TikZ preamble
```latex
\documentclass[border=8pt]{standalone}
\usepackage{tikz,pgfplots,amsmath,amssymb}
\pgfplotsset{compat=1.18}
\usetikzlibrary{arrows.meta,angles,quotes,calc,positioning}
\usepgfplotslibrary{fillbetween}
\definecolor{acc}{HTML}{1F5FBF}\definecolor{wcol}{HTML}{C2410C}
\definecolor{res}{HTML}{0F7A5A}\definecolor{muted}{HTML}{6B7280}\definecolor{gridc}{HTML}{D9DDE5}
\tikzset{axis arrow/.style={-{Stealth[length=2.2mm]},thin},
         vec/.style={-{Stealth[length=2.6mm]},very thick,acc}}
```
pgfplots: `axis lines=middle`; open endpoints `mark=o`, closed ones `mark=*`; bands (e.g. an ε-tube) with `fill between`, `opacity=.12`; clip runaway curves with `restrict y to domain`.

### matplotlib skeleton
```python
import matplotlib; matplotlib.use("Agg")
import matplotlib.pyplot as plt
C = dict(acc="#1F5FBF", wcol="#C2410C", res="#0F7A5A", muted="#6B7280", gridc="#D9DDE5")
fig, ax = plt.subplots(figsize=(7, 4.2))
ax.grid(color=C["gridc"], lw=.6); ax.spines[["top", "right"]].set_visible(False)
# … plot with color=C["acc"] etc.
fig.tight_layout(); fig.savefig("X.png", dpi=160); fig.savefig("X.svg")
```

### Graphviz skeleton
```dot
digraph G {
  rankdir=TB; bgcolor="white";
  node [shape=box, style="rounded,filled", fillcolor="white", color="#1F5FBF", fontname="Helvetica", fontsize=12];
  edge [color="#6B7280", fontname="Helvetica", fontsize=10];
  // result/end nodes: color="#0F7A5A"; exception/error path: color="#C2410C", style=dashed
}
```

## 6. Interactive study pages

1. If there is a skill for artifact design (e.g. `artifact-design`), **load it before writing** and follow it. Without an artifact tool: send a self-contained `.html` file.
2. **One page per topic block** ("Sequences Lab", "Scheme Trainer"). First check the user's existing artifacts and extend a matching page with a new mode instead of creating a new one.
3. Layout: drawing area (square for geometry), controls/options below it, and next to or below that a **log** that writes out the calculation or assessment steps live with exact values. Modes as a toggle at the top. Must work on a phone (≈ 400 px) without horizontal scrolling.
4. Library: JSXGraph for maths (`https://cdn.jsdelivr.net/npm/jsxgraph@1.13.3/distrib/jsxgraphcore.js`), otherwise plain JavaScript + SVG. No other dependencies.

### Technical rules (tested)
- Keep state (points, n, ε, degree …) in an object `S` outside the drawing. On input: clamp → snap (e.g. angles to π/12, values to 0.5) → update `S` → `render()`. Choose limits so that derived objects stay visible too.
- Create sliders as HTML `<input type=range>` once and do **not** replace them on render (only rewrite the result part), otherwise dragging breaks.
- Colours as CSS variables; rebuild the drawing completely on mode change, light/dark change (`matchMedia('(prefers-color-scheme: dark)')`) and resize (`ResizeObserver`, debounced).
- JSXGraph: its CSS can't be loaded separately, so inline `.jxgbox{border:0} .JXG_navigation{display:none!important}`; `JXG.Options.text.display='internal'`; board with `keepAspectRatio:true, showNavigation:false, showCopyright:false`, pan/zoom off; draw axes and grid yourself; call `JXG.JSXGraph.freeBoard(board)` before rebuilding.
- Show/hide via `el.hidden` plus `[hidden]{display:none!important}`.
- Browser storage only for small things (last mode), always inside `try/catch`.

### Testing before publishing
- At minimum: extract the script and run `node --check`.
- If Playwright/Chromium is available: load the page headless (redirect CDN scripts via `page.route` to a local copy from `npm i jsxgraph@1.13.3` if needed), take screenshots light at 1200 px and dark at 400 px, click every mode, check console errors and `document.documentElement.scrollWidth`. One correction round.

Then publish and say in one sentence what the page shows and what to try.
