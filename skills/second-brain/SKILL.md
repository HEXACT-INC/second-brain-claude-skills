---
name: second-brain
description: Query and update the user's local Second Brain personal knowledge base through the personal-kb MCP tools (kb_schema, kb_read_data, kb_write_data). Use for stored data (contacts, notes, billing, language vocabulary and phrase tracking, anything in the KB). Always discover the current schema before issuing queries; never assume table or column names.
---

# Second Brain skill

This skill gives you read and controlled-write access to the user's local
Second Brain SQLite knowledge base via the ``personal-kb`` MCP server entry
they configured in Claude (same key as in ``claude_desktop_config.json`` under
``mcpServers``). The user runs Second Brain locally; Claude spawns that server
on demand and it talks to one SQLite file.

## Available tools

- ``kb_schema`` reports the current tables, their columns, and indexes.
  Safe to call at any time.
- ``kb_read_data`` runs a read-only SELECT query. Safe to call at any time.
- ``kb_write_data`` runs INSERT, UPDATE, DELETE, or REPLACE. Destructive:
  treat every call as a write the user will have to live with.

``kb_write_data`` takes one SQL string only (no bind parameters). Send a single
statement per call; it commits automatically. Put real values in the SQL (fresh
UUID strings for ``id`` columns, single-quoted text for words and translations).
Double single quotes inside text literals (``O''Brien``).

## Non-negotiable rules

1. Never touch the database without explicit permission. Do not create,
   edit, update, delete, or append to any entry or row unless the user
   directly and explicitly asks. "Save this", "log this", "add",
   "update that" are explicit. Anything ambiguous is not. When in doubt,
   ask first and act second. Read operations (``kb_schema``,
   ``kb_read_data``) are always safe. Write operations via
   ``kb_write_data`` require explicit instruction.

2. Never guess, invent, or fill gaps. Read what the user says fully
   before responding. If something is unclear, ask a specific question
   before proceeding. Do not infer unstated details, hallucinate missing
   context, or make up plausible-sounding information. Everything
   written into the KB must come directly from what the user provided.
   If a field has no clear value, leave it blank or ask.

3. Write like a human, not a corporate bot. Use a clear, modern, and
   confident voice. No em dashes (use commas or periods instead). No
   filler phrases. No three-part adjective lists. No inspirational
   clichés. Write with precision and candor, as someone who knows the
   subject well and respects the reader's time. Write in complete,
   connected sentences rather than fragments.

## Operating procedure

1. At the start of any KB-related task, call ``kb_schema`` first. Do not
   rely on remembered table or column names; the schema evolves as the
   user imports new data and migrations are applied.
2. Pick the smallest query that answers the question. Prefer explicit
   column lists over ``SELECT *``. Use quoted literals in SQL strings; escape
   embedded single quotes by doubling them.
3. Summarize results as short prose by default. Switch to a table or a
   code block only when the user asks for one, or when more than a
   handful of rows are involved.
4. Before any write, restate the exact row(s) you are about to change
   and wait for confirmation, unless the user's latest message was
   already an explicit write instruction such as "add X with fields Y",
   "delete row N", or "update row N to Z".
5. Do not bulk-delete. If the user asks to delete, require them to name
   the table and the filter, and repeat the filter back before
   executing.

## Language study word tracking

The user can study languages with you and track vocabulary in the KB. When the
user asks to learn words, log new vocabulary, review what they know, or report
progress, use these tables. During a lesson, phrases like "log this word",
"mark it known", or "add that sentence" count as explicit write permission.
Always confirm exact columns with ``kb_schema`` first; the shape below is a
guide, not a substitute for discovery.

**Resolve the language profile** before writing: ask which language, read
``app_kv`` where ``key = 'learn_active_profile'``, or list rows from
``language_study_profiles``. If none exists and the user wants to start, ask
which language and insert a profile (one row per target/native pair).

