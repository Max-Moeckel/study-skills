# study-skills

[Deutsch](#deutsch) · [English](#english)

---

## Deutsch

Eine Sammlung von [Claude Skills](https://claude.com/docs/skills/overview) fürs Lernen an der Uni. Die Skills sind fachneutral und antworten in der Sprache, in der du schreibst.

### Skills

| Skill | Wofür |
|---|---|
| [exam-tutor](skills/exam-tutor/SKILL.md) | Tutor für ein beliebiges Fach. Fragt beim ersten Mal nach Material, Prüfer, Klausurtermin und Schwerpunkten, baut daraus einen Lernplan, erklärt Stoff, stellt und bewertet Aufgaben und führt einen Lernstand. |
| [study-visualization](skills/study-visualization/SKILL.md) | Bilder zu Aufgaben und Begriffen: statische Skizzen (TikZ, matplotlib, Graphviz) und interaktive Lernseiten mit Reglern. Ergänzt `exam-tutor`. |

### Installieren

**Claude-App (claude.ai, Desktop):**
1. [Customize › Plugins](https://claude.ai/customize/plugins) öffnen.
2. **Add › Add marketplace** wählen und `Max-Moeckel/study-skills` eingeben.
3. Das Plugin **study-skills** hinzufügen.

Die Skills sind dann in Chat, Cowork und Claude Code verfügbar.

**Claude Code:**
```
/plugin install study-skills --marketplace Max-Moeckel/study-skills
```
oder im Terminal:
```bash
claude plugin marketplace add Max-Moeckel/study-skills
claude plugin install study-skills@study-skills
```

**Einzelner Skill per Upload:** den Skill-Ordner zippen (`cd skills && zip -r exam-tutor.zip exam-tutor`) und unter [Customize › Skills](https://claude.ai/customize/skills) hochladen.

### Benutzen

Einfach anfangen, z. B. „Hilf mir bei der Klausurvorbereitung in Statistik“. Beim ersten Mal stellt `exam-tutor` ein paar Fragen und legt zwei Dateien an:

- `course.md`: Kursangaben (Prüfer, Termin, Format, Material, Schwerpunkte)
- `progress.md`: Lernplan und Fortschritt

Beide liegen in deinem Kursordner bzw. Projekt, nicht in diesem Repo. Die `.gitignore` schließt sie sicherheitshalber aus.

In Claude Code direkt aufrufen: `/study-skills:exam-tutor`.

### Neuen Skill hinzufügen

Ein Ordner pro Skill unter `skills/`, darin eine `SKILL.md` mit Frontmatter. Der Ordnername muss dem `name` entsprechen:

```markdown
---
name: my-skill
description: "Ein Satz, wann Claude den Skill benutzen soll."
---

# Anleitung …
```

Danach `version` in `.claude-plugin/plugin.json` erhöhen und mit `claude plugin validate .` prüfen. Vor dem Pushen: keine Namen, Pfade, Links oder Kursmaterial.

### Lizenz

[MIT](LICENSE)

---

## English

A collection of [Claude skills](https://claude.com/docs/skills/overview) for studying at university. The skills are subject-neutral and reply in whatever language you write in.

### Skills

| Skill | What it does |
|---|---|
| [exam-tutor](skills/exam-tutor/SKILL.md) | Tutor for any course. On first use it asks for your materials, examiner, exam date and priorities, builds a study plan, explains topics, sets and grades practice problems and keeps a progress log. |
| [study-visualization](skills/study-visualization/SKILL.md) | Pictures for tasks and concepts: static sketches (TikZ, matplotlib, Graphviz) and interactive study pages with sliders. Complements `exam-tutor`. |

### Install

**Claude app (claude.ai, desktop):**
1. Open [Customize › Plugins](https://claude.ai/customize/plugins).
2. Choose **Add › Add marketplace** and enter `Max-Moeckel/study-skills`.
3. Add the **study-skills** plugin.

The skills are then available in chat, Cowork and Claude Code.

**Claude Code:**
```
/plugin install study-skills --marketplace Max-Moeckel/study-skills
```
or in your terminal:
```bash
claude plugin marketplace add Max-Moeckel/study-skills
claude plugin install study-skills@study-skills
```

**Single skill via upload:** zip the skill folder (`cd skills && zip -r exam-tutor.zip exam-tutor`) and upload it under [Customize › Skills](https://claude.ai/customize/skills).

### Usage

Just start, e.g. "Help me prepare for my statistics exam". On first use `exam-tutor` asks a few questions and creates two files:

- `course.md`: course details (examiner, date, format, materials, priorities)
- `progress.md`: study plan and progress

Both live in your course folder or project, not in this repo. The `.gitignore` excludes them just in case.

Invoke directly in Claude Code: `/study-skills:exam-tutor`.

### Adding a skill

One folder per skill under `skills/`, containing a `SKILL.md` with frontmatter. The folder name must match `name`:

```markdown
---
name: my-skill
description: "One sentence on when Claude should use this skill."
---

# Instructions …
```

Then bump `version` in `.claude-plugin/plugin.json` and check with `claude plugin validate .`. Before pushing: no names, paths, links or course material.

### License

[MIT](LICENSE)
