Load this when editing, revising, restructuring, deduplicating, or cleaning up existing Life OS notes across `~/my-data`.

# Note Editing Checklist

Follow this checklist in order. This file is for editing existing Life OS notes across `people`, `projects`, `tasks`, `goals`, `notes`, `check-ins`, `journal`, and `user-context.md`.

## 1. Ground yourself first

- Read `~/my-data/user-context.md` before touching anything.
- Use it to identify likely canonical notes, active projects, related people, and recent context.
- If the edit involves reminders, snoozes, crons, or task reminder mechanics, also load [reminders.md](./reminders.md). If birthdays are involved, also load [birthday-reminders.md](./birthday-reminders.md).
- If the request is a broad vault-wide cleanup or canonicalization pass, also load [cleanup-guidance.md](./cleanup-guidance.md).

## 2. Find the canonical note set

- Search before editing. Do not assume the file named in the prompt is the only note involved.
- Gather the canonical note plus any overlapping notes that may need tightening, linking, archiving, or redirecting.
- If the user refers to a placeholder or informal name, find the existing canonical file before creating anything new.
- If no canonical note exists, create or rename only after checking the relevant directory and template.

## 3. Decide what belongs where

- Keep durable identity facts, relationships, and high-level context in entity notes.
- Keep actionable instructions, checklists, next steps, and reminder mechanics in task notes.
- Keep project coordination, status, goals, and links in project notes.
- Keep freeform knowledge, observations, and reference material in general notes.
- Do not duplicate operational detail across multiple notes. Prefer lightweight summaries and wikilinks that point to the canonical owner.

## 4. Edit the note cleanly

- Preserve the existing `_template.md` frontmatter schema for that directory. Do not invent new frontmatter fields.
- Keep the most current, useful content near the top of the note body.
- Use exact kebab-case filenames and exact wikilinks that match the filename stem.
- If a placeholder note should be renamed, rename it immediately and update all affected wikilinks and `user-context.md` references.
- When consolidating content, move the live material to the canonical note instead of maintaining parallel copies.

## 5. Handle dedupe and non-canonical notes

- Deduplicate both file-level and body-level content aggressively.
- If another note stops being canonical, prefer converting it into a tiny archived pointer note instead of deleting it.
- Archived pointer notes should stay minimal: mark them archived, add one short handoff sentence to the canonical note, keep only lightweight related links, and preserve the changelog so the audit trail still makes sense.
- Hard delete only when the file is pure noise and preserving it adds no historical value.
- After cross-note dedupe, re-read the tightened note and verify it no longer contains operational specifics that belong elsewhere.

## 6. Maintain history correctly

- For regular Life OS notes, update or append a `## Changelog` section with newest entries first.
- Keep the changelog at the bottom of the file.
- Do not add a separate `## Changelog` to `~/my-data/user-context.md`.

## 7. Refresh `user-context.md`

- After editing source notes, update `~/my-data/user-context.md` so it reflects the current state of the vault.
- Keep it concise, newest first, and linked to the actual canonical notes.
- Remove stale items older than 10 days and keep `Current Context` trimmed to the top 25 items.

## 8. Verify before finishing

- Re-open the edited note set and check that canonical ownership is clear.
- Confirm wikilinks resolve to the right kebab-case filenames.
- Confirm frontmatter stayed within template rules.
- Confirm reminder logic was not embedded into general notes when it belongs in task/reminder workflows.
- Confirm there is no stale duplicate content left behind in superseded notes.

## 9. Respond clearly

- Tell the user what you edited, what you consolidated, and any proactive cleanup you performed.
- Include clickable Obsidian links in the standard format from `SKILL.md`.
- End with any open questions that were captured in the notes, phrased naturally. If there are none, do not mention open questions.
