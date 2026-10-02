# Workspace data model

All personal data lives in the workspace's database (the `db` capability of the published artifact), never in this repository. Claude reads and writes it with the `ArtifactData` tool. The page reads it live and writes to it when the student ticks, adds or edits something.

Access rule declared at publish: `{ path: "", read: "owner", write: "owner" }`. Only the artifact's owner can read or write any of it. A shared link shows nothing.

Conventions:

- **Dates** are `YYYY-MM-DD` (all-day) or ISO 8601 with offset, e.g. `2026-10-18T18:00:00+03:00`. The student's time zone is `Asia/Riyadh` (UTC+3, no DST).
- **Timestamps** (`created_at`, `updated_at`, `done_at`, `generated_at`, `last_run_at`) are ISO 8601 UTC strings.
- **IDs** are stable so that re-running a sync never duplicates anything:
  - email-derived digest items: `gm-<gmailThreadId>`
  - tasks: `t-<short-slug>` when Claude creates them from a known source (`t-english-fee`), `t-bb-<calendarEventId>` for Blackboard deadlines, and `t-<base36 time>-<random>` when created in the page
  - events: `ev-<short-slug>` (`ev-module-2`, `ev-english-3`)
  - memory: `m-<...>`
- Never store passwords, one-time codes, bank account numbers or IBANs. Point to the source email (`link`) instead.

## Collections

### `tasks/<id>`

| field | type | notes |
|---|---|---|
| `title` | string | short imperative, required |
| `details` | string | markdown: `**bold**`, `- lists`, `[links](https://…)` |
| `due` | string \| null | `YYYY-MM-DD` or ISO datetime with offset |
| `priority` | `high` \| `medium` \| `low` | |
| `type` | `assignment` \| `exam` \| `reading` \| `prep` \| `admin` \| `personal` | drives the Tasks filters |
| `status` | `open` \| `done` | |
| `course` | string | course or module name, e.g. `English course`, `Module 2` |
| `source` | `email` \| `blackboard` \| `calendar` \| `claude` \| `manual` | |
| `source_ref` | string | Gmail thread id or calendar event id (used for dedupe) |
| `source_label` | string | human label, e.g. the email subject and date |
| `link` | string | URL to open the source (Gmail thread link, Blackboard) |
| `calendar_event_id` | string | set when Claude created a Google Calendar reminder for it |
| `added_by` | `copilot` \| `claude` \| `me` | |
| `created_at`, `updated_at`, `done_at` | string | |

### `events/<id>`

Programme schedule items Claude learned from email: module weeks, classes, sessions, exams. Live Google Calendar events are read by the page directly and are not copied here.

| field | type | notes |
|---|---|---|
| `title` | string | Module weeks are titled `Module #N` so the page can number them |
| `start`, `end` | string | `end` is inclusive for all-day events |
| `all_day` | boolean | |
| `kind` | `module` \| `class` \| `exam` \| `deadline` \| `event` | `module` drives the progress line and countdown |
| `location`, `notes` | string | |
| `source`, `source_ref`, `link`, `updated_at` | | as for tasks |

Don't create an `event` for something that is already a dated task: the Schedule view shows dated tasks as deadlines.

### `inbox/<gm-threadId>`

Claude's digest of MBSC mail: one document per email thread.

| field | type | notes |
|---|---|---|
| `subject`, `from`, `from_name` | string | |
| `date` | string | date of the latest message in the thread |
| `summary` | string | 1–3 sentences: what it says and what it means for the student |
| `action` | string \| null | the one thing to do, short imperative |
| `due` | string | optional, `YYYY-MM-DD` |
| `importance` | `high` \| `normal` \| `low` | |
| `status` | `new` \| `done` | `done` = handled or purely informational |
| `thread_id`, `link` | string | Gmail thread id and its web link |
| `task_ids` | string[] | tasks created from this email (hides “Make it a task”) |

### `memory/<id>`

| field | type | notes |
|---|---|---|
| `text` | string | one durable fact, preference, goal or person |
| `category` | `profile` \| `preference` \| `goal` \| `course` \| `people` \| `other` | |
| `source` | `me` \| `claude` | |
| `created_at` | string | |

### `drafts/<id>`

Emails Claude prepared for the student to send: `title`, `purpose`, `to`, `cc`, `subject`, `body`, `status` (`ready` | `sent`), `lang`, `created_at`, `updated_at`. Claude never sends them.

### `courses/<id>` (optional)

`name`, `code`, `module`, `professor`, `notes`. Fill it in once course names are known (they're in PDFs and on Blackboard, not in email text).

## Single documents

| path | contents |
|---|---|
| `profile/main` | `name` (first name), `full_name`, `student_email`, `personal_email`, `student_id` (when known), `timezone` |
| `program/info` | `name`, `short_name`, `school`, `cohort`, `class_of`, `format`, `start` (first teaching day), `duration_months`, `expected_end` (once known), `next_module_note`, `facts[]` (strings), `contacts[]` (`name`, `role`, `email`, `phone`), `links[]` (`label`, `url`) |
| `insights/main` | Claude's analysis: `lang`, `headline`, `assessment`, `week_plan[]`, `risks[]`, `decisions[]`, `load[]`, `generated_at`. The page's **Rethink** button regenerates it with the student's own Claude plan |
| `brief/today` | `date` (`YYYY-MM-DD`), `headline`, `body` (markdown bullets), `generated_at` |
| `meta/sync` | `last_run_at`, `runs`, `schedule`, `next_run_hint`, `last_summary` (markdown), `sources` (map of source → status) |
| `settings/main` | `routine_trigger_id`, `calendar_reminders` (bool), `language` (`ar` or `en`), `mail_query`, `timezone`, `setup.<step>` = `{done, at, by}` for steps `gmail`, `routine`, `blackboard_calendar`, `home_screen`, `outlook_forward`, `repo_private`, `chrome`, `m365` |

## Housekeeping

The database holds at most 25,000 documents. The sync deletes `done` tasks older than 120 days and `done` inbox items older than 60 days.
