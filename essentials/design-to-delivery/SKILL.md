---
name: design-to-delivery
description: >
  Turns Granola meeting transcripts from Glass/Rex design calls into Jira tickets,
  then spins up coding agents to execute the work and open PRs for human review.
  Use when user says "design to delivery", "transcript to tickets", "turn this call
  into work", "process this design call", "kick off work from this meeting", or wants
  to convert a design discussion into executable engineering work.
kit: essentials
audience: [swe, ops, exec]
workflow: [prep, automation]
tools: [query_granola_meetings, get_meeting_transcript, createJiraIssue, editJiraIssue, getVisibleJiraProjects, createIssueLink, addCommentToJiraIssue]
tags: [design, delivery, jira, agents, transcripts, automation]
source_kind: pendo-curated
author: jonathan.rice
version: 1.0.0
created: 2026-05-13
---

# Design to Delivery

Turn a Glass/Rex design call into shipped code: fetch the transcript, extract discrete
work items, create Jira tickets, execute coding agents, and open PRs — with a human
review gate before any code is written.

See [AGENT_PROMPT.md](AGENT_PROMPT.md) for the coding agent briefing template.

## Workflow

### Step 1 — Identify the meeting(s)

Ask the user which meeting(s) to process. Accept a name, keyword, or "my last design call."
Use `query_granola_meetings` to search, then `get_meeting_transcript` to fetch the full
transcript. If multiple meetings match, show a list and let the user pick.

### Step 2 — Extract proposed tickets

Analyze the transcript and extract discrete, independently-executable work items. For each:
- **Title**: short imperative phrase
- **Description**: what needs to be built and why, grounded in the transcript
- **Acceptance criteria**: concrete, testable conditions for done
- **Transcript excerpt**: the 2–4 lines from the call that motivate this ticket
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
- Include description, acceptance criteria, and transcript excerpt
- Link dependent tickets via `createIssueLink`

### Step 6 — Execute coding agents

Build a dependency graph. Tickets with no blockers run in parallel. Blocked tickets wait
for their dependencies to have open PRs before spawning. **Prioritize correctness over speed.**

For each ticket, spawn an Agent with `isolation: "worktree"` and pass:
- Full ticket description and acceptance criteria
- Transcript excerpt that motivated the work
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
