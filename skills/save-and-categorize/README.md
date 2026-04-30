# Save and Categorize

Smart capture workflow for "save this" commands.

## Prerequisite

This skill depends on the default `second-brain` skill.
Install `second-brain` first, then add this skill.
It inherits core KB access, safety, and write-confirmation rules from
`second-brain`.

## What it returns

- `ProposedType`
- `ProposedTitle`
- `ProposedTags`
- `PossibleDuplicate` (if relevant)
- `NormalizedPayload`
- `ConfirmationPrompt`

## Example prompts

- "Save this: We should test Stripe retries for failed invoices"
- "Log this note about our Q3 pricing discussion"
- "Capture this idea and tag it properly"
- "Save this meeting takeaway under the right category"
- "Save this meeting note from today's broker call"
- "Capture this investor update and tag it correctly"
- "Log this deal note as a follow-up task"
