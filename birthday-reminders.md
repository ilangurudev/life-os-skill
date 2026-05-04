# Birthday Reminder Guidance

Load this file when setting up or modifying birthday reminders specifically. For general reminder/cron mechanics, see [reminders.md](./reminders.md).

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
- For batch birthday captures, use known relationships in the same batch as context. If several names are known colleagues/work stakeholders and new names are grouped with them, default unknown people in that batch to colleague cadence, but record the inference and an open question so Guru can upgrade them later if they are close friends/family.
- If the user gives an explicit cadence, that overrides these defaults.

## Task / Note Recording

When you set birthday reminders:
- Store the birthday date in the person note.
- Record the chosen cadence in the person note and/or task note.
- For advance reminders, include a gentle action nudge when appropriate.

## Cron Guidance

- Always create or update the backing task file first.
- Then create one recurring yearly cron job per reminder touchpoint.
- Save all relevant cron job IDs back into the task note's `cron_id` field as a comma-separated list if needed.
- Update `~/my-data/user-context.md` after changes.

## Relationship Corrections / Cadence Changes

When Guru later clarifies that a person's relationship category was wrong, adjust the existing setup instead of recreating everything:

- Update the person note and birthday task first, including the relationship, tags, cadence explanation, reasoning, open questions, and changelog.
- Preserve any still-valid existing cron jobs, especially day-of birthday reminders, rather than deleting/recreating them.
- Add only the missing reminder touchpoints when upgrading cadence, e.g. colleague → close friend adds 1-week and day-before jobs while keeping day-of.
- Remove only the no-longer-valid extra touchpoints when downgrading cadence, e.g. close friend → colleague removes advance jobs while keeping day-of.
- After cron changes, resync the task metadata: `cron_id`, `next_reminder_time`, `last_5_reminder_times`, and changelog.
- Update `~/my-data/user-context.md` with the corrected relationship/cadence and cron IDs.
- If the correction is durable relationship knowledge likely to matter outside the vault, update long-term memory too.
