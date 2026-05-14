---
name: design-to-delivery
description: >
  End-to-end workflow that takes Glass/Rex AI experience design context — from Granola
  transcripts, Google Docs, Slides, Sheets, or a live grilling session — and delivers
  filled scoping checklists, a BQ event table, a monitoring dashboard scaffold, Pendo
  tracking guidance, calendar invites, Jira tickets, and coding agents that open PRs
  for human review. Use when user says "design to delivery", "transcript to tickets",
  "turn this call into work", "process this design call", "kick off work from this
  meeting", or wants to convert a design discussion into executable engineering work.
---

# Design to Delivery

Five-phase workflow: gather context → fill scoping checklists → set up data & tracking
→ create Jira tickets → execute coding agents. Every phase ends with a human approval
gate before the next begins. Agents open PRs but never merge.

See [CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md) for the full pre- and post-launch field
definitions. See [AGENT_PROMPT.md](AGENT_PROMPT.md) for the coding agent briefing template.

---

## Phase 1 — Gather Context

Ask the user where the design context lives. Accept any combination:

| Source | How to ingest |
|---|---|
| Granola recording | `query_granola_meetings` → `get_meeting_transcript` |
| Google Doc / Slides / Sheet | Fetch the URL the user provides; read contents via Drive MCP |
| Multiple sources | Combine into a unified context before proceeding |
| Nothing yet | See below |

**If no context exists:** reference the scoping checklist framework (see
[CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md)) and invoke `grill-me` to interview the user.
Frame the grilling around the 11 pre-launch fields: JTBD, trigger, experience type, data
sources, output, actions, suppression logic, success metric, build vs buy, experience
evaluation, and ongoing monitoring. Use the grill-me output as the design context.

**Approval gate:** summarise the gathered context in 3–5 bullets and ask the user to
confirm before proceeding.

---

## Phase 2 — Fill Scoping Checklists

**2a. Create a named project doc**

Copy the scoping checklist template (Google Doc ID:
`1N2XsrW6EifvxBs_bDVoGCiVZR_9jDrrctzhSdijZxDw`) using `copy_file`. Name it:
`[Project Name] — AI Experience Scoping Checklist`. Share the link with the user.

**2b. Fill the pre-launch checklist** (11 fields — see [CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md))

Work through each field using the gathered context. Where the context doesn't answer a
field, ask the user directly. Fill answers into the copied doc via the Drive MCP.

**2c. Fill the post-launch checklist** (CC/CD framework)

Define the Surface → Engage → Resolve metrics, calibration loop plan, feedback
mechanisms (thumbs up/down + free-form), business KPI, and UX KPI. Fill into the doc.

**Approval gate:** show the user a summary of all filled fields and ask for confirmation
before proceeding. They can edit anything in the doc directly.

---

## Phase 3 — Set Up Data & Tracking

Run all four sub-steps, then present a combined summary for approval.

**3a. BQ event table**

Ask the user: which GCP project and dataset should the event table live in?
A coding agent will generate and apply the `CREATE TABLE` DDL based on the experience
type and output fields defined in Phase 2. The table should follow the pattern of
`gtm_engineering.sales_hygiene_event_log` — event log with timestamp, user, action,
result, and experience-specific fields.

**3b. Monitoring dashboard**

Ask the user: is there an existing dashboard template or repo to start from?
- If yes: provide the URL/repo and a coding agent will clone and adapt it.
- If no: a coding agent will scaffold a basic dashboard reading from the BQ event table
  and mark it with a TODO for the user to build out to completion.

Reference the IAF HQ dashboard (`https://iaf-hq-94438242953.us-east1.run.app/shb/roadmap`)
as the design target for structure and layout.

**3c. Pendo tracking**

Based on the experience type from Phase 2:
- **P4E** — for employee-facing applications (surfaces UI in Glass or internal tools)
- **Agent Analytics** — for AI agents only (no direct UI surface)

Identify the specific events to track and document them in the scoping checklist doc.

**3d. Calendar invites**

Create two calendar events for the user (add user as sole attendee; prompt them to add
others before the meetings):

| Meeting | Timing | Purpose |
|---|---|---|
| Code Review | 2 business days after today | Review PRs opened by coding agents |
| Team Review | 2 business days after Code Review | Review output with the broader team |

Use `create_event` with the project name in the title. Include the Jira project link and
GitHub repo in the description once known.

**Approval gate:** present the BQ table schema, dashboard plan, Pendo tracking events,
and calendar invite details. Get explicit approval before any of this is created.

---

## Phase 4 — Create Jira Tickets

Ask which Jira project to use (offer to list via `getVisibleJiraProjects`).

Extract discrete, independently-executable work items from the Phase 2 context. For each:
- **Title**: short imperative phrase
- **Description**: what to build and why, grounded in the source material
- **Acceptance criteria**: concrete, testable conditions for done
- **Source excerpt**: 2–4 lines from the source material that motivate this ticket
- **Dependencies**: any tickets that must complete first
- **Suggested repo**: where the code likely lives (e.g. `rex-skills`, `project-rex`)

Present the full ticket list for user review. User can approve as-is, edit, remove, or
adjust dependencies. **Do not create any tickets until explicitly approved.**

On approval: create each ticket via `createJiraIssue` (unassigned), link dependents via
`createIssueLink`, and update the calendar invite descriptions with the Jira links.

---

## Phase 5 — Execute Coding Agents

Build a dependency graph. Tickets with no blockers run in parallel. Blocked tickets wait
for their dependencies to have open PRs. **Prioritize correctness over speed.**

For each ticket, spawn an Agent with `isolation: "worktree"` using the template in
[AGENT_PROMPT.md](AGENT_PROMPT.md). Pass:
- Full ticket description and acceptance criteria
- Source excerpt that motivated the work
- BQ table schema and dashboard scaffold location (from Phase 3)
- Output context from any dependency tickets (file paths, exported names)
- Pointers to the most relevant existing code (search before spawning)
- Instructions to run all checks (tests, lint, build) before opening a PR
- Instructions to open a draft PR targeting main — **never merge**

**After each PR opens:**
1. Comment the PR URL on the Jira ticket via `addCommentToJiraIssue`
2. Assign the ticket to the user via `editJiraIssue`

---

## Final Summary

```
Design to Delivery — Complete

Scoping doc: <google doc link>

Tickets created:
- [PROJ-123] Title — <jira link>

PRs opened:
- [repo#N] Title — <pr link> (ticket: PROJ-123)

Calendar:
- Code Review: <date> — <calendar link>
- Team Review: <date> — <calendar link>

Needs your attention:
- Dashboard scaffold at <path> — marked TODO, needs build-out
- Any agent blockers or unresolved decisions
```
