# Check-ins

Load this file when Guru wants to run, create, modify, or reason about check-ins — especially morning or night check-ins.

Check-ins are the structured intake layer for Life OS: conversational enough to preserve rawness, structured enough to prevent speaker's block.

## Purpose

Check-ins should:

- Capture Guru's real words and state, not sterile productivity-app mush.
- Add lightweight structure so speaking is easy.
- Create continuity between morning intention, night reflection, and journal memory.
- Surface tasks, people, projects, risks, emotions, and open loops without over-interrogating.
- Work even when Guru is tired, terse, scattered, or not in the mood.

## Core Principle

Rawness first, structure second.

Let Guru speak naturally. Preserve verbatims. Then extract structure after the fact.

Do not force every check-in into a complete form. Missing fields are fine. A check-in that actually happens beats a beautiful template that gets avoided.

## Check-in Types

### Morning Check-in

The morning check-in is prospective: it captures the day's starting state and creates anchors for the night check-in.

It should answer:

- How is Guru starting the day?
- What would make today successful?
- What needs protection?
- What could derail the day?
- Who or what needs attention?
- What intention/theme should the day carry?

### Night Check-in

The night check-in is retrospective: it captures what actually happened and hands material to the journal synthesis flow.

It should answer:

- What happened today?
- What mattered?
- What changed from the morning plan?
- How did the emotional arc evolve?
- What people/interactions matter?
- What should tomorrow inherit?
- What raw lines should be preserved in the journal?

For journal drafting rules, load [journal-synthesis.md](./journal-synthesis.md).

## Morning Default Flow

Ask compactly:

1. How did you sleep, and what's your energy like?
2. What's the vibe mentally / physically?
3. What are the 1–3 things that would make today successful?
4. Anything you're anxious about, avoiding, or likely to get derailed by?
5. Any people, conversations, or follow-ups I should keep on the radar?
6. Give me one sentence for the day — intention, warning label, theme, whatever.

Do not ask all six if the user already gives a dump. Extract first, ask only what's missing.

## Night Default Flow

Before running a night check-in:

1. Read `~/my-data/user-context.md`.
2. Search for today's morning check-in in `~/my-data/check-ins/`.
3. Search for today's journal entry in `~/my-data/journal/`.
4. Read relevant people/project/task notes only if the check-in mentions them or user-context says they are active.

If a morning check-in exists, start by naming the anchors:

> Night check-in. This morning you had: [success condition], [main risk], [intention]. What actually happened?

Then ask:

1. What happened today?
2. What actually mattered?
3. What changed from the morning plan?
4. What felt good, bad, unresolved, or weird?
5. Any meaningful people / conversations / follow-ups?
6. What should tomorrow inherit?
7. Dump raw thoughts for the journal — messy is fine.

If no morning check-in exists, continue anyway. Do not make the user feel like they failed the system. The system works for Guru, not the other way around.

## Modes

### Quick Mode

Use when Guru seems busy, terse, tired, or just wants the ritual done.

Morning prompt:

> Morning check-in. Quick version: sleep/energy, top 1–3, main risk, any people, one-line intention.

Night prompt:

> Night check-in, quick version: what happened, what mattered, current mood, carryovers, raw journal dump.

### Full Mode

Use when the day seems important, emotionally loaded, travel-heavy, meeting-heavy, decision-heavy, or Guru explicitly wants depth.

Optional morning prompts:

- What would make today feel clean?
- What should be protected?
- What can be intentionally ignored?
- What are you pretending is not a problem?
- Who needs attention?
- What would future-you be annoyed you skipped?

Optional night prompts:

- What was the actual center of gravity of the day?
- What are you still carrying?
- What surprised you?
- What did you avoid?
- What was better than expected?
- What should tomorrow not inherit?
- Is there a sentence from today worth preserving exactly?

### Recovery Mode

Use when Guru is fried, overwhelmed, sick, sad, underslept, or resistant.

Morning prompt:

> Bare minimum version: three words for how you feel, one must-do, one thing to avoid. That's it.

Night prompt:

> Give me one messy paragraph. I'll extract the recap, mood, carryovers, and journal. No ceremony.

## Scheduled Automation

Daily check-in prompts are backed by recurring task notes and cron jobs:

```text
~/my-data/tasks/run-morning-checkin.md  -> 7:30 AM America/New_York
~/my-data/tasks/run-night-checkin.md    -> 7:30 PM America/New_York
```

