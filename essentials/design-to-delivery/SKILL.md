---
name: design-to-delivery
description: >
  End-to-end orchestrator that turns Glass/Rex AI experience design context into shipped
  code. Gathers context via interview or existing sources, generates a Pendo-branded
  scoping doc, sets up BQ + monitoring infrastructure, creates Jira tickets, and spawns
  coding agents that write code, open PRs, and get reviewed before human sign-off.
  Use when user says "design to delivery", "transcript to tickets", "turn this call into
  work", "process this design call", "kick off work from this meeting", or wants to
  convert a design discussion into executable engineering work.
---

# Design to Delivery — Orchestrator

Six-phase workflow. The skill handles all human-facing phases and delegates execution
to specialized agents. Every phase ends with an approval gate.

Reference files:
- [INTERVIEW_GUIDE.md](INTERVIEW_GUIDE.md) — 9-section interview question guide
- [CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md) — field definitions and CC/CD framework
- [agents/scoping-doc-agent.md](agents/scoping-doc-agent.md) — doc generation agent
- [agents/infrastructure-agent.md](agents/infrastructure-agent.md) — BQ + dashboard agent
- [agents/code-agent.md](agents/code-agent.md) — per-ticket coding agent
- [agents/code-reviewer-agent.md](agents/code-reviewer-agent.md) — PR review agent

---

## Context Block

Build this object progressively across phases. Pass the relevant fields to each agent.

```
{
  project_name:          string
  project_description:   string
  modules:               Module[]        // from Phase 1 interview
  jira_project:          string          // from Phase 1 questions
  repo_name:             string          // from Phase 1 questions
  repo_url:              string          // from Phase 1 questions
  gcp_project:           string          // from Phase 1 questions
  bq_dataset:            string          // from Phase 1 questions
  dashboard_template:    string | null   // from Phase 1 questions
  pendo_tracking_type:   "P4E" | "Agent Analytics"  // derived from experience type
  scoping_doc_url:       string          // filled after Phase 2
  scoping_doc_path:      string          // filled after Phase 2
  bq_table_name:         string          // filled after Phase 3
  dashboard_url:         string          // filled after Phase 3
  tickets:               Ticket[]        // filled after Phase 4
}
```

---

## Phase 1 — Gather Context

Ask the user where design context lives:

| Source | How to ingest |
|---|---|
| Granola recording | `query_granola_meetings` → `get_meeting_transcript` |
| Google Doc / Slides / Sheet | Read via Drive MCP using the URL provided |
| Multiple sources | Combine before proceeding |
| Nothing yet | Run the structured interview below |

**If no context exists — invoke `grill-with-docs`:**

Pass [INTERVIEW_GUIDE.md](INTERVIEW_GUIDE.md) and [CHECKLIST_FIELDS.md](CHECKLIST_FIELDS.md)
as the domain documentation. The session covers all 9 sections of the Module Definition
Framework, one at a time, challenging terminology against Pendo's domain model and
documenting resolved decisions inline.

Start with:
> "Let's scope this out properly. I'll walk you through the Module Definition Framework
> section by section — usually 5–10 minutes per module.
>
> First: **what's the name of this project**, and give me a one-sentence description?"

Ask: how many modules? (default 1). Complete all 9 sections per module before the next.

**Also collect during Phase 1:**
- Which Jira project? (`getVisibleJiraProjects` to list options)
- Which repo? (existing or new)
- Which GCP project + BQ dataset for the event table?
- Is there an existing dashboard template/repo to start from?

**Derive:** `pendo_tracking_type` from experience type:
- Action Driver or Intelligence Provider → `P4E`
- Pure AI agent with no UI surface → `Agent Analytics`

**Approval gate:** present a summary of the full context block and confirm before Phase 2.

---

## Phase 2 — Generate Scoping Document

Spawn the scoping doc agent. This phase is **sequential** — Phase 3 does not start
until the user has confirmed the finalized document.

```
Agent(
  description: "Generate scoping doc for {project_name}",
  prompt: <contents of agents/scoping-doc-agent.md> + context block (all Phase 1 fields),
  isolation: "worktree"
)
```

