# Reminders & Task Management

Load this file whenever creating, modifying, snoozing, cancelling, or asking about any task, reminder, or cron job.

## Task-to-Cron Flow (STRICT ORDER)

1. **FIRST:** Create or edit the task markdown file in `~/my-data/tasks`
2. **THEN:** Create the cron job — the cron prompt MUST:
   - Reference the task file path and instruct the cron agent to read it at runtime
   - Attach `skills:["life-os"]` on the cron job itself when creating or updating it
   - **Embed the full reminder format template inline** (see "Cron Prompt Template" below). The cron session has no access to this skill file, so if the format isn't in the prompt, the agent won't know how to structure the reminder.
   - Explicitly require reminder-metadata write-back in the task note before the final user-facing response is produced
   - Require the final note-link line verbatim, not paraphrased or relabeled
- Default reminder: 1 day before due_date at 9:00 AM user's timezone
- If `due_date` is today or tomorrow, remind in a few hours instead
- Save the cron job_id in the task frontmatter as `cron_id` so it can be updated later
- After creating the cron, update the task note's existing creation/queued-reminder changelog entry in place when possible instead of adding a near-duplicate second line. The final changelog should usually have one clean creation entry that includes both the original task capture and the linked cron/scheduled reminder details.
- On task creation, initialize `last_5_reminder_times` in the task frontmatter to `never sent` unless this is a legacy reminder migration with known prior history
- **Never create a cron without a backing task file first**
- **If a task is very likely to want a reminder (which is almost always true), create a cron immediately after creating the file.** User will request a change to the reminder schedule if needed.
- As for the due date, assign it if it's clear or add to list of open questions to get clarity from user. The reminder request time is not the due date.  

See also: [Birthday Reminders](./birthday-reminders.md) for relationship-aware cadences by closeness tier.
See also: [Maintainence & Linting Crons](./maintainenance-linting-crons.md) for guidance when setting up jobs to ensure vault health and consistency. 

## Cron - Reminder Metadata Sync

For task reminders, keep these fields synced in the task frontmatter:
- `cron_id` — the cron job ID (or comma-separated list if multiple crons)
- `next_reminder_time` — the earliest upcoming scheduled run when a cron exists; if no reminder is currently scheduled, explicitly write `next_reminder_time: none scheduled` instead of leaving it blank
- `last_5_reminder_times` — a comma-separated newest-first history of the most recent reminder times that actually fired or were sent, capped at 5 entries. For a brand-new task with no reminder history, explicitly write `last_5_reminder_times: never sent`. If prior history for an older task cannot be reconstructed confidently, write `last_5_reminder_times: unknown`.

When a cron reminder is currently firing, verify `next_reminder_time` from live cron state before editing the task note. Prefer `cronjob(action="list")` when available; if the cron run needs local state verification, `/home/ilangurudev/.hermes/cron/jobs.json` contains the persisted job definitions, including `id`, `schedule_display`, `next_run_at`, `last_run_at`, and `last_status`. Convert `next_run_at` into Guru's display timezone/format before writing it into frontmatter.

## Reminder Timing Rules

- If the user changes the timing of a recurring reminder (e.g. they confirm a preferred time and ask to move the next one), update the recurring cron itself so future runs stay aligned to that new time preference, not just the next occurrence.
- When the user asks for a specific reminder time, use that exact time. Only fall back to a reasonable default like 9:00 AM when they gave a vague time window such as "tomorrow morning," and say what default you picked.
- For recurring reminders created via `cronjob`, prefer explicit cron syntax (e.g. `0 16 * * 0`) over natural-language schedules if precision matters. Verify the created job's `next_run_at`, then sync that exact next occurrence back into the task frontmatter `next_reminder_time` and `cron_id`, while leaving `last_5_reminder_times` at `never sent` for a brand-new reminder or preserving the existing history for an updated reminder.
- For one-off reminders created via `cronjob`, pass the schedule as an ISO timestamp with offset (for example `2026-04-27T19:00:00-04:00`). Do not use strings like `2026-04-27 07:00 PM America/New_York` — the tool rejects them as invalid timestamps.

## Telegram Reply Targeting

When the user replies to a cron reminder on Telegram, the quoted snippet identifies which task/reminder they're responding to. Use that to find and edit the correct task file, appropriately. They might say done, snooze, rmeind me at x, etc. and you can edit the task/cron accoridngly. 

## Snooze / Cancel / Re-establish

- **"Done"** → Update the completed_time field, record the completion in changelog. 
   - For one-off reminders, remove the cron job via `cron(action='remove')`; Change status to done. 
   - For genuinely recurring reminders (daily/weekly/every-N-days/monthly cadence), do NOT remove the recurring cron. Keep the task as todo unless the user explicitly wants the routine ended. But update next_reminder_time. Calculate that from the completion time + cadence  
   - Reminder tool quirk worth remembering: the `cronjob` tool does not accept update strings like `every 3 days at 8:00 PM`. For every-N-days reminder changes, verify the resulting `next_run_at` after updating, and prefer explicit cron syntax or a one-off snooze plus follow-up re-establish flow instead of assuming natural-language parsing will work.
