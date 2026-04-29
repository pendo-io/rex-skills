---
id: weekly-digest
name: Weekly Digest
description: Scans Slack, Calendar, Jira, and Linear for the past week and produces a personal digest.
kit: essentials
tools: [search_slack, fetch_calendar_events, fetch_jira_issues, fetch_linear_issues, fetch_iaf_metrics]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Weekly Digest

## When to use
Every Friday at 4pm local time via the `weekly-digest-friday` automation, or on-demand when a user says "what happened this week" or "give me my week". Also useful Monday morning to review the week just past before planning the week ahead.

## What it does
Aggregates the user's week across Slack mentions, meetings attended, Jira/Linear tickets moved, and PRs shipped. Produces a concise narrative digest — not a list dump. Highlights wins, commitments made, and anything that fell through the cracks.

## Inputs
- user_email: the user to digest (default: current user).
- start_date (optional): ISO date, default is last Monday.
- end_date (optional): ISO date, default is today.

## Instructions (given to the agent)

1. Compute the date range. Default to last Monday 00:00 through now.
2. In parallel, fetch:
   - `search_slack(user=user_email, mentioned=true, since=start_date)` — threads where the user was tagged.
   - `fetch_calendar_events(user_email, start_date, end_date)` — meetings attended (filter out declined).
   - `fetch_jira_issues(assignee=user_email, updated_since=start_date)` — Jira activity.
   - `fetch_linear_issues(assignee=user_email, updated_since=start_date)` — Linear activity.
3. Build a three-part digest:
   - **Wins** — 2 to 4 bullets. Something shipped, closed, or decided. Must be concrete.
   - **In flight** — 2 to 4 bullets. Work that moved but isn't done. Include the next milestone.
   - **Dropped or at risk** — anything where the user was asked something and hasn't responded, or a ticket that went stale, or a meeting where a commitment was made with no follow-up.
4. End with a "commitments log" — every "I'll do X by Y" found in Slack or meeting notes, with a checkbox.
5. Keep the total digest under 400 words. Prose first, bullets second.
6. If the user had a PTO day or out-of-office block in their calendar, note it and exclude that day from "dropped".

## Example

User: "give me my week"

REX: Fetches the week's activity. Reports: Wins (shipped IAF-67 to prod, closed 3 Linear tickets, approved Scot's logo); In flight (SAVER graduation to parallel — blocked on BQ log review, Confluence docs for IAF-70); Dropped (Cassie asked for a shadow summary on Tuesday — no reply); Commitments (send Scot logo specs by Thursday — DONE; reply to Cassie — OPEN).

## Output format
Markdown with three sections (Wins, In flight, Dropped or at risk) plus a checklist of commitments. Prose-first, no dense tables.