The agent returns: `scoping_doc_url`, `scoping_doc_path`.

Present the Drive link to the user. Ask them to review the doc and confirm it's accurate
before proceeding. They can edit the doc directly in Drive.

**Approval gate:** user confirms the scoping doc is finalized.

---

## Phase 3 — Set Up Infrastructure

Spawn the infrastructure agent. Pass the full context block **including the finalized
scoping doc** (`scoping_doc_url` + `scoping_doc_path`).

```
Agent(
  description: "Set up BQ + dashboard for {project_name}",
  prompt: <contents of agents/infrastructure-agent.md> + full context block,
  isolation: "worktree"
)
```

The agent returns: `bq_table_name`, `dashboard_url` (or scaffold path if no template).

Also create two calendar invites (user as sole attendee — prompt them to add others):

| Meeting | Timing | Purpose |
|---|---|---|
| Code Review | 2 business days from today | Review PRs and code reviewer feedback |
| Team Review | 2 business days after Code Review | Review output with broader team |

**Approval gate:** present BQ table name, dashboard link/scaffold, Pendo tracking type,
and calendar invites. Confirm before Phase 4.

---

## Phase 4 — Create Jira Tickets

Using the finalized context block (modules + scoping doc), extract discrete work items.
For each ticket:

| Field | Description |
|---|---|
| Title | Short imperative phrase |
| Description | What to build and why, grounded in the scoping doc |
| Acceptance criteria | Concrete, testable conditions for done |
| Source excerpt | 2–4 lines from scoping doc or transcript |
| Dependencies | Other tickets that must complete first |
| Repo | Where the code lives |

Present the full ticket list. User can approve, edit, remove, or adjust dependencies.
**Do not create tickets until explicitly approved.**

On approval:
- Create each ticket via `createJiraIssue` (unassigned initially)
- Link dependents via `createIssueLink`
- Update calendar invite descriptions with Jira links + repo URL
- Add tickets to context block

---

## Phase 5 — Execute Coding Agents

Build a dependency graph from the tickets. Spawn agents for all unblocked tickets in
parallel. As each completes and opens a PR, check if it unblocks any waiting tickets
and spawn those next. **Prioritize correctness over speed.**

For each ticket, spawn:

```
Agent(
  description: "Implement: {ticket_title}",
  prompt: <contents of agents/code-agent.md> + {
    ticket,
    context_block,
    dependency_outputs: [...] // file paths + exported names from completed dependencies
  },
  isolation: "worktree"
)
```

Each agent returns a draft PR URL. Do not wait for all agents before proceeding to Phase 6.

---

## Phase 6 — Code Review

As each PR is opened, immediately spawn a code reviewer agent:

```
Agent(
  description: "Review PR for: {ticket_title}",
  prompt: <contents of agents/code-reviewer-agent.md> + {
    ticket,
    pr_url,
    context_block
  }
)
```

Each reviewer returns a structured verdict: **Approved**, **Approved with suggestions**,
or **Changes requested** — with specific, actionable notes.

Once all reviews are complete, present results to the user:

```
Code Review Summary — {project_name}

[PROJ-123] Title
  PR: <link>
  Verdict: Approved with suggestions
  Notes: ...

[PROJ-124] Title
  PR: <link>
  Verdict: Changes requested
  Notes: ...
```

After human review, update each Jira ticket:
- Add PR link via `addCommentToJiraIssue`
- Assign ticket to user via `editJiraIssue`

---

## Final Summary

```
Design to Delivery — Complete

Scoping doc:   <drive link>
BQ table:      <project.dataset.table>
Dashboard:     <url or "scaffold at <path> — TODO">
Calendar:      Code Review <date> | Team Review <date>

Tickets:
  [PROJ-123] Title — <jira link> | PR: <link> | ✅ Approved
  [PROJ-124] Title — <jira link> | PR: <link> | ⚠️  Changes requested

Next steps:
  - Address any "Changes requested" items before the Code Review meeting
  - Add attendees to calendar invites
  - Dashboard scaffold needs build-out (if no template was provided)
```
