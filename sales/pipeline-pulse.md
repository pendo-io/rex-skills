---
id: pipeline-pulse
name: Pipeline Pulse
description: Generates a daily pipeline health digest for a rep or team.
kit: sales
tools: [query_salesforce, fetch_revos_signals, query_bigquery]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Pipeline Pulse

## When to use
Every weekday morning at 8am local time via the `pipeline-pulse-daily` automation. On-demand before a 1:1, deal review, or weekly forecast call.

## What it does
Snapshots the pipeline state and surfaces what actually changed since yesterday — stage moves, amount changes, close-date slips, new opps, closed-won/lost. Then ranks the top 5 opps that need attention today based on hygiene gaps, Gong silence, or RevOS signals.

## Inputs
- scope: "me" (default) or "team:<owner_email>" for an FLM rolling up a team.
- horizon (optional): "current_quarter" (default), "next_quarter", or "all_open".

## Instructions (given to the agent)

1. Determine the owner set. For "me", use the current user's email. For "team:<email>", fetch direct reports via `query_salesforce("SELECT Id, Email FROM User WHERE ManagerId = ...")`.
2. Pull the pipeline. `query_salesforce("SELECT Id, Name, StageName, Amount, CloseDate, Account.Name, Owner.Email, LastModifiedDate, Next_Steps__c, Primary_Competitor__c, LastActivityDate FROM Opportunity WHERE Owner.Email IN (...) AND IsClosed = false AND CloseDate >= ... AND CloseDate <= ...")`.
3. Pull yesterday's snapshot from `query_bigquery("SELECT * FROM pendo-reporting.revos.pipeline_snapshot WHERE snapshot_date = DATE_SUB(CURRENT_DATE(), INTERVAL 1 DAY) AND owner_email IN UNNEST(@owners)")`. If no snapshot exists, skip the diff section.
4. Compute what changed:
   - **Stage moves** — opps that changed StageName. Include old → new and amount.
   - **Amount changes** — opps where Amount changed more than 10%.
   - **Slipped close dates** — opps where CloseDate moved out.
   - **New opps** — created since yesterday.
   - **Closed** — won or lost since yesterday.
5. Rank the top 5 opps needing attention today. Scoring:
   - +3 if `Next_Steps__c` is empty or older than 14 days.
   - +2 if `LastActivityDate` is older than 10 days.
   - +2 if a RevOS signal fired in the last 3 days.
   - +3 if CloseDate is within 14 days and stage is not Commit.
   - +2 if `Primary_Competitor__c` is null on a stage-4+ opp.
6. Produce the digest — counts (open opps, total $, avg days to close, weighted pipeline), change section, top 5 list with the reason each made the list and the single best action to take.

## Example

User: "pipeline pulse"

REX: 23 open opps, $4.2M total, weighted $1.8M. Since yesterday: 2 stage moves (Adobe → Negotiation, Salesforce → Discovery), 1 slipped close date (Ford), 1 closed-won ($180k, Boeing). Top 5 to touch today: Adobe (pricing viewed, no next step in SFDC), Salesforce (Gong call Tuesday, still in Discovery), Oracle (stale 22 days, CloseDate in 10 days), etc.

## Output format
A dashboard-style summary — counts at top, "what changed" in the middle, ranked top-5 action list at the bottom. Each item in the top 5 gets one line of reason + one line of recommended action.
