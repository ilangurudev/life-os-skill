# Structured Query Guidance

Load this file whenever the user asks to retrieve, list, filter, or summarize Life OS content in a schema-aware way — especially requests like "all tasks due this week", "upcoming birthdays", "notes about X", "what's stale", or "show me active project tasks".

## Core Query Rule

Do not start with a broad vault-wide search unless the request is genuinely unstructured.

Use this order:
1. Route to the right folder(s)
2. Filter by frontmatter first
3. Only then do body-text search on the narrowed candidate set if the request is about topic/content
4. Load full note bodies only for finalists
5. Sort, summarize, and answer

This keeps retrieval fast, cheap, and predictable.

## Route by Folder First

Treat the vault folders like typed tables:

- `tasks/` = actions, reminders, due items, recurring obligations
- `people/` = identity and relationship context
- `notes/` = ideas, concepts, observations, freeform knowledge
- `projects/` = coordination and execution context
- `goals/` = higher-level outcomes
- `check-ins/` and `daily-logs/` = reflective/history records

Default routing rules:
- If the request is about actions, reminders, due windows, or operational status -> query `tasks/` first
- If the request is about a person or entity -> query `people/` first
- If the request is about a topic, idea, or concept -> query `notes/` first
- If the request spans multiple layers, start with the canonical operational owner and only expand if needed

## Frontmatter First

Before reading note bodies, use the schema fields that already exist.

Common useful task filters:
- `status`
- `recurrence`
- `due_date`
- `priority`
- `area`
- `project`
- `goal`
- `tags`
- `next_reminder_time`

Common useful note filters:
- `status`
- `tags`
- `updated`

If the user asks for `active`, `current`, `upcoming`, `overdue`, `stale`, `open`, `unfinished`, or similar operational states, frontmatter filtering should do most of the work.

## Text Search Is Second Pass

If the user asks whether notes "talk about", "mention", or "reference" a topic:
- first constrain by folder and status
- then run text search only on the survivors
- search both title/body and tags when relevant

Example:
"all unarchived notes that talk about life-os"
- folder: `notes/`
- filter: `status != archived`
- then search for `life-os`, `life os`, `lifeos`

## Birthday / Event Queries

For operational birthday questions like:
- who has birthdays in the next 2 weeks?
- what birthday reminders are coming up?

Query `tasks/` first, not `people/`.

Why:
- the person note owns identity/context
- the task note owns operational reminder state
- birthday reminder tasks already carry fields like recurrence, due date, tags, cron state, and next reminder timing

Use people notes only to enrich the final answer if needed.

## Named Recipe Pattern

When a structured query is likely to recur, mentally map it to a stable recipe instead of inventing a fresh search strategy each time.

Each recipe should define:
- target folder(s)
- frontmatter filters
- optional text search
- sort order
- optional limit
- render style

Good starter recipes:
- `open_tasks_due_this_week`
- `upcoming_birthdays_2_weeks`
- `notes_about_life_os`
- `overdue_tasks`
- `active_project_tasks`
- `stale_notes`
- `people_to_follow_up_with`

## Example Recipes

### open_tasks_due_this_week
- folders: `tasks/`
- filters:
  - `status in [todo, in-progress, delayed, blocked]`
  - `area = personal`
  - `due_date between today and end_of_week`
- sort:
  - `due_date asc`
  - `priority asc`

### upcoming_birthdays_2_weeks
- folders: `tasks/`
- filters:
  - `tags contains birthday`
  - `status != done`
  - `due_date between today and today + 14 days`
- sort:
  - `due_date asc`

### notes_about_life_os
- folders: `notes/`
- filters:
  - `status != archived`
- text search:
  - title/body/tags contains any of `life-os`, `life os`, `lifeos`
- sort:
  - `updated desc`

## Query Hygiene

- Do not read every file body up front
- Do not do semantic or fuzzy vault-wide wandering when the schema can answer the question directly
- Prefer loading a few finalists over scanning the whole corpus
- If date fields are mixed in formatting, normalize them in the query logic before filtering
- If a request is repeated often, consider capturing it as a named recipe in the future

## Output Style

Return concise results with enough context to act:
- name/title
- key state fields (status, due date, area, reminder time, etc.)
- relevant wikilinks or Obsidian applet links when useful

The goal is not just retrieval but fast decision support.
