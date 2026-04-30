# Second Brain (Default Skill)

Default skill for safe access to your local Second Brain database through MCP.

## What it does

- Uses `kb_schema`, `kb_read_data`, and `kb_write_data`.
- Enforces schema-first querying.
- Requires explicit user intent before writes.
- Keeps responses concise and grounded in actual data.

## When to use it

Use this as the baseline skill whenever the user asks about stored personal KB data:

- contacts
- emails
- billing records
- notes
- imported data

## What it does not do

- It does not browse the web.
- It does not export files unless explicitly requested.
- It does not invent fields, tables, or values.

## Decision Safety

- Every read response should include confidence and what it is based on.
- Every important conclusion should be traceable to concrete KB entities.
- Every write requires explicit user intent, and business-impact writes
  should be previewed before execution.

## Example prompts

- "Show me how many contacts I have by country"
- "Find all notes that mention Stripe this month"
- "Update this contact's status to active"
- "Delete records from table X where Y" (with explicit filter confirmation)
