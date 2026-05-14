---
name: design-to-delivery
description: >
  Turns Glass/Rex design context — from Granola transcripts, Google Docs, Slides,
  Sheets, or a live grilling session — into Jira tickets, then spins up coding agents
  to execute the work and open PRs for human review. Use when user says "design to
  delivery", "transcript to tickets", "turn this call into work", "process this design
  call", "kick off work from this meeting", or wants to convert a design discussion
  into executable engineering work.
kit: essentials
audience: [swe, ops, exec]
workflow: [prep, automation]
tools: [query_granola_meetings, get_meeting_transcript, createJiraIssue, editJiraIssue, getVisibleJiraProjects, createIssueLink, addCommentToJiraIssue]
tags: [design, delivery, jira, agents, transcripts, automation]
source_kind: pendo-curated
author: jonathan.rice
version: 1.1.0
created: 2026-05-13
---

# Design to Delivery

Turn Glass/Rex design context into shipped code: gather the input, extract discrete work
items, create Jira tickets, execute coding agents, and open PRs — with a human review gate
before any code is written.

See [AGENT_PROMPT.md](AGENT_PROMPT.md) for the coding agent briefing template.

## Workflow

### Step 1 — Gather context

Ask the user where the design context lives. Accept any combination of:

| Source | How to ingest |
|---|---|
| Granola recording | `query_granola_meetings` → `get_meeting_transcript` |
| Google Doc | Fetch the doc URL the user provides; read its contents |
| Google Slides | Fetch the deck URL; read slide text and speaker notes |
| Google Sheet | Fetch the sheet URL; read relevant tabs |
| Multiple sources | Combine all into a unified context before extracting tickets |
| Nothing yet | Default to the **grill-me** skill (see below) |

**If no context exists:** invoke the `grill-me` skill to interview the user about what
they want to build. Use the output of that session as the design context for Step 2.
Frame the grilling around: what problem is being solved, who it's for, what the
experience should feel like, and what constraints exist.

If multiple Granola meetings match a keyword, show a list and let the user pick.

### Step 2 — Extract proposed tickets

Analyze the gathered context and extract discrete, independently-executable work items.
For each:
- **Title**: short imperative phrase
- **Description**: what needs to be built and why, grounded in the source material
- **Acceptance criteria**: concrete, testable conditions for done
- **Source excerpt**: the 2–4 lines from the source material that motivate this ticket
- **Dependencies**: any other tickets that must complete first
- **Suggested repo**: where the code likely lives (e.g. `rex-skills`, `project-rex`)

Focus on Glass/Rex scope only. Skip discussion items, questions, and anything not actionable.

### Step 3 — Clarify before presenting

Ask two questions (can be asked together):
1. Which Jira project should the tickets go into? (offer to list via `getVisibleJiraProjects`)
2. Should the work go into an existing repo — and if so, which one — or a new repo?

### Step 4 — Present the plan and get approval

Show the full proposed ticket list in a scannable format. The user can approve as-is,
edit tickets inline, remove tickets, or adjust dependencies.

**Do not create any tickets or write any code until the user explicitly approves.**

### Step 5 — Create Jira tickets

For each approved ticket, create via `createJiraIssue`:
- Leave assignee blank initially
- Include description, acceptance criteria, and source excerpt
- Link dependent tickets via `createIssueLink`

### Step 6 — Execute coding agents

Build a dependency graph. Tickets with no blockers run in parallel. Blocked tickets wait
for their dependencies to have open PRs before spawning. **Prioritize correctness over speed.**

For each ticket, spawn an Agent with `isolation: "worktree"` and pass:
- Full ticket description and acceptance criteria
- Source excerpt that motivated the work (transcript lines, doc paragraph, slide text, or grill-me output)
- Links and output context from any dependency tickets
- Pointers to the most relevant existing code in the repo (search before spawning)
- Instructions to run all available checks (tests, lint, build) before opening a PR
- Instructions to open a draft PR targeting main when checks pass

Use the template in [AGENT_PROMPT.md](AGENT_PROMPT.md) to structure each agent briefing.

### Step 7 — Close the loop

After each PR is opened:
1. Add the PR URL as a comment on the Jira ticket via `addCommentToJiraIssue`
2. Assign the ticket to the user via `editJiraIssue`

### Step 8 — Deliver the summary

```
Design to Delivery — Complete

Tickets created:
- [PROJ-123] Title — <jira link>

PRs opened:
- [repo#N] Title — <pr link> (ticket: PROJ-123)

Needs your attention:
- Any blockers or decisions the agent couldn't resolve
```

## Quality bar

Agents must not open a PR if checks fail. They should fix failures before surfacing to
the user. If an agent cannot fix a failure after reasonable attempts, it reports the
blocker in the summary rather than opening a broken PR.