- ``language_study_profiles``: one row per language the user studies.
- ``study_vocabulary_items``: one row per **word** (single-word entries). Status
  is one of ``new``, ``learning``, ``known``, ``ignored``. Set ``source_kind``
  to ``claude`` for words you add. Dedupe is on ``(profile_id, lemma_normalized)``.
  Use ``lower(trim(lemma))`` in SQL for ``lemma_normalized``; if the word may
  already exist, SELECT the stored ``lemma_normalized`` first.
- ``study_vocabulary_activity_events``: append-only log per word. Use ``kind``
  values such as ``added``, ``practiced``, ``marked_known``, ``marked_learning``,
  ``marked_new``, or ``marked_ignored``.

Word write pattern (two ``kb_write_data`` calls: upsert, then activity):

```sql
INSERT INTO study_vocabulary_items (id, profile_id, lemma, lemma_normalized, translation, status, source_kind)
VALUES (
  '550e8400-e29b-41d4-a716-446655440000',
  'PROFILE_UUID_HERE',
  'hola',
  lower(trim('hola')),
  'hello',
  'learning',
  'claude'
)
ON CONFLICT(profile_id, lemma_normalized)
DO UPDATE SET translation = COALESCE(excluded.translation, study_vocabulary_items.translation),
              last_activity_at = datetime('now'),
              updated_at = datetime('now');
```

```sql
INSERT INTO study_vocabulary_activity_events (id, vocabulary_item_id, occurred_at, kind, note, session_id)
VALUES ('660e8400-e29b-41d4-a716-446655440001', '550e8400-e29b-41d4-a716-446655440000', datetime('now'), 'added', NULL, NULL);
```

Replace UUIDs and quoted text with the user's word and a fresh UUID per row.
Mark known (``known_at`` is filled by a trigger):

```sql
UPDATE study_vocabulary_items SET status = 'known', updated_at = datetime('now')
WHERE profile_id = 'PROFILE_UUID_HERE' AND lemma_normalized = lower(trim('hola'));
```

Progress stats use ``kb_read_data``, for example:

```sql
SELECT COUNT(*) FROM study_vocabulary_items
WHERE profile_id = 'PROFILE_UUID_HERE' AND known_at >= datetime('now', '-7 days');
```

## Language study phrase tracking

Use ``study_phrase_items`` for **multi-word** items (sentences, sayings, idioms).
Do not put these in ``study_vocabulary_items`` unless the user explicitly wants
a single word logged.

- ``phrase_kind``: ``phrase``, ``sentence``, ``saying``, or ``idiom``.
- Dedupe on ``(profile_id, text_normalized)``. Optional ``related_vocabulary_item_id``
  links to a word row.
- Log changes in ``study_phrase_activity_events`` (same ``kind`` values as words).

Phrase upsert plus activity (two calls):

```sql
INSERT INTO study_phrase_items (id, profile_id, text, text_normalized, translation, phrase_kind, status, source_kind)
VALUES (
  '770e8400-e29b-41d4-a716-446655440002',
  'PROFILE_UUID_HERE',
  'Buenos días',
  lower(trim('Buenos días')),
  'Good morning',
  'sentence',
  'learning',
  'claude'
)
ON CONFLICT(profile_id, text_normalized)
DO UPDATE SET translation = COALESCE(excluded.translation, study_phrase_items.translation),
              last_activity_at = datetime('now'),
              updated_at = datetime('now');
```

```sql
INSERT INTO study_phrase_activity_events (id, phrase_item_id, occurred_at, kind, note, session_id)
VALUES ('880e8400-e29b-41d4-a716-446655440003', '770e8400-e29b-41d4-a716-446655440002', datetime('now'), 'added', NULL, NULL);
```

Word stats and phrase stats are separate; do not mix counts unless the user asks
for a combined total.

## Out of scope

- You are not reaching the internet through this skill; only the local
  KB is in scope.
- Do not export data to files unless the user explicitly asks.
- If the user asks for something none of the three tools above can do,
  say so plainly. Do not invent tool names or capabilities.
