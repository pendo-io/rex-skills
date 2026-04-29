---
id: jira-ticket-triage
name: Jira Ticket Triage
description: Summarizes a Jira ticket and suggests the next action based on status, comments, and linked PRs.
kit: essentials
tools: [fetch_jira_issue, search_slack, fetch_github_pr]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Jira Ticket Triage

## When to use
User pastes a Jira URL or ticket key (e.g. IAF-67), or says "what's next on IAF-67". Also used by the morning digest automation to surface stale tickets.

## What it does
Fetches the ticket, its comments, linked PRs, and any recent Slack mentions. Returns a tight summary — what the ticket asks for, current state, blockers, and the single best next action for the current user.

## Inputs
- ticket_key_or_url: e.g. IAF-67 or https://pendo.atlassian.net/browse/IAF-67
- perspective (optional): "me" (default, triage from the current user's POV) or "owner" (triage from the assignee's POV).

## Instructions (given to the agent)

1. Normalize the input to a ticket key. If a URL, strip to key. If ambiguous, ask the user to clarify.
2. Call `fetch_jira_issue(key, include_comments=true, include_links=true)`.
3. If the ticket has linked PRs, call `fetch_github_pr(url)` for each to check review state, CI status, and approval count.
4. Call `search_slack(query=f"{key}", days=7)` to find recent mentions — often the real decision happens in Slack, not in Jira comments.
5. Produce the triage output:
   - **Ask** — one sentence paraphrase of what the ticket is actually asking for.
   - **Status** — current Jira status, assignee, last update timestamp, and a plain-English state ("waiting on review", "blocked on SFDC perm set", "ready to deploy").
   - **Blockers** — bulleted list of anything blocking progress, each with the owner.
   - **Next action** — exactly one recommended next step for the user. Be specific: "Ping Lindy in #iaf-platform about the OAuth redirect URI" is good. "Follow up" is not.
6. If the ticket is Done, say so and skip the Next action.
7. If the ticket is stale (>14 days without update), flag it.

## Example

User: "triage IAF-67"

REX: Fetches IAF-67, finds it's about on-leave FLM escalation + past-due CE filter for SHB. Linked PR is merged. Status is In Progress, deployed to rev 00075, shadow mode. Next action: "Monitor SAVER shadow logs for 48h, then graduate to parallel. Reply to Cassie in #sales-hygiene-bot with the shadow summary."

## Output format
Four sections — Ask, Status, Blockers, Next action. Under 200 words unless the ticket is unusually complex.
