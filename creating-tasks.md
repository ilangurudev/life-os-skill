##  Task-to-Cron Flow (STRICT ORDER):
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

See also: [Birthday Reminder Guidance](./birthday-reminder-guidance.md)

## Snooze/Cancellation/Re-establish Flow:
- "Snooze" → pick a reasonable snooze duration (e.g. 1 day, 3 days, 1 week depending on the task context), update the cron job, append the reminder that just fired to `last_5_reminder_times` (newest first, max 5), then explicitly tell Guru when they'll get the next reminder. Offer alternatives like "I'll remind you again Friday. Want a different day?"
- When user says "remind me [day/time]" → calculate the date, update cron to that day at 9:00 AM, confirm the new time
- When user says "done" or "already handled" → remove the cron job via cron(action='remove')
- When user says "re-establish", "set that up again", "bring that reminder back", or anything similar → FIRST search existing task files, people/project notes, active cron jobs, and session history to recover the previous setup before creating anything new. Reuse the prior cadence/checklist/linking style when it is recoverable; only ask a follow-up if the old schedule is genuinely unclear.
- Always keep `cron_id` in sync in the task file.
- Keep both reminder timestamps synced too: `next_reminder_time` for the next scheduled reminder, `last_5_reminder_times` for a comma-separated newest-first history of the most recent reminder times that actually fired/sent.
- After all of these updates, update the task files to note the changes of course. (especially `next_reminder_time` and `last_5_reminder_times`.)

## Telegram Reply Targeting:
When Guru replies to a cron reminder on Telegram, the quoted snippet identifies which task/reminder they're responding to. Use that to find and edit the correct task file:
- "Done" → update status to `done`, remove cron job, and preserve/update `last_5_reminder_times` if the reminder just fired
- "Snooze" → set `snoozed_until`, update cron schedule, append the just-fired reminder time to `last_5_reminder_times`, trim to the newest 5 entries, and sync the new `next_reminder_time`
- "Remind me Thursday" → calculate date, update task `due_date` and cron, keep `last_5_reminder_times` as-is, sync `next_reminder_time`
- "Already handled" → mark done, remove cron job, and preserve/update `last_5_reminder_times` if the reminder just fired
- Any other reply → treat as a note/update on that specific task file, append timestamped content, and if the reply is clearly to a reminder that just went out, sync `last_5_reminder_times` accordingly

Tone: conversational, not robotic. "Lavanya's birthday is in 2 weeks. Thought about what to get her?" not "REMINDER: Birthday upcoming."

## Cron Prompt Template (embed this directly in every cron job prompt):

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

Don't forget to update the user_context.md file after creating the cron!


