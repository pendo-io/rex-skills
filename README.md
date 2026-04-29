# rex-skills

Curated skill library for **Glass / Project Rex** — Pendo's internal AI productivity suite.

Each skill is a single markdown file with YAML frontmatter that Sensei ingests via the GitHub API.

## Layout

```
.
├── _kits.json              # Closed taxonomy of kits (admin-defined)
├── essentials/             # Skills every Pendo user should install
├── sales/                  # AE / pipeline / Gong skills
├── csm/                    # Customer Success / churn / QBR
├── flm/                    # Front-line manager 1:1 prep, team rollups
├── engineering/            # IAF platform / SAVER / governed BQ
├── finance/                # FP&A / forecast / Ramp triage
├── dev/                    # Platform meta-skills (scaffolding, evals)
├── frontend/               # UI design + skill creation skills
├── decks/                  # Presentation generation skills
└── templates/              # SKILL_TEMPLATE.md and starter scaffolds
```

## Skill format

```yaml
---
id: champion-tracking
name: Champion Tracking
description: Given an account, finds UserGems champion-moved data and Pulse contacts.
kit: csm                                # one closed-vocab kit
audience: [csm, ae]                     # closed: ae, csm, flm, engineering, finance, ops, exec
workflow: [research, analysis]          # closed: research, drafting, analysis, reporting,
                                        # automation, prep, monitoring, review
tools: [query_salesforce, query_bigquery, fetch_usergems_moves]
tags: [hot, pendo-github]               # free-form, optional
source_kind: pendo-curated              # pendo-curated | user-created | gh-imported
author: pendo-iaf                       # email or handle of the owner
version: 1.0.0
created: 2026-04-17
---

# Champion Tracking

## When to use
...

## What it does
...

## Inputs
...

## Instructions (given to the agent)
...

## Example
...

## Output format
...
```

## Adding a skill

1. Pick a kit folder, or use `templates/SKILL_TEMPLATE.md`.
2. Add the YAML frontmatter, write the body sections.
3. Open a PR. Owners + collaborators of the kit can merge.
4. Sensei picks it up on next sync (auto on push to `main` once the GitHub App webhook is wired).

## Source of truth

This repo is the source of truth for **skill content**. Engine code lives in [`pendo-io/rex-sensei`](https://github.com/pendo-io/rex-sensei). Anything dynamic (likes, comments, ratings, install counts, drafts) lives in Postgres — not here.
