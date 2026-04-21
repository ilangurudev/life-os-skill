# Reminders & Task Management

Load this file whenever creating, modifying, snoozing, cancelling, or asking about any task, reminder, or cron job.

## Task-to-Cron Flow (STRICT ORDER)

1. **FIRST:** Create or edit the task markdown file in `~/my-data/tasks`
2. **THEN:** Create the cron job — the cron prompt MUST:
   - Reference the task file path and instruct the cron agent to read it at runtime
   - Attach `skills:["life-os"]` on the cron job itself when creating or updating it
   - **Embed the full reminder format template inline** (see "Cron Prompt Template" below). The cron session has no access to this skill file, so if the format isn't in the prompt, the agent won't know how to structure the reminder.
   - Require the final note-link line verbatim, not paraphrased or relabeled
- Default reminder: 1 day before due_date at 9:00 AM user's timezone
- If `due_date` is today or tomorrow, remind in a few hours instead
- Save the cron job_id in the task frontmatter as `cron_id` so it can be updated later
- On task creation, initialize `last_5_reminder_times` in the task frontmatter to `never sent` unless this is a legacy reminder migration with known prior history
- **Never create a cron without a backing task file first**
- **If a task is very likely to want a reminder (which is almost always true), create a cron immediately after creating the file.** User will request a change to the reminder schedule if needed.

See also: [Birthday Reminders](./birthday-reminders.md) for relationship-aware cadences by closeness tier.

## Cron - Reminder Metadata Sync

For task reminders, keep these fields synced in the task frontmatter:
- `cron_id` — the cron job ID (or comma-separated list if multiple crons)
- `next_reminder_time` — the earliest upcoming scheduled run when a cron exists; if no reminder is currently scheduled, explicitly write `next_reminder_time: none scheduled` instead of leaving it blank
- `last_5_reminder_times` — a comma-separated newest-first history of the most recent reminder times that actually fired or were sent, capped at 5 entries. For a brand-new task with no reminder history, explicitly write `last_5_reminder_times: never sent`. If prior history for an older task cannot be reconstructed confidently, write `last_5_reminder_times: unknown`.

## Reminder Timing Rules

- If the user changes the timing of a recurring reminder (e.g. they confirm a preferred time and ask to move the next one), update the recurring cron itself so future runs stay aligned to that new time preference, not just the next occurrence.
- When the user asks for a specific reminder time, use that exact time. Only fall back to a reasonable default like 9:00 AM when they gave a vague time window such as "tomorrow morning," and say what default you picked.
- For recurring reminders created via `cronjob`, prefer explicit cron syntax (e.g. `0 16 * * 0`) over natural-language schedules if precision matters. Verify the created job's `next_run_at`, then sync that exact next occurrence back into the task frontmatter `next_reminder_time` and `cron_id`, while leaving `last_5_reminder_times` at `never sent` for a brand-new reminder or preserving the existing history for an updated reminder.

## Telegram Reply Targeting

When the user replies to a cron reminder on Telegram, the quoted snippet identifies which task/reminder they're responding to. Use that to find and edit the correct task file, appropriately. They might say done, snooze, rmeind me at x, etc. and you can edit the task/cron accoridngly. 

## Snooze / Cancel / Re-establish

- **"Done" or "already handled"** → for one-off reminders, remove the cron job via `cron(action='remove')`; for genuinely recurring reminders (daily/weekly/every-N-days/monthly cadence), do NOT remove the recurring cron. Instead record the completion in changelog, update the frontmatter fields, append the just-fired reminder time to `last_5_reminder_times`, keep the task active unless the user explicitly wants the routine ended, and sync `next_reminder_time` to the next scheduled run.
   - Reminder tool quirk worth remembering: the `cronjob` tool does not accept update strings like `every 3 days at 8:00 PM`. For every-N-days reminder changes, verify the resulting `next_run_at` after updating, and prefer explicit cron syntax or a one-off snooze plus follow-up re-establish flow instead of assuming natural-language parsing will work.
- **"Snooze"** → If vague, pick a reasonable snooze duration (e.g. 1 day, 3 days, 1 week depending on the task context), update the cron job, append the reminder that just fired to `last_5_reminder_times` (newest first, max 5), then explicitly tell Guru when they'll get the next reminder. Offer alternatives like "I'll remind you again Friday. Want a different day?"
- **"Remind me [day/time]"** → calculate the date, update cron to that day at 9:00 AM, confirm the new time
- **"Re-establish" / "set that up again" / "bring that reminder back"** → FIRST search existing task files, people/project notes, active cron jobs, and session history to recover the previous setup before creating anything new. Reuse the prior cadence/checklist/linking style when recoverable; only ask a follow-up if the old schedule is genuinely unclear.

