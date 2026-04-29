---
id: churn-risk-investigation
name: Churn Risk Investigation
description: Uses RetOs signals, Pulse health, and product usage drop to assess account churn risk.
kit: csm
tools: [fetch_retos_churn_risk, query_salesforce, query_bigquery, list_gong_calls]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Churn Risk Investigation

## When to use
A RetOs alert fires on an account, or renewal is within 90 days and the CSM wants a read. Also used in quarterly book-of-business reviews. On-demand when a CSM says "how is Adobe doing" or "churn risk for my book".

## What it does
Pulls the RetOs churn risk score and contributing factors, the Pulse health score history, product usage trends (DAU/MAU, feature adoption), Gong sentiment from recent calls, and open support tickets. Synthesizes into a risk grade (Green / Yellow / Red) with the top 3 drivers and a recommended play.

## Inputs
- account_name_or_id: SFDC account name or Id. Also accepts "my book" to run across the CSM's entire portfolio.
- lookback_days (optional): default 90.

## Instructions (given to the agent)

1. Resolve the account. If "my book", fetch all accounts where `Success_Manager__c = current_user`.
2. For each account, fetch in parallel:
   - `fetch_retos_churn_risk(account_id)` — RetOs score and factor breakdown.
   - `query_bigquery("SELECT pulse_score, score_date FROM pendo-reporting.pulse.health_history WHERE account_id = @id AND score_date >= @since ORDER BY score_date DESC")` — Pulse trend.
   - `query_bigquery("SELECT day, daily_active_visitors, feature_adoption_rate FROM pendo-reporting.pendolytics.account_daily_usage WHERE account_id = @id AND day >= @since")` — product usage.
   - `query_salesforce("SELECT Id, Subject, Priority, Status, CreatedDate FROM Case WHERE AccountId = ... AND CreatedDate >= LAST_N_DAYS:{lookback_days}")` — support cases.
   - `list_gong_calls(account_id, since=lookback_days)` — recent calls.
3. Grade the risk:
   - **Red** — RetOs score > 70, OR Pulse dropped more than 25 points in 90 days, OR DAU dropped more than 40% AND renewal within 90 days.
   - **Yellow** — RetOs 40-70, OR Pulse dropped 10-25 points, OR DAU dropped 20-40%, OR more than 3 P1 cases in 60 days.
   - **Green** — none of the above.
4. Identify the top 3 risk drivers in plain English. Avoid jargon. "Daily active users dropped from 340 to 190 over the last 60 days" beats "DAU regression detected".
5. Recommend one play per grade:
   - Red: Escalate — schedule an exec sync with the account sponsor this week, loop in the RD.
   - Yellow: Re-engage — schedule a value-realization review, share a relevant case study, audit guide adoption.
   - Green: Nurture — send a product-update summary, suggest one new feature to try.
6. If running across a whole book, return a sorted table (Red first) with one-line summaries per account and let the user drill into any row.

## Example

User: "churn risk for Adobe"

REX: RetOs score 78 (Red). Pulse dropped 112 to 74 over 60 days. DAU dropped 46%. Top drivers: power-user Sarah Chen stopped logging in March 12; adoption of guides paused in February; two P1 support cases on session replay. Recommends exec sync this week with RD Mike. Suggests specific outreach: "Check in with Sarah — we've lost our champion."

## Output format
A risk card (Red / Yellow / Green grade at top, 3 drivers, 1 play) for single accounts, or a sorted book-view table for portfolio runs.
