# Second Brain (Default Skill)

Default skill for safe access to your local Second Brain database through the
`personal-kb` MCP server (`kb_schema`, `kb_read_data`, `kb_write_data`).

## What it does

- Discovers the live schema before every query; never assumes table or column names.
- Reads KB data with `kb_read_data` and summarizes results in short prose.
- Writes only on explicit instruction, with confirmation before destructive changes.
- Tracks language study vocabulary and phrases when you ask to log words or sentences.

## When to use it

Use this as the baseline skill whenever you ask about stored personal KB data:

- contacts
- notes
- billing records
- imported data
- language vocabulary and phrase progress

## Language study

During a lesson, phrases like "log this word", "mark it known", or "add that
sentence" count as explicit write permission. Words go in `study_vocabulary_items`;
multi-word items (sentences, sayings, idioms) go in `study_phrase_items`. The skill
resolves your active language profile before writing.

## What it does not do

- It does not browse the web.
- It does not export files unless you explicitly ask.
- It does not invent fields, tables, or values.

## Safety

- Reads are always safe. Writes require explicit intent ("save this", "log this", "add", "update that").
- Before a write, the agent restates the exact rows to change unless your message already named them.
- Deletes require a named table and filter, repeated back before execution. No bulk deletes.

## Example prompts

- "Show me how many contacts I have by country"
- "Find all notes that mention Stripe this month"
- "Update this contact's status to active"
- "Delete records from table X where Y" (with explicit filter confirmation)
- "Log hola as hello in my Spanish study list"
- "Mark buenos días as known"
- "How many words did I learn this week?"
