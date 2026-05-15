---
name: design-to-delivery
description: >
  End-to-end workflow that takes Glass/Rex AI experience design context — from Granola
  transcripts, Google Docs, Slides, Sheets, or a live structured interview — and delivers
  a Pendo-branded scoping checklist (.docx), a BQ event table, a monitoring dashboard
  scaffold, Pendo tracking guidance, calendar invites, Jira tickets, and coding agents
  that open PRs for human review. Use when user says "design to delivery", "transcript
  to tickets", "turn this call into work", "process this design call", "kick off work
  from this meeting", or wants to convert a design discussion into executable engineering
  work.
---

# Design to Delivery

Five-phase workflow: gather context → fill scoping checklists → set up data & tracking
→ create Jira tickets → execute coding agents. Every phase ends with a human approval
gate before the next begins. Agents open PRs but never merge.

See [INTERVIEW_GUIDE.md](INTERVIEW_GUIDE.md) for the full section-by-section interview
questions. See [CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md) for field definitions.
See [AGENT_PROMPT.md](AGENT_PROMPT.md) for the coding agent briefing template.

---

## Phase 1 — Gather Context

Ask the user where the design context lives. Accept any combination:

| Source | How to ingest |
|---|---|
| Granola recording | `query_granola_meetings` → `get_meeting_transcript` |
| Google Doc / Slides / Sheet | Fetch the URL the user provides; read contents via Drive MCP |
| Multiple sources | Combine into a unified context before proceeding |
| Nothing yet | Run the structured interview (see below) |

**If no context exists — run the structured interview using `grill-with-docs`:**

Invoke the `grill-with-docs` skill, passing [INTERVIEW_GUIDE.md](INTERVIEW_GUIDE.md) and
[CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md) as the domain documentation to challenge against.

`grill-with-docs` will:
- Ask questions one at a time, waiting for feedback before continuing
- Challenge the user when they use terms that conflict with Pendo's domain model
  (e.g., "Action Driver" vs "Alert", "Intelligence Provider" vs "Copilot")
- Sharpen fuzzy language into precise canonical terms
- Cross-reference with the scoping checklist framework and any existing project docs
- Update `CONTEXT.md` and create ADRs inline as decisions crystallise

The grilling session should cover all 9 sections of the Module Definition Framework
(see [INTERVIEW_GUIDE.md](INTERVIEW_GUIDE.md)) across all modules, one at a time.
Start with: project name, one-sentence description, and number of modules.

Start with:
> "Let's scope this out properly. I'll walk you through the Module Definition Framework
> section by section — it usually takes 5–10 minutes per module and ensures the build
> is focused and measurable.
>
> First: **what's the name of this project**, and give me a one-sentence description of
> what it's supposed to do?"

**Approval gate:** summarise the gathered context in 3–5 bullets per module and ask the
user to confirm before proceeding to Phase 2.

---

## Phase 2 — Generate Scoping Document

**2a. Generate the Pendo-branded .docx**

Using the data gathered in Phase 1, generate a scoping document using the
`scripts/generate_scoping_doc.py` generator:

1. Download the canonical template from Google Drive:
   - File ID: `1wPQ3GgFYdB4AWdhxrJG4dfC304CiJV3qtFHtJpLb3Ik`
   - Export as `.docx` via the Drive MCP `download_file_content` tool
2. Call `generate_scoping_doc()` with the filled module data and optional CC/CD framework
   (see [scripts/generate_scoping_doc.py](scripts/generate_scoping_doc.py) for the full
   interface and Pendo Pink brand styling rules)
3. Output to `/mnt/user-data/outputs/[ProjectName]_Scoping_Checklist.docx`
4. Present the file to the user for download

**Brand styling (mandatory):**
- Document title → Pendo Pink (`#FF4081`)
- All bordered table header rows → `#FF4081` fill, white bold text
- All other styles inherit from the template (Arial, Heading 1/2/3)

**2b. Fill the post-launch CC/CD framework**

After the pre-launch sections, ask if the user also wants to fill in the CC/CD framework:
- V1/V2/V3 calibration phases (control level, agency level, status)
- Surface → Engage → Resolve evaluation plan
- Business KPI + UX KPI per module
- Primary owner, co-owner, runbook location

If yes, include it in the generated doc.

**2c. Upload to Drive (with confirmation)**

Ask the user for explicit confirmation before uploading:
> "Want me to upload this to the scoping checklist folder in Drive?"

Drive folder ID: `1Gh2I3RqvtMmUda0IrKLql6itrD4ixXnI`

Only upload on explicit "yes." Report the file ID or web link after upload.

**Approval gate:** confirm the user is happy with the doc before proceeding to Phase 3.

---

## Phase 3 — Set Up Data & Tracking

Run all four sub-steps, then present a combined summary for approval.

**3a. BQ event table**

Ask: which GCP project and dataset should the event table live in?
A coding agent generates and applies the `CREATE TABLE` DDL based on the experience type
and output fields from Phase 1. Follow the pattern of `gtm_engineering.sales_hygiene_event_log`
— event log with timestamp, user, action, result, and experience-specific fields.

**3b. Monitoring dashboard**

Ask: is there an existing dashboard template or repo to start from?
- If yes: a coding agent clones and adapts it.
- If no: a coding agent scaffolds a basic dashboard reading from the BQ event table,
  marked TODO for build-out. Reference the IAF HQ dashboard for structure.

**3c. Pendo tracking**

Based on experience type from Phase 1:
- **P4E** — employee-facing applications (surfaces UI in Glass or internal tools)
- **Agent Analytics** — AI agents only (no direct UI surface)

Identify specific events to track and document them in the scoping doc.

**3d. Calendar invites**

Create two calendar events (user as sole attendee — prompt them to add others):

| Meeting | Timing | Purpose |
|---|---|---|
| Code Review | 2 business days from today | Review PRs opened by coding agents |
| Team Review | 2 business days after Code Review | Review output with broader team |

Include project name, Jira project link, and GitHub repo in the invite descriptions.

**Approval gate:** present BQ schema, dashboard plan, Pendo tracking events, and
calendar invite details. Get explicit approval before creating anything.

---

## Phase 4 — Create Jira Tickets

Ask which Jira project to use (offer to list via `getVisibleJiraProjects`).

Extract discrete, independently-executable work items from the Phase 1 context.
For each ticket:
- **Title**: short imperative phrase
- **Description**: what to build and why, grounded in the source material
- **Acceptance criteria**: concrete, testable conditions for done
- **Source excerpt**: 2–4 lines from source material that motivate this ticket
- **Dependencies**: any tickets that must complete first
- **Suggested repo**: where the code likely lives (e.g. `rex-skills`, `project-rex`)

Present the full ticket list for review. **Do not create any tickets until explicitly approved.**

On approval: create each ticket via `createJiraIssue` (unassigned), link dependents via
`createIssueLink`, update calendar invite descriptions with Jira links.

---

## Phase 5 — Execute Coding Agents

Build a dependency graph. Independent tickets run in parallel; blocked tickets wait for
their dependencies to have open PRs. **Prioritize correctness over speed.**

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

Scoping doc: <google drive link>

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
