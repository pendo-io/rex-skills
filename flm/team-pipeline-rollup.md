---
id: team-pipeline-rollup
name: Team Pipeline Rollup
description: Rolls up a team's pipeline with rep-by-rep signal counts.
kit: flm
tools: [query_salesforce, fetch_revos_signals, query_bigquery]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Team Pipeline Rollup

## When to use
Before a team forecast call, for a quarterly review, or when an FLM needs to answer "how's the team doing" in under 60 seconds. Also triggered by the `team-rollup-monday` automation on Monday mornings for all FLMs.

## What it does
Rolls up a team's pipeline — total open $, weighted $, stage distribution, close-date distribution, hygiene score — and breaks it out rep-by-rep. Flags reps who are light on pipeline, heavy on stale deals, or absent from Gong. One screen, optimized for a forecast call.

## Inputs
- manager_email: the FLM's Pendo email. Default is current user.
- quarter (optional): "current" (default) or "next".

## Instructions (given to the agent)

1. Resolve direct reports. `query_salesforce("SELECT Id, Email, Name FROM User WHERE Manager.Email = @email AND IsActive = true")`.
2. For each rep, fetch in parallel:
   - Open opps: `query_salesforce("SELECT Id, Name, StageName, Amount, CloseDate, LastActivityDate, Next_Steps__c FROM Opportunity WHERE Owner.Email = @email AND CloseDate >= @q_start AND CloseDate <= @q_end AND IsClosed = false")`.
   - Signals: `fetch_revos_signals(owner_email=email, window_days=7)`.
   - Gong activity count: `query_bigquery("SELECT COUNT(*) FROM pendo-reporting.revos.gong_calls WHERE host_email = @email AND call_date >= CURRENT_DATE() - 14")`.
3. Compute team totals: open $, weighted $ (apply stage probabilities), count commit-stage, avg days in stage, count opps closing this quarter.
4. Compute hygiene score per rep (0-100):
   - +30 if every open opp has non-empty Next_Steps__c.
   - +25 if no opp is stale (no activity in 14 days).
   - +20 if every opp has a Close Date in the current or next quarter.
   - +15 if the rep logged more than 3 Gong calls in the last 14 days.
   - +10 if every stage-4+ opp has a Primary_Competitor__c.
5. Return:
   - **Team totals** — four numbers (open $, weighted $, count commits, hygiene score avg).
   - **Stage distribution** — counts per stage, visualized as a small bar.
   - **Rep-by-rep table** — name, open $, weighted $, commits, hygiene score, top flag (if any). Sort by weighted $ descending.
   - **Flags** — list reps with hygiene score below 60 or zero Gong activity in 14 days. One line per flag.
6. Keep it dense but readable. Target a forecast-call use case — the FLM should be able to scan it while on a Zoom.

## Example

User: "team rollup"

REX: 6 reps on Emma Chen's team. Team open $8.4M, weighted $3.6M, 11 commit-stage opps, avg hygiene 72. Top reps: Mike Torres $2.1M weighted, Rachel Kim $980k. Flags: Jordan Lee — hygiene 48 (5 opps without Next_Steps__c); Sam Patel — zero Gong calls in 14 days.

## Output format
Dashboard-style: team totals at top, stage distribution mini-chart, sortable rep table, flags list at bottom.
