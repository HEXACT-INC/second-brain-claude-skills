---
name: quick-lookup
description: Answer "what do I know about X?" by searching across relevant KB entities and returning a concise synthesis instead of raw data dumps. Use for fast context retrieval on people, topics, companies, projects, and events.
---

# Quick Lookup

Provide a short, useful synthesis across KB sources.

## Workflow

1. Start with `kb_schema` and identify candidate sources (entries, contacts, notes, related records).
2. Query only the minimum needed to answer the question.
3. Merge overlapping facts and remove duplicates.
4. Distinguish confirmed facts from uncertain or missing information.

## Output format

Return these sections:

1. `ExecutiveSummary` (2-4 lines)
2. `WhatKnown` (concise synthesis)
3. `Evidence` (key supporting items with timestamps when available)
4. `DecisionUse` (how this can be used now)
5. `UncertainOrMissing` (gaps, conflicts, unknowns)

## Rules

- Do not return raw SQL output unless explicitly requested.
- Prefer synthesis over chronology unless the user asks for a timeline.
- If there are multiple entities matching X, return top matches with a
  short disambiguation question before final synthesis.
- Inherit all safety and write rules from `second-brain`.
