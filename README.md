# study-skills

Eine Sammlung von [Claude Skills](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) fürs Lernen an der Uni. Die Skills sind fachneutral und enthalten keine Kurs- oder Personendaten.

## Skills

| Skill | Wofür |
|---|---|
| [klausur-tutor](skills/klausur-tutor/SKILL.md) | Tutor für ein beliebiges Fach. Fragt beim ersten Mal nach Material, Prüfer, Klausurtermin und Schwerpunkten, baut daraus einen Lernplan, erklärt, stellt und bewertet Aufgaben und führt einen Lernstand. |
| [lern-visualisierung](skills/lern-visualisierung/SKILL.md) | Bilder zu Aufgaben und Begriffen: statische Skizzen (TikZ, matplotlib, Graphviz) und interaktive Lernseiten mit Reglern. Ergänzt `klausur-tutor`. |

## Installieren

**claude.ai / Claude-App:** Den Skill-Ordner (z. B. `skills/klausur-tutor`) als ZIP packen und in den Einstellungen unter Skills hochladen.

```bash
cd skills && zip -r klausur-tutor.zip klausur-tutor
```

**Claude Code:** Den Ordner nach `~/.claude/skills/` (für dich) oder `.claude/skills/` (für ein Projekt) kopieren.

## Benutzen

Einfach anfangen, z. B. „Hilf mir bei der Klausurvorbereitung in Statistik“. Beim ersten Mal stellt `klausur-tutor` ein paar Fragen und legt zwei Dateien an:

- `kurs.md`: Kursangaben (Prüfer, Termin, Format, Material, Schwerpunkte)
- `lernstand.md`: Lernplan und Fortschritt

Beide liegen in deinem Kursordner bzw. Projekt, nicht in diesem Repo. Die `.gitignore` schließt sie sicherheitshalber aus.

## Neuen Skill hinzufügen

Ein Ordner pro Skill unter `skills/`, darin eine `SKILL.md` mit Frontmatter:

```markdown
---
name: mein-skill
description: Ein Satz, wann Claude den Skill benutzen soll.
---

# Anleitung …
```

Vor dem Veröffentlichen prüfen: keine Namen, Pfade, Links oder Kursmaterial.
