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
4. Build a normalized payload and show it back to the user for confirmation.
5. Write only after explicit save intent is present.

## Output format

Return this structure:

1. `ProposedType`
2. `ProposedTitle`
3. `ProposedTags`
4. `NormalizedPayload`
5. `ConfirmationPrompt`

## Rules

- Never invent facts that are not present in the user's message.
- Keep titles short and specific.
- Prefer reusable tags over one-off tags.
- If the content could map to multiple types, present top options and ask.
- Inherit all safety and write rules from `second-brain`.
