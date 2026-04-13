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

## Reminder Metadata Sync

For task reminders, keep these fields synced in the task frontmatter:
- `cron_id` — the cron job ID (or comma-separated list if multiple crons)
- `next_reminder_time` — the earliest upcoming scheduled run when a cron exists; if no reminder is currently scheduled, explicitly write `next_reminder_time: none scheduled` instead of leaving it blank
- `last_5_reminder_times` — a comma-separated newest-first history of the most recent reminder times that actually fired or were sent, capped at 5 entries. For a brand-new task with no reminder history, explicitly write `last_5_reminder_times: never sent`. If prior history for an older task cannot be reconstructed confidently, write `last_5_reminder_times: unknown`.

## Reminder Timing Rules

- If the user changes the timing of a recurring reminder (e.g. they confirm a preferred time and ask to move the next one), update the recurring cron itself so future runs stay aligned to that new time preference, not just the next occurrence.
- When the user asks for a specific reminder time, use that exact time. Only fall back to a reasonable default like 9:00 AM when they gave a vague time window such as "tomorrow morning," and say what default you picked.
- For recurring reminders created via `cronjob`, prefer explicit cron syntax (e.g. `0 16 * * 0`) over natural-language schedules if precision matters. Verify the created job's `next_run_at`, then sync that exact next occurrence back into the task frontmatter `next_reminder_time` and `cron_id`, while leaving `last_5_reminder_times` at `never sent` for a brand-new reminder or preserving the existing history for an updated reminder.

## Snooze / Cancel / Re-establish

- **"Snooze"** → pick a reasonable snooze duration (e.g. 1 day, 3 days, 1 week depending on the task context), update the cron job, append the reminder that just fired to `last_5_reminder_times` (newest first, max 5), then explicitly tell Guru when they'll get the next reminder. Offer alternatives like "I'll remind you again Friday. Want a different day?"
- **"Remind me [day/time]"** → calculate the date, update cron to that day at 9:00 AM, confirm the new time
- **"Done" or "already handled"** → remove the cron job via cron(action='remove')
- **"Re-establish" / "set that up again" / "bring that reminder back"** → FIRST search existing task files, people/project notes, active cron jobs, and session history to recover the previous setup before creating anything new. Reuse the prior cadence/checklist/linking style when recoverable; only ask a follow-up if the old schedule is genuinely unclear.
- Always keep `cron_id`, `next_reminder_time`, and `last_5_reminder_times` in sync in the task file after any of these operations.

## Telegram Reply Targeting

When Guru replies to a cron reminder on Telegram, the quoted snippet identifies which task/reminder they're responding to. Use that to find and edit the correct task file:

- **"Done"** → update status to `done`, remove cron job, preserve/update `last_5_reminder_times` if the reminder just fired
- **"Snooze"** → set `snoozed_until`, update cron schedule, append the just-fired reminder time to `last_5_reminder_times`, trim to newest 5, sync `next_reminder_time`
- **"Remind me Thursday"** → calculate date, update task `due_date` and cron, keep `last_5_reminder_times` as-is, sync `next_reminder_time`
- **"Already handled"** → mark done, remove cron job, preserve/update `last_5_reminder_times`
- **Any other reply** → treat as a note/update on that specific task file, append timestamped content, and sync `last_5_reminder_times` if the reply is clearly to a reminder that just went out

Tone: conversational, not robotic. "Lavanya's birthday is in 2 weeks. Thought about what to get her?" not "REMINDER: Birthday upcoming."

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

## Response Overrides for Cron Reminders

These override the normal life-os response formatting when the message is a cron reminder:
- Do not relabel the note link as `Task note:` or any paraphrase when the cron prompt provides an exact sentence to append. Use the exact sentence from the cron prompt verbatim.
- Do not add an `Open questions:` footer to cron reminders unless the cron prompt explicitly asks for it.

Don't forget to update `~/my-data/user-context.md` after creating or modifying any cron!
