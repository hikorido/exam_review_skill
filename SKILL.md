---
name: revision-tracker
description: >
  Generates a personalised exam revision plan and interactive HTML checklist tracker
  from a student's actual course materials (lectures, briefings, workbooks, slides).
  Use this skill whenever a student asks for a revision plan, study schedule, exam prep,
  or wants to organise their studying — especially when they have course files accessible
  via MCP tools (local folders, drives, etc.). Also triggers when the user mentions
  upcoming exams, needs a study tracker, or wants to turn their course materials into
  an actionable day-by-day plan. Do NOT wait for the user to explicitly say "use the
  revision tracker skill" — if they mention exams and course materials, use it.
---

# Revision Tracker Skill

Turns a student's raw course materials into a personalised, day-by-day revision plan
delivered as a downloadable interactive HTML checklist that saves progress in the browser.

---

## Phase 1: Gather context

Before reading any files, ask the student for:

1. **What courses** are they revising? (names + any relevant details like closed/open book)
2. **Where are the materials?** (MCP-connected folder path, or ask them to describe the structure)
3. **When is the final review day?** (treat this as the deadline — not necessarily the exam date)
4. **Daily availability and constraints** — how many hours, any ADHD/focus issues, blocked days?

If the student has already provided some of this in the conversation, extract it and only ask for what's missing.

---

## Phase 2: Explore the course materials

Use the available MCP file tools (`list_directory`, `read_text_file`) to explore the student's course folders. For each course, you need to find and read:

- **Lecture slides** (PDFs or markdown slide files)
- **Briefing slides** (weekly briefing PDFs or notes)
- **Workbook README files** (these contain the actual exercises and cross-references)
- **Past exam papers** (if present — prioritise finding these)
- **Any syllabus or topic list**

### Reading strategy

1. Start with `list_directory` at the top level to understand the folder structure
2. For each course, list subdirectories to find lectures, briefings, workbooks
3. Read `README.md` files in workbooks — these contain the full exercise descriptions and cross-references to slides
4. For markdown slide files, read them in full. For PDFs you can't read directly, infer from filenames and folder names
5. Look for past exam files — they are the highest-value material

**Important:** Read workbooks carefully. They typically reference specific slide segments (e.g. `WB2/03 Inheritance/slides/segment-1.pdf`) — note these cross-references so each study session can point to the exact right material.

### What to extract per course

- Complete topic list (one topic per week/workbook)
- Which topics are lecture-based vs workbook-based vs project-based
- Any projects or coursework the student built (OXO game, DB server, etc.) — these are excellent exam practice vehicles
- Exam format: closed book or open book (completely changes study strategy)
- Past paper availability

---

## Phase 3: Build the revision plan

### Principles

- **Closed-book exams** need recall practice — sessions end with "write from memory" exercises
- **Open-book exams** need understanding + a well-organised notes sheet — sessions focus on comprehension and building the cheat sheet
- **ADHD-friendly structure**: 3 sessions per day, 25–35 min each, with rest days built in (roughly 1 rest/buffer day per 6 study days)
- **Weighting**: allocate more days to harder topics and closed-book subjects; open-book subjects need less memorisation time
- **Recall testing**: every 6–7 days, insert a "recall test" day where the student writes everything from memory without notes
- **Past papers**: schedule at least one full timed past paper attempt in the final week

### Session structure (3 per day)

Each session for a content day should follow:
1. **READ** — the specific lecture PDF or workbook slides to open (exact filename/path)
2. **DO** — the specific workbook exercise or task to complete
3. **PAPER/RECALL** — a from-memory writing exercise to test retention

For rest/buffer days, sessions are lighter:
1. Light review of the week's notes (30 min max)
2. No new material

For recall test days:
1. One course recall exercise (write key concepts from memory)
2. Another course recall exercise
3. Cross-course exercise or past paper question

### Day allocation example pattern (25 days, 3 courses)