These are infrastructure/agent tasks. They should be pinned under the dashboard Agent Notes lens and skipped by the nightly reminder safety-net worker.

Morning cron personalization:
- read `user-context.md`
- read yesterday's morning/night check-ins if present
- read yesterday's journal if present
- carry forward only 1-3 useful threads so the prompt feels continuous but light

Night cron personalization:
- read today's morning check-in first
- read today's existing journal if present
- read yesterday's check-ins/journal for continuity
- use `user-context.md` for active people/projects/tasks
- mention only 2-4 relevant context points so it feels personal, not creepy

The scheduled prompt should ask for the check-in, not pre-create notes before Guru responds.

## File Naming

Use one check-in note per session:

```text
~/my-data/check-ins/YYYY-MM-DD-morning.md
~/my-data/check-ins/YYYY-MM-DD-night.md
```

Use the exact daily date in Guru's local timezone unless the user explicitly says otherwise.

Wikilinks should use exact filename stems:

```md
[[YYYY-MM-DD-morning]]
[[YYYY-MM-DD-night]]
```

Do not invent cute names like `daily-vibes-final-final.md`. We are building a system, not opening a Canva account.

## Morning Note Shape

Use the current `check-ins/_template.md` frontmatter when creating actual vault notes. In the body, prefer:

```md
# Morning Check-in — YYYY-MM-DD

## Raw Verbatim
> User's morning words, preserved as much as possible.

## Snapshot
- Sleep:
- Energy:
- Mood:
- Body:
- Emotional baseline:

## Today's Shape
- Fixed commitments:
- Flexible blocks:
- Constraints:
- Things I am avoiding:

## Success Conditions
1.
2.
3.

## If Nothing Else
-

## People
- On my mind:
- Follow-ups:
- Conversations:

## Risks / Friction
- Could derail today:
- Anxiety / dread:
- Needs protection:

## Intention
> "..."

## Anchors for Tonight
- Success condition:
- Main risk:
- Protected priority:
- Person / relationship focus:
- Intention:
```

## Night Note Shape

Use the current `check-ins/_template.md` frontmatter when creating actual vault notes. In the body, prefer:

```md
# Night Check-in — YYYY-MM-DD

## Raw Verbatim
> User's nighttime words, preserved as much as possible.

## Morning Anchor Review
- Morning success condition:
- What happened:
- Morning risk:
- Did it materialize:
- Morning intention:
- Did it survive contact with reality:

## Day Recap
- What happened:
- What changed from the plan:
- What got done:
- What did not get done:

## Emotional Arc
- Morning state:
- Midday state:
- Night state:
- Main emotional events:

## Wins
-

## Friction / Regrets
-

## People
- Meaningful interactions:
- Follow-ups needed:
- People to remember:

## Body / Health
- Energy:
- Food:
- Movement:
- Sleep setup:

## Carryovers
- Tasks:
- Thoughts:
- Decisions:

## Tomorrow Seeds
- First action:
- Carryover priority:
- Emotional residue:
- People:
- Avoid repeating:

## Journal Draft Notes
- Include:
- Avoid:
- Tone:
```

## Anchor Rules

Morning anchors are the handoff to the night check-in. Keep them crisp and reviewable.

Good anchors:

- "Protect 90 minutes for the Audit memo."
- "Don't let Slack turn into the whole day."
- "Text Matt back."
- "Move cleanly, don't thrash."

Bad anchors:

- "Be better."
- "Have a productive day."
- "Optimize my wellness." Gross. No.

## After Capture

After Guru answers, summarize briefly:

```md
Today's shape:
- Energy:
- Win condition:
- Risk:
- People:
- Intention:
```

For night:

```md
Tonight's read:
- What happened:
- What mattered:
- Mood:
- Carryovers:
- Journal material:
```

Then, if appropriate, suggest one practical adjustment: protect a block, lower scope, move a follow-up, or make a tomorrow seed.

If the check-in changes the real state of a task or reminder — progress made, task no longer needing a safety-net nag, carryover becoming firm, etc. — load `reminders.md` and sync the relevant task/reminder metadata before replying. A check-in is often the moment task state becomes true; don't leave stale reminder goblins alive just because the user didn't explicitly say "cancel reminder."

Keep it short. Check-ins are not depositions.