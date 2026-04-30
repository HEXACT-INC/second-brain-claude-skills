---
name: weekly-review
description: Summarize what the user captured this week from their Second Brain, including saved items, notes, and activity signals. Use when the user asks for a weekly recap, Monday review, or "what happened this week" summary.
---

# Weekly Review

Generate a concise weekly recap from the user's KB.

## Workflow

1. Start with `kb_schema` and confirm where relevant weekly data exists.
2. Query the last 7 days of relevant records (notes, saves, logs, related activity).
3. Group findings by theme, not by raw table order.
4. Highlight open loops and unresolved items.
5. End with a short focus suggestion for the next week.

## Output format

Return these sections in order:

1. `WhatCaptured` (3-8 bullets)
2. `KeyThemes` (2-5 bullets)
3. `OpenLoops` (0-5 bullets, write "None" if empty)
4. `NextWeekFocus` (2-4 bullets)

## Rules

- Keep the whole review short and practical.
- Do not dump raw rows unless asked.
- If data is sparse, say what is missing and still provide a useful recap.
- Inherit all safety and write rules from `second-brain`.
