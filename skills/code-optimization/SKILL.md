---
name: meeting-prep
description: Build a pre-meeting brief by pulling known context about a person or company from the Second Brain KB. Use when the user asks to prepare for a call, meeting, intro, or check-in.
---

# Meeting Prep

Produce a compact meeting brief from KB data.

## Workflow

1. Run `kb_schema` first and identify where contact, company, notes, and interaction data lives.
2. Gather relevant rows for the target person/company and recent related activity.
3. Resolve duplicates or conflicting facts before writing the brief.
4. Build a one-brief output focused on context the user can act on in the call.

## Output format

Return these sections in order:

1. `MeetingObjective` (single sentence)
2. `WhoTheyAre` (identity and role context)
3. `RecentInteractions` (last touchpoints)
4. `ContextSignals` (important notes, risk/opportunity signals)
5. `NegotiationLevers` (only if relevant)
6. `RiskFlags` (max 3)
7. `DesiredOutcome` (binary success condition)
8. `SuggestedAgenda` (3-6 items)
9. `QuestionsToAsk` (3-7 items)

## Rules

- Prioritize recent and high-confidence signals.
- If the person or company match is ambiguous, force disambiguation
  before creating the brief.
- Keep non-agenda and non-question content to 8 bullets max.
- Keep it brief enough to read in under 2 minutes.
- Inherit all safety and write rules from `second-brain`.
