---
name: save-and-categorize
description: Process "save this" requests into clean KB records by selecting an entry type, generating a title, assigning tags, and confirming the normalized payload. Use when the user shares raw content and asks to save, log, or capture it.
---

# Save and Categorize

Turn unstructured capture requests into clean, confirmed KB records.

## Workflow

1. Read the user input and identify the likely entry type from available schema.
2. Generate a concise title from the actual content, not generic wording.
3. Propose tags based on topic, project, entity, and intent.
4. Run a quick similarity lookup to detect likely duplicates.
5. Build a normalized payload and show it back to the user for confirmation.
6. Write only after explicit save intent is present.

## Output format

Return this structure:

1. `ProposedType`
2. `ProposedTitle`
3. `ProposedTags`
4. `PossibleDuplicate` (if relevant)
5. `NormalizedPayload` including:
   - `sourceType` (meeting/email/idea/task/document)
   - `timeContext` (date or inferred period)
   - `entityRefs` (people, companies, properties, deals)
6. `ConfirmationPrompt`

## Rules

- Never invent facts that are not present in the user's message.
- Keep titles short and specific.
- Prefer reusable tags over one-off tags.
- Use up to 5 tags, with at least one domain tag and one intent tag.
- If the content could map to multiple types, present top options and ask.
- Inherit all safety and write rules from `second-brain`.
