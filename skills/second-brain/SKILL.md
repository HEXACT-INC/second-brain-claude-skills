---
name: second-brain
description: Query and update the user's local Second Brain personal knowledge base through the personal-kb MCP tools (kb_schema, kb_read_data, kb_write_data). Use whenever the user asks about their stored data - contacts, emails, billing records, notes, anything in the KB. Always discover the current schema before issuing queries; never assume table or column names.
---

# Second Brain skill

This skill gives you read and controlled-write access to the user's local
Second Brain SQLite knowledge base via the `personal-kb` MCP server entry
they configured in Claude (same key as in `claude_desktop_config.json` under
`mcpServers`). The user runs Second Brain locally. Claude spawns that server
on demand and it talks to one SQLite file.

## Available tools

- `kb_schema` reports the current tables, their columns, and indexes.
  Safe to call at any time.
- `kb_read_data` runs a read-only SELECT query. Safe to call at any time.
- `kb_write_data` runs INSERT, UPDATE, DELETE, or REPLACE. Destructive:
  treat every call as a write the user will have to live with.

## Non-negotiable rules

1. Never touch the database without explicit permission. Do not create,
   edit, update, delete, or append to any entry or row unless the user
   directly and explicitly asks. "Save this", "log this", "add",
   "update that" are explicit. Anything ambiguous is not. When in doubt,
   ask first and act second. Read operations (`kb_schema`,
   `kb_read_data`) are always safe. Write operations via
   `kb_write_data` require explicit instruction.

2. Never guess, invent, or fill gaps. Read what the user says fully
   before responding. If something is unclear, ask a specific question
   before proceeding. Do not infer unstated details, hallucinate missing
   context, or make up plausible-sounding information. Everything
   written into the KB must come directly from what the user provided.
   If a field has no clear value, leave it blank or ask.

3. Write like a human, not a corporate bot. Use a clear, modern, and
   confident voice. No em dashes (use commas or periods instead). No
   filler phrases. No three-part adjective lists. No inspirational
   cliches. Write with precision and candor, as someone who knows the
   subject well and respects the reader's time. Write in complete,
   connected sentences rather than fragments.

## Operating procedure

1. At the start of any KB-related task, call `kb_schema` first. Do not
   rely on remembered table or column names, the schema evolves as the
   user imports new data and migrations are applied.
2. Pick the smallest query that answers the question. Prefer explicit
   column lists over `SELECT *`. Parameterize values. Never concatenate
   user-supplied strings into SQL.
3. Use a query budget for read tasks: run the minimum query first, then
   run at most one follow-up query if confidence is still not sufficient.
   If confidence remains low, stop and report uncertainty clearly.
4. Summarize results as short prose by default. Switch to a table or a
   code block only when the user asks for one, or when more than a
   handful of rows are involved.
5. For read results, include this footer:
   - `Confidence`: High | Medium | Low
   - `BasedOn`: tables or entities used
   - `MissingData`: what would improve the answer
6. Before any write, restate the exact row(s) you are about to change
   and wait for confirmation, unless the user's latest message was
   already an explicit write instruction such as "add X with fields Y",
   "delete row N", or "update row N to Z".
7. For writes with business impact, show a compact mutation checklist
   before execution:
   - target table and row filter
   - columns being changed
   - new values
8. Do not bulk-delete. If the user asks to delete, require them to name
   the table and the filter, and repeat the filter back before
   executing.

## Out of scope

- You are not reaching the internet through this skill, only the local
  KB is in scope.
- Do not export data to files unless the user explicitly asks.
- If the user asks for something none of the three tools above can do,
  say so plainly. Do not invent tool names or capabilities.
