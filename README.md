# rex-skills

Curated skill library for **Glass / Project Rex** — Pendo's internal AI productivity suite.

Skills follow the **Anthropic Agent Skills format**: each skill is a folder with a `SKILL.md` inside. Sensei ingests them via the GitHub API.

```
<kit>/<skill-id>/SKILL.md     # required
<kit>/<skill-id>/<other>      # optional resources, examples, helper scripts
```

## Layout

```
.
├── _kits.json              # Closed taxonomy of kits (admin-defined)
├── essentials/             # Skills every Pendo user should install
├── sales/                  # AE / pipeline / Gong skills
├── csm/                    # Customer Success / churn / QBR
├── swe/                    # Software engineering — coding, debugging, code review, dev workflows
├── flm/                    # Front-line manager 1:1 prep, team rollups
├── finance/                # FP&A / forecast / Ramp triage
└── templates/              # SKILL_TEMPLATE.md and starter scaffolds
```

## Skill format

`SKILL.md` is markdown with YAML frontmatter. `name` and `description` are
required (Anthropic spec). The rest are Pendo-specific extensions Sensei
uses for ranking, filtering, and discovery surfaces.

```yaml
---
name: caveman                                   # canonical id (kebab-case)
description: >                                  # 1-paragraph; used by Sensei + agent
  Ultra-compressed communication mode. Use when user says "caveman", "be brief".
kit: swe                                        # closed: essentials, sales, csm, swe, flm, finance
audience: [swe]                                 # closed: ae, csm, flm, swe, finance, ops, exec
workflow: [communication]                       # closed: research, drafting, analysis, reporting,
                                                # automation, prep, monitoring, review, communication
tools: []                                       # canonical tool names (e.g. query_salesforce)
tags: [terse, productivity]                     # free-form, optional
source_kind: gh-imported                        # pendo-curated | user-created | gh-imported
source_url: https://github.com/...              # optional, for imports
author: juliusbrussee                           # email or handle
version: 1.0.0
created: 2026-04-30
---

(body markdown)
```

## Adding a skill

1. Create folder `<kit>/<skill-id>/`.
2. Drop `SKILL.md` inside (use `templates/SKILL_TEMPLATE.md`).
3. Add support files (examples, scripts, resources) in the same folder if needed.
4. Open a PR. Owners + collaborators of the kit can merge.
5. Sensei picks it up on next sync (auto on push to `main` once the GitHub App webhook is wired).

## Source of truth

This repo is the source of truth for **skill content**. Engine code lives in [`pendo-io/project-rex`](https://github.com/pendo-io/project-rex) (Sensei is the `rex-sensei/` folder there). Anything dynamic (likes, comments, ratings, install counts, drafts) lives in Postgres — not here.