```
Week 1: Course A × 3 days, Course B × 1 day, Course C × 1 day, Rest × 1, Recall × 1
Week 2: Course A × 2 days, Course B × 1 day, Course C × 2 days, Rest × 1, Recall × 1
Week 3: Mix harder topics, Course C past paper, Rest × 1, Full mock × 1
Week 4: Consolidation only, cheat sheet building (open book), final light review
Final day: Skim notes only, no new material
```

Adjust this pattern based on the actual number of courses, difficulty, and days available.

---

## Phase 4: Generate the interactive HTML tracker

Produce a single self-contained HTML file. The file must:

### Structure
- Dark or light theme (ask the student, or default to light)
- Header with overall completion percentage and a colour progress bar
- Course colour legend
- "Today" bar that auto-detects today's date and shows the current day's topic + sessions done
- A calendar grid (7 columns, one row per week) of clickable day cards
- A detail panel that opens when a day card is clicked
- A reset button

### Day cards
Each card shows:
- Date (e.g. "Apr 7")
- Course badge (colour-coded: Java = purple, SEDP = green, Software Tools = orange, Mixed = amber, Rest = grey)
- Topic title
- Mini dots showing sessions done/total

### Detail panel
Opens below (or above on mobile) the clicked card. Shows:
- Day title and date
- Each session as a clickable checklist item — click to tick/untick
- Ticked sessions show strikethrough text and a green checkbox
- A study tip specific to that day

### Persistence
Use `localStorage` to save and restore tick state. Key format: `revision_tracker_v1` (or bump the version if iterating). State is a JSON object mapping `d{dayIndex}_s{sessionIndex}` to `true/false`.

### Styling rules
- Font: Google Fonts — `Syne` (headings, badges) + `DM Mono` (body)
- No external JS dependencies — pure vanilla JS only
- Fully self-contained: no external CSS files, no frameworks
- Must work offline once downloaded
- Mobile-responsive: on narrow screens, calendar collapses to 4 or 3 columns

### Code quality
- Single `<script>` block at the bottom
- The `days` array is the only data source — all rendering derives from it
- `buildCalendar()`, `openDay(idx)`, `closePanel()`, `toggle(dayIdx, sessIdx)`, `updateUI(dayIdx)`, `updateOverall()`, `setTodayBar()`, `resetAll()` as named functions

---

## Phase 5: Output

1. Save the HTML file to `/mnt/user-data/outputs/revision-tracker.html`
2. Use `present_files` to share it with the student
3. Briefly explain how to use it:
   - Open in any browser
   - Click a day card to see the session breakdown
   - Click each session to tick it off
   - Progress saves automatically

---

## Common corrections to anticipate

These are mistakes that came up during the original creation — watch for them:

- **Don't forget the workbooks.** Lecture slides alone miss the practical exercises. Always read workbook README files and cross-reference them with the relevant lecture.
- **Check exam scope.** If a course has Teaching Block 1 and Teaching Block 2, confirm which TB the upcoming exam covers — don't include the wrong half.
- **Cross-reference in sessions.** Each session description should name the exact file to open (e.g. "Lecture 08 (Iterator-Pattern.pdf) + WB4 Task 5 slides") — not just "read the lecture slides".
- **Closed vs open book changes everything.** Closed-book sessions must end with a paper recall exercise. Open-book sessions should build toward a structured notes sheet.
- **Past papers are the highest-value resource.** If they exist, schedule at least one full attempt and one answer-review session.
- **ADHD-friendly pacing.** Keep sessions short (25–35 min). Build in rest days. Don't cram 5 things into one session.

---

## Iterating on the tracker

If the student asks to change the plan (e.g. wrong exam scope, missing topics, wrong date range):

1. Identify which days are affected
2. Re-read the relevant course materials if needed
3. Update only the `days` array entries in the HTML — don't rebuild the whole file unless the structure is broken
4. Re-present the updated file

If the student asks for a theme change (light/dark, font, colours), update only the CSS `:root` variables and relevant colour references.
