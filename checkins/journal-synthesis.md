# Journal Synthesis from Check-ins

Load this file when Guru wants a daily journal entry drafted, autocompleted, updated, or synthesized from morning/night check-ins.

Journal synthesis is the narrative memory layer: it turns raw check-in material into a readable daily journal entry while preserving Guru's actual words where they matter.

## Purpose

The journal entry should:

- Use both morning and night check-ins when available.
- Preserve meaningful verbatims.
- Convert raw material into a readable first-person memory.
- Capture what happened, how it felt, who mattered, and what remains open.
- Avoid inventing events, emotions, people, or significance.
- Avoid sterile productivity summaries and fake profundity.

The journal is not a transcript. It is also not a LinkedIn post wearing pajamas. It should sound like something future Guru would actually want to read.

## Prerequisite Lookup

Before drafting or updating the journal:

1. Read `~/my-data/user-context.md`.
2. Read today's morning check-in if it exists.
3. Read today's night check-in if it exists.
4. Read today's existing journal entry if it exists.
5. Read relevant people/project/task notes only if needed for correct wikilinks or context.

If one check-in is missing, continue with what exists. Do not make up missing context.

## Scheduled Night Check-in Handoff

The recurring night check-in task is:

```text
~/my-data/tasks/run-night-checkin.md -> 7:30 PM America/New_York
```

The scheduled prompt should not pre-create the journal. It should gather the retrospective and explicitly invite raw journal verbatims. After Guru replies, use this file to synthesize/update the day's journal from:

- today's morning check-in
- Guru's night check-in response
- today's existing journal, if present
- yesterday's check-ins/journal only where they add real continuity
- `user-context.md` for active people/projects/tasks

The goal is a personalized retrospective, not a generic journaling worksheet.

## Journal File Naming

Use one journal note per day:

```text
~/my-data/journal/YYYY-MM-DD.md
```

Use the exact daily date in Guru's local timezone unless the user explicitly says otherwise.

When linking source check-ins, use exact filename stems:

```md
## Source Check-ins
- [[YYYY-MM-DD-morning]]
- [[YYYY-MM-DD-night]]
```

If only one check-in exists, link only that one. Do not create fake source links.

## Source Priority

Use sources in this order:

1. User's latest direct instruction in the conversation.
2. Night check-in raw verbatim.
3. Morning check-in raw verbatim.
4. Structured fields from night check-in.
5. Structured fields from morning check-in.
6. Existing journal entry, if updating.
7. Related notes for names/projects/tasks, only for grounding and wikilinks.

Raw user language beats extracted structure unless the structure clearly resolves ambiguity.

## What to Extract

From the check-ins, extract:

- Concrete events.
- Emotional baseline and emotional arc.
- Morning intention and whether it survived the day.
- Success conditions and what happened to them.
- Main risks/friction and whether they materialized.
- People, conversations, and follow-ups.
- Wins and regrets.
- Open loops and tomorrow seeds.
- Exact phrases worth preserving.

## Suggested Journal Shape

Use the current `journal/_template.md` frontmatter when creating actual vault notes. In the body, prefer:

```md
# Journal — YYYY-MM-DD

## Summary
A short paragraph capturing the day.

## What Happened
Concrete events, decisions, conversations, work, travel, family, logistics.

## How I Felt
The emotional arc of the day, based on morning and night check-ins.

## What Mattered
The meaning / center of gravity of the day. Not necessarily the most productive thing.

## People
Notable people and interactions, wikilinked where appropriate.

## Open Loops
Tasks, decisions, follow-ups, unresolved thoughts.

## Verbatims
> Preserved lines from the user's check-ins.

## Reflection
A polished-but-still-Guru paragraph that future Guru would actually want to read.

## Source Check-ins
- [[YYYY-MM-DD-morning]]
- [[YYYY-MM-DD-night]]
```

Omit sections that would be empty or fake. A boring Tuesday does not need to become a memoir wearing a tiny hat.

## Synthesis Rules

- Do not invent events, emotions, people, or conclusions.
- If uncertain, mark uncertainty instead of presenting it as fact.
- Preserve 2–5 meaningful verbatim quotes when available.
- Keep the journal first-person unless Guru explicitly wants otherwise.
- Use Guru's phrasing when it has texture.
- Avoid therapy-speak unless Guru used it.
- Avoid productivity theater.
- Avoid fake profundity: no "journey," no "emotional landscape," no LinkedIn monk nonsense.
- Do not over-index on tasks; record the actual day.
- Prefer specific sensory/emotional texture over generic summary.
- Keep unresolved things unresolved. Do not force closure.

## Tone

The journal should be:

- Calm.
- Specific.
- First-person.
- Lightly reflective.
- Honest without being melodramatic.
- Polished enough to read later, raw enough to feel true.

Bad:

> Today was a day of growth and challenge. I navigated priorities while reflecting on my emotional landscape.

Good:

> Today had that slightly scattered feeling where I was moving, but not always toward the thing I said mattered. I did get the important pieces unstuck, though, and the conversation with Matt clarified more than I expected.

## Verbatim Handling

Treat verbatims as durable raw material.

There are three layers:

### 1. Raw Verbatim

Guru's actual words. Preserve exactly when emotionally or factually important.

> "I feel like I'm doing the classic thing where I know what matters and still orbit the dumb little tasks."

### 2. Structured Extraction

What the system understands:

- Avoiding high-leverage work.
- Feeling scattered.
- Needs prioritization/protection.
- Possible tomorrow seed: identify one high-leverage block.

### 3. Journal Synthesis

Readable memory in Guru's voice:

> I noticed myself circling the smaller tasks even though I knew what mattered. The day had that annoying texture where motion was easy and priority was not.

Do not collapse all rawness into bland summary. The whole point is to preserve texture.

## Existing Journal Rule

If the daily journal already exists:

- Do not overwrite human-written content blindly.
- Merge generated content carefully.
- Preserve existing sections unless clearly stale or duplicated.
- Prefer appending or updating a generated section over replacing the whole note.
- Keep source check-in links.
- If replacing generated text, preserve prior human edits and unique verbatims.

## Linking Rules

- Link source check-ins in `## Source Check-ins`.
- Wikilink people, projects, and tasks where canonical notes exist.
- Do not create new people/project/task notes during synthesis unless the user clearly provided durable information and the main life-os capture rules indicate it should be recorded.
- Keep wikilinks exact and kebab-case.

## Tomorrow Handoff

At the end of synthesis, extract tomorrow seeds from the night check-in:

- Carryover priority.
- First action.
- Emotional residue.
- People to follow up with.
- Thing to avoid repeating.

These should feed the next morning check-in, not bloat the journal.

Example next-morning opener:

> Last night left these open loops: X, Y, Z. Still true, or did sleep delete half this list like nature intended?

## Final Response After Journal Update

When responding to Guru after creating/updating a journal entry:

- Link the journal note.
- Mention source check-ins used.
- Mention any carryovers/tomorrow seeds extracted.
- Keep it short.

Do not paste the whole journal unless Guru asks.