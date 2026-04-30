---
name: skill-id-here
description: >
  What the skill does, in one paragraph. Include explicit trigger phrases —
  "Use when user says X, mentions Y, or asks for Z." A description that's a bit
  pushy triggers more reliably than one that's too neutral.
kit: essentials
audience: []
workflow: []
tools: []
tags: []
source_kind: pendo-curated
author: your-handle
version: 1.0.0
created: 2026-04-30
---

# Skill Name Here

Open with one or two sentences on the outcome. Imperative form preferred — explain
the *why* behind instructions instead of leaning on ALWAYS / NEVER.

## When to use
Trigger conditions — situation, user phrasing, state of the world.

## Inputs
- input_name: description (required/optional)

## Instructions

1. Step one.
2. Step two.
3. Step three.

## Example

User: "example prompt"
Agent: (what the agent does)

## Output format
Describe the structure of the output.

---

## Bundled resources (optional)

Per Anthropic Agent Skills spec, this folder may also contain:

```
<skill-id>/
├── SKILL.md          (this file — required)
├── scripts/          (executable code for deterministic / repetitive work)
├── references/       (docs loaded into context as needed)
└── assets/           (templates, fonts, icons used in output)
```

Reference resources from SKILL.md with explicit guidance on when to load them.
For large reference files (>300 lines), include a table of contents.
