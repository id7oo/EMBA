# Course import: Blackboard → Study guide

Run this from a Claude session that can use the student's own signed-in Chrome (Claude in Chrome, or the Claude desktop app with Chrome connected). The cloud sync can't do it, because Blackboard and SIS need the student's browser session and MFA. The session also needs the `ArtifactData` tool for the workspace: https://claude.ai/artifact/BrrZBD4T78NMkDyWDNbSDe

Never type, read or store passwords; the student signs in. Read only: don't submit, post, or change anything on Blackboard or SIS.

## Steps

1. **Blackboard → Courses.** For each current or upcoming course, write `courses/c-<code>`:
   - `name`, `code`, `module`, `professor`
   - `overview_ar` (what the course is about, in Arabic) and `why_ar` (why it matters for the student's career)
   - `order`, `source_link`
2. **Each course's content** (syllabus, weekly folders, slides, readings, cases). For every class session or unit, write `lessons/l-<code>-<n>`:
   - `course_id`, `title`, `date` (if scheduled), `order`
   - `summary_ar`: an Arabic explanation in markdown, as a tutor would teach it, not a copy
   - `key_concepts[{term, explain_ar, example_ar}]`, with examples from Saudi workplaces
   - `prep[]` (what to do before class), `readings[{title, url, minutes}]`
   - `questions[]` (smart things to raise in class), `quiz[{q, answer, why}]` (3–5 self-check questions)
   - `source_link`
3. **Due dates and assessments.** Write tasks `t-bb-<id>` (`type` assignment or exam, `source: "blackboard"`, `link`). Write teaching weeks as `events` (`kind: "module"`, `Module #N`).
4. **Calendar feed.** Calendar → settings → *Get external calendar link*. Show it to the student so they can add it to Google Calendar as "Blackboard". Don't store the link anywhere.
5. **SIS.** Record the timetable, payment plan, next installment and any holds as tasks or events. Never copy card or bank details.
6. **Outlook.** List mail from the last 30 days that isn't in Gmail. Help the student set up forwarding, or the Power Automate flow from `docs/integrations.md`. When it works, set `settings/main.connections.outlook = "ok"`.
7. **Record the run.** Set `settings/main.connections.chrome_last` to the current time, and add a line to `meta/sync.last_summary`.

Write in Arabic for the student; keep course names, terms and email text in English.