- **"Snooze"** → If vague, pick a reasonable snooze duration (e.g. 1 day, 3 days, 1 week depending on the task context), update the cron job, append the reminder that just fired to `last_5_reminder_times` (newest first, max 5), then explicitly tell Guru when they'll get the next reminder. Offer alternatives like "I'll remind you again Friday. Want a different day?"
- **"Remind me [day/time]"** → calculate the date, update cron to that day at 9:00 AM, confirm the new time
- **"Re-establish" / "set that up again" / "bring that reminder back"** → FIRST search existing task files, people/project notes, active cron jobs, and session history to recover the previous setup before creating anything new. Reuse the prior cadence/checklist/linking style when recoverable; only ask a follow-up if the old schedule is genuinely unclear.

## When the user corrects reminder details

If Guru corrects a captured reminder detail after creation — for example a voice transcription mistake, who the people are, or who the output should go to — update the existing reminder rather than creating a duplicate:
- Edit/rename the existing task file if the title slug is now wrong, preserving the original changelog/audit trail.
- Update the cron job schedule, name, and prompt to point at the new task path and corrected task summary.
- Sync task frontmatter fields (`updated`, `tags`, `area`, `cron_id`, `next_reminder_time`, `last_5_reminder_times`) after the cron update.
- Update linked person/project/context notes to reflect the corrected facts and remove stale open questions.
- Search the vault for stale wrong terms/old slugs and clean them up where they represent the same task, while leaving changelog entries as historical context when useful.
- If the corrected detail is durable (e.g. people are work colleagues), save it to memory compactly.

## When the task scope changes

Sometimes the user is not really saying "done" or "snooze" — they are saying the old task is now the wrong shape.

Common pattern: a very specific recurring task ("set up X") becomes unnecessary because the broad constraint is solved (for example a general default day/time now exists), but the user still wants a lighter recurring nudge around the broader topic.

When that happens:
- Close the old specific task instead of letting it limp along forever.
- Remove its recurring cron rather than silently reusing it for a different purpose.
- Create a new canonical task whose title and description match the broader ongoing need.
- Pick a sensible default reminder time if the user gave only a window like "Sunday afternoons/evenings"; record that reasoning in the note.
- Update linked project/person/note references so they point at the new canonical task, while keeping the old task as an audit-trail note with a pointer to the replacement.
- Update `user-context.md` to mention the newly canonical task, not the retired one.

This keeps the vault clean and prevents old task names from lying about what the reminder is actually for.## When the task scope changes

Sometimes the user is not really saying "done" or "snooze" — they are saying the old task is now the wrong shape.

Common pattern: a very specific recurring task ("set up X") becomes unnecessary because the broad constraint is solved (for example a general default day/time now exists), but the user still wants a lighter recurring nudge around the broader topic.

When that happens:
- Close the old specific task instead of letting it limp along forever.
- Remove its recurring cron rather than silently reusing it for a different purpose.
- Create a new canonical task whose title and description match the broader ongoing need.
- Pick a sensible default reminder time if the user gave only a window like "Sunday afternoons/evenings"; record that reasoning in the note.
- Update linked project/person/note references so they point at the new canonical task, while keeping the old task as an audit-trail note with a pointer to the replacement.
- Update `user-context.md` to mention the newly canonical task, not the retired one.

This keeps the vault clean and prevents old task names from lying about what the reminder is actually for.

## Cron Prompt Template

When creating the cron prompt, include this template verbatim — fill in the bracketed placeholders with actual values:

```
Read the task file at [FILE_PATH]. Send the user a warm, friendly reminder about [TASK_SUMMARY].
Keep it short and casual. Don't be robotic about it — talk like a friend, not a notification bot.
Base the reminder on the task file so it reflects the current state.

Before finalizing your response, update the same task note so reminder metadata stays in sync:
- record that this reminder fired or was sent
- keep `cron_id`, `next_reminder_time`, and `last_5_reminder_times` synced with the current cron state
- prepend the fired reminder timestamp into `last_5_reminder_times` newest-first and cap it at 5 entries
- add a changelog entry noting the reminder was sent and the metadata was synced
- preserve `last_5_reminder_times: never sent` only for tasks that truly have no reminder history yet; if older history cannot be reconstructed confidently, use `unknown` only for those older unrecoverable entries rather than for the newly fired reminder

CRITICAL OUTPUT RULES:
- Your final output must end with exactly this sentence, character-for-character except for normal URL rendering:
Task created via life-os skill. Refer to [<note-name>](<note-link>).
- Build `<note-link>` by preferring `${LIFEOS_EXTERNAL_BASE_URL}/note/<slug>` when `LIFEOS_EXTERNAL_BASE_URL` is available to the run. Otherwise fall back to `https://ilangurudev.github.io/obsidian-links/?file=<folder>/<file>.md`.
- Do not rename that line to "Task note" or any other label.
- Do not add an "Open questions" section.
- Do not add any text after that exact final sentence.
- Put that exact sentence on its own final line.
```

When building a cron prompt for any reminder that depends on context beyond the task note itself, explicitly include every required file path in the prompt and instruct the cron run to read them at runtime (for example `user-context.md`, a linked checklist note, or a source note like a quotes log). Cron runs are fresh sessions and will not reliably infer extra sources unless told.

Tone: conversational, not robotic. "Lavanya's birthday is in 2 weeks. Thought about what to get her?" not "REMINDER: Birthday upcoming."

## Response Overrides for Cron Reminders

These override the normal life-os response formatting when the message is a cron reminder:
- Do not relabel the note link as `Task note:` or any paraphrase when the cron prompt provides an exact sentence to append. Use the exact sentence from the cron prompt verbatim.
- Do not add an `Open questions:` footer to cron reminders unless the cron prompt explicitly asks for it.