## Cron Prompt Template

When creating the cron prompt, include this template verbatim — fill in the bracketed placeholders with actual values:

```
Read the task file at [FILE_PATH]. Send the user a warm, friendly reminder about [TASK_SUMMARY].
Keep it short and casual. Don't be robotic about it — talk like a friend, not a notification bot.
Base the reminder on the task file so it reflects the current state.

CRITICAL OUTPUT RULES:
- Your final output must end with exactly this sentence, character-for-character except for normal URL rendering:
Task created via life-os skill. Refer to note at https://ilangurudev.github.io/obsidian-links/?file=<folder>/<file>.md.
- Do not rename that line to "Task note" or any other label.
- Do not add an "Open questions" section.
- Do not add any text after that exact final sentence.
- Put that exact sentence on its own final line.
```

When building a cron prompt for any reminder that depends on context beyond the task note itself, explicitly include every required file path in the prompt and instruct the cron run to read them at runtime (for example `user-context.md`, a linked checklist note, or a source note like a quotes log). Cron runs are fresh sessions and will not reliably infer extra sources unless told.

Tone: conversational, not robotic. "Lavanya's birthday is in 2 weeks. Thought about what to get her?" not "REMINDER: Birthday upcoming."

## Daily Safety-Net Reconciliation Pattern

When designing or implementing reminder reliability, prefer a two-layer model:

### Layer 1: baseline task-level reminder cron
- Owns the normal cadence for the task
- Reads the task file before sending
- Sends the scheduled reminder
- Writes reminder-send state back into the task file

### Layer 2: daily safety-net reconciliation cron
- Runs as a central repair/escalation worker (Guru currently prefers 1:00 AM in their timezone)
- Scans open tasks for reminder drift, stale state, and missing/broken reminder linkage
- Is allowed to create or update cron jobs automatically with minimal user intervention
- Should create/update real reminder crons using the same life-os reminder prompt template and note-link rules, not some alternate message format

Use the safety-net layer to classify tasks into buckets such as:
- healthy
- missing cron
- broken cron linkage
- overdue with no future reminder
- unresolved after reminder
- recurring drift
- stale / review-needed

## Escalation Rules

Treat a missed reminder as a real miss immediately; do not assume silence means completion.

For one-off tasks without urgent due-date pressure, Guru's preferred default escalation ladder is:
- next day
- 2 days later
- 2 days later
- weekly thereafter until dispositioned

For tasks with a due date, compress that ladder and become more aggressive as the due date approaches.

## Recurring Task Occurrence Model

Recurring tasks are special: the task itself usually stays active (`status: todo`) even after one occurrence is completed.

Use this model:
- Keep the recurring task itself active unless the user explicitly retires/cancels it
- Treat `completed_time` as the timestamp of the last completed occurrence for recurring tasks
- Keep the audit trail in the changelog for each completion/snooze/skip/disposition
- Keep `next_reminder_time` as the next baseline scheduled reminder from the recurring cadence

Miss detection for recurring tasks should reason about the latest occurrence, not whether the task is globally done forever. A practical rule:
- if the latest reminder-send time is newer than `completed_time`, that occurrence is still unresolved
- if `completed_time` is after the latest reminder-send time, the latest occurrence was handled

When a recurring occurrence remains unresolved, the safety-net may create a temporary follow-up cron that nags until that occurrence is dispositioned, while leaving the baseline recurring cron intact.

When the user handles that occurrence:
- update `completed_time` if completed
- record snoozed/skipped/completed in the changelog
- remove the temporary follow-up cron
- keep the baseline recurring cron unless the user wants the whole routine ended

## Response Overrides for Cron Reminders

These override the normal life-os response formatting when the message is a cron reminder:
- Do not relabel the note link as `Task note:` or any paraphrase when the cron prompt provides an exact sentence to append. Use the exact sentence from the cron prompt verbatim.
- Do not add an `Open questions:` footer to cron reminders unless the cron prompt explicitly asks for it.

Don't forget to update `~/my-data/user-context.md` after creating or modifying any cron!


