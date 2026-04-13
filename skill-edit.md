# Editing This Skill

Meta guidance for agents making changes to the life-os skill files themselves.

## Progressive Disclosure Model

The skill is structured around progressive disclosure — **do not collapse everything into SKILL.md**:

- **SKILL.md** is the always-loaded core. It should contain only rules that apply to ALL life-os interactions. Keep it scannable and under ~100 lines of content.
- **On-demand files** (reminders.md, birthday-reminders.md, cleanup-guidance.md, this file) are loaded only when the conversation triggers them. Each must be self-contained for its concern — an agent loading `reminders.md` should not also need to read SKILL.md rules to handle a reminder correctly.

## When adding a new rule

Ask: "Does this apply to every life-os interaction, or only a specific workflow?"
- If every interaction → add to the appropriate section in SKILL.md
- If specific workflow → add to (or create) the relevant on-demand file, and add a row to the routing table in SKILL.md

## When creating a new on-demand file

- Add a routing entry in the SKILL.md "On-Demand Guidance" table with a clear trigger description
- Make the file self-contained: include a one-line description of when to load it at the top
- Cross-link to related on-demand files where relevant (e.g. birthday-reminders.md links to reminders.md for general cron mechanics)

## Conventions

- Keep filenames in kebab-case, consistent with the vault naming convention
- Use relative links between skill files (e.g. `./reminders.md`)
- Group rules by concern, not by chronological addition — avoid flat numbered lists that grow unboundedly
- When a rule is moved between files, delete it from the source — do not leave behind stubs or "see X" redirects in numbered lists

## Tool Tips for Bulk Edits

- When editing small markdown notes, be careful with line-numbered file reads: do not paste line-number prefixes into patch replacements.
- For multi-section note cleanups, prefer rewriting the full note with `write_file` after verifying the current content, rather than stacking fragile partial patches.
- `read_file` may return a dedup placeholder like "File unchanged since last read..." instead of the literal file body in the same conversation. Never feed that placeholder into `write_file`. Use the earlier content already in context or fully reconstruct the note body.
