# revision-tracker

A Claude skill that reads your actual course materials and generates a personalised, day-by-day exam revision plan — delivered as a downloadable interactive HTML checklist you can tick off as you study.

Built for students who have their lecture slides, briefings, and workbooks in a folder Claude can access via MCP.

> Originally built for a CS master's student juggling three exam courses with 25 days to go. Generalised to work for any subject, any course structure, and any deadline.

**[→ Live demo](https://hikorido.github.io/exam_review_skill/)**

---

## What it does

1. **Reads your course materials** — lectures, briefings, workbook exercises, past papers — via MCP-connected folders
2. **Builds a day-by-day plan** cross-referencing the exact files you need to open each session
3. **Generates an interactive HTML tracker** you download and open in any browser — no account, no internet required after download
4. **Saves your progress** automatically using browser localStorage

Each study day gets 3 short sessions (25–35 min each), structured as:

- **READ** — the exact lecture PDF or slide segment to open
- **DO** — the specific workbook task to work through
- **RECALL** — a from-memory writing exercise to test retention

---

## Demo

**[→ https://hikorido.github.io/exam_review_skill/](https://hikorido.github.io/exam_review_skill/)**

Here's an example tracker generated for three fictional courses over 25 days:

| Day | Course | Topic | Sessions |
|-----|--------|-------|----------|
| Apr 7 | Algorithms (closed book) | Sorting & complexity | Read Lecture 03 · Do WB2 Task 2 · Write Big-O from memory |
| Apr 8 | Algorithms (closed book) | Trees & graphs | Read Lecture 04 · Do WB3 Task 1 · Draw BST insert from memory |
| Apr 9 | Systems Design (closed book) | REST & HTTP | Read Slides 11 · Do lab exercise · Write a raw HTTP request |
| Apr 10 | Web Dev (open book) | HTML & CSS | Read Slides 12–13 · Do workbook · Build notes sheet section |
| Apr 11 | Rest / buffer | — | Light review only |

> *Click a day card in the HTML tracker → see the session breakdown → tick each one off as you finish*

---

## Requirements

- [Claude Desktop](https://claude.ai/download) with skills support
- At least one MCP file server connected to your course materials folder

---

## Installation

1. Download [`revision-tracker.skill`](./revision-tracker.skill)
2. Open Claude Desktop
3. Go to **Settings → Skills**
4. Drag in the `.skill` file

The skill will trigger automatically whenever you mention exams and course materials.

---

## Usage

Just talk to Claude naturally. Some prompts that trigger it:

> *"My exams start May 15th. I have three courses — Algorithms, Systems Design, and Web Dev. Can you read my course folders and make me a revision plan?"*

> *"I need to revise for my closed-book exam in 3 weeks. Can you make me a study schedule with a checklist?"*

> *"Turn my course materials into a revision tracker I can tick off."*

Claude will ask a few questions (deadline, daily availability, any focus constraints like ADHD), explore your course folders, then generate the plan and HTML file.

---

## What Claude reads from your folders

| Material | Why |
|---|---|
| Lecture slide files (PDF or `.md`) | Topic list and content per week |
| Workbook `README.md` files | Exact exercises + cross-references to slides |
| Briefing PDFs | Weekly context and project briefs |
| Past exam papers | Scheduled as high-value practice sessions |

The skill cross-references all of these so each session points to the **exact file to open** — not just "read the lecture slides".

---

## Example folder structure Claude can work with

```
MyCourseMaterials/
├── Algorithms/
│   ├── Weekly Lectures/
│   │   ├── 01-Introduction.pdf
│   │   ├── 02-Sorting.pdf
│   │   └── ...
│   ├── Weekly Workbooks/
│   │   ├── 01 Basics/
│   │   │   └── README.md      ← skill reads these for exercise cross-refs
│   │   └── ...
│   └── past-papers/
│       ├── exam-2024.pdf
│       └── exam-2024-answers.pdf
├── SystemsDesign/
│   └── slides/
│       ├── 1a-Introduction.pdf
│       └── ...
└── WebDev/
    ├── 11-http/
    │   └── slides.md
    └── ...
```

---

## HTML tracker features

- **Calendar grid** — one card per day, colour-coded by course
- **Clickable sessions** — tick each one off as you finish
- **Progress bar** — overall % complete across all sessions
- **Today bar** — auto-detects today's date, shows what's due and sessions done
- **Persistent state** — saves in `localStorage`, survives browser restarts
- **Offline** — works with no internet after download
- **Mobile-friendly** — calendar collapses on small screens

---

## Closed book vs open book

The skill adapts session structure based on exam format:

**Closed book** — sessions end with a paper recall exercise ("write this from memory"). Rest days and recall test days built in every ~6 days.

**Open book** — sessions focus on understanding and building a structured notes sheet to bring into the exam.

---

## Study design principles

- 3 sessions × 25–35 min per day (ADHD-friendly, no marathon study blocks)
- ~1 rest/buffer day per 6 study days
- Recall test days every week (self-testing beats rereading)
- Past paper session in the final week
- Final day is skim-only — no new material

---

## Adapting the plan

If something needs changing after the tracker is generated, just tell Claude:

> *"Actually the Web Dev exam only covers the second half of the course."*  
> *"Can you make it a dark theme?"*  
> *"I got the dates wrong — the deadline is April 28, not May 2."*

Claude will update only the affected parts rather than rebuilding from scratch.

---

## Repo structure

```
revision-tracker/
├── SKILL.md                  # Skill instructions Claude reads
├── revision-tracker.skill    # Packaged skill file (install this)
├── index.html                # GitHub Pages landing page
├── evals/
│   └── evals.json            # Test cases
└── README.md                 # This file
```

---

## Contributing

PRs welcome. Some ideas:

- Google Drive / Notion as material sources
- Additional colour themes
- Export plan as PDF alongside the HTML
- Week-view vs month-view calendar option

---

## Licence

MIT
