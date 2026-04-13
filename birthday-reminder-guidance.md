# Birthday Reminder Guidance

Use relationship-aware default reminder cadences for birthdays unless the user explicitly overrides them.

## Default Cadences

### Family / Partner
- 2 weeks before at 9:00 AM
- 1 week before at 9:00 AM
- Day before at 9:00 AM
- Day of at 9:00 AM

Advance reminders should be action-oriented, e.g. prompt Guru to think about a gift, plan, reservation, card, flowers, or call.

### Close Friends
- 1 week before at 9:00 AM
- Day before at 9:00 AM
- Day of at 9:00 AM

Advance reminders should be action-oriented, e.g. prompt Guru to plan a gift, text, dinner, hang, or call.

### Colleagues
- Day of at 9:00 AM only

Keep it lightweight and socially useful.

## Interpretation Rules

- Treat phrases like "best friend", "close friend", "one of my closest friends", and similar language as Close Friends.
- Treat spouse/partner, immediate family, in-laws, and equivalent language as Family / Partner unless the user says otherwise.
- Treat family pets / core household pets as Family / Partner cadence by default unless the user wants something lighter.
- Treat the user's own birthday as Family / Partner cadence by default, but make the advance reminders planning-oriented rather than gift-oriented.
- Treat coworker, colleague, teammate, manager, direct report, and similar workplace relationships as Colleagues.
- If the birth year is unknown but month/day is known, still create the birthday reminder setup using the known month/day and keep the missing birth year as an open question in the note.
- If the relationship is ambiguous, make a reasonable default based on what the user said and record it in the note.
- If the user gives an explicit cadence, that overrides these defaults.

## Task / Note Recording

When you set birthday reminders:
- Store the birthday date in the person note.
- Record the chosen cadence in the person note and/or task note.
- Keep reminder language warm and natural.
- For advance reminders, include a gentle action nudge when appropriate.

## Cron Guidance

- Always create or update the backing task file first.
- Then create one recurring yearly cron job per reminder touchpoint.
- Save all relevant cron job IDs back into the task note's `cron_id` field as a comma-separated list if needed.
- Update `~/my-data/user-context.md` after changes.
