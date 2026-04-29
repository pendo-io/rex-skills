---
id: forecast-variance-explanation
name: Forecast Variance Explanation
description: Explains why the current-quarter forecast moved — new wins, slipped deals, churn.
kit: finance
tools: [query_salesforce, query_bigquery, fetch_retos_churn_risk]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Forecast Variance Explanation

## When to use
When the weekly forecast snapshot shows movement and FP&A needs to explain why. Triggered by the `forecast-variance-weekly` automation every Wednesday after the forecast call. On-demand before a board update or a finance review.

## What it does
Diffs the current forecast against last week's snapshot, attributes the variance to specific opportunities and churn events, buckets the drivers (new wins, slipped deals, amount changes, churn, upsell), and produces a plain-English explanation suitable for a slide or email.

## Inputs
- quarter (optional): "current" (default).
- compare_to (optional): ISO week (YYYY-Www), default last week.
- scope (optional): "company" (default), "segment:enterprise", "segment:mid-market", or "team:<manager_email>".

## Instructions (given to the agent)

1. Fetch the current forecast and the comparison snapshot. Both live in `pendo-reporting.finance.forecast_snapshot`.
2. Compute the variance: `current_commit - prior_commit`, `current_best_case - prior_best_case`, `current_pipeline - prior_pipeline`.
3. Attribute the variance. Break it into buckets:
   - **New wins** — opps closed-won since last snapshot. Sum amount.
   - **Closed-lost** — opps closed-lost. Sum amount. Include the reason code.
   - **Slipped out of quarter** — opps whose CloseDate moved beyond this quarter. Sum amount, list top 3 by amount.
   - **Slipped into quarter** — opps that moved into this quarter. Sum amount, list top 3.
   - **Amount changes** — opps where Amount changed more than 10%. Net the deltas.
   - **Stage downgrade** — opps that went backward in stage (e.g. Commit → Best Case). Sum amount.
   - **Stage upgrade** — opps that moved forward.
   - **Churn** — from RetOs, any account whose renewal was downgraded or cancelled. Net impact.
   - **Upsell** — any account whose renewal was upsized. Net impact.
4. Produce the narrative:
   - **Headline** — one sentence. "Commit moved from $12.4M to $11.8M this week, a net decrease of $600k."
   - **Drivers** — 3 to 5 bullets, ordered by absolute impact. Each with dollar amount and reason.
   - **Notable opps** — up to 5 named opps with the biggest individual impact, each with a one-line explanation.
   - **Risk ahead** — any opps still in the forecast but showing warning signs (stale, no Next_Steps__c, competitor flagged) that could slip next week.
5. Round dollars to the nearest $10k above $100k, to the nearest $1k below. Never show cents.
6. End with a link to the full opp-level variance table.

## Example

User: "explain forecast variance"

REX: Commit moved from $12.4M to $11.8M (-$600k). Drivers: 2 closed-wins (+$340k), 1 slipped-out (Oracle, -$800k, CloseDate moved to Q3), 1 upsell (Adobe, +$120k), 2 amount changes (net -$80k), 1 churn (LegacyCo, -$180k). Notable: Oracle slipped because CISO review ran long; IBM upgraded stage from Best Case to Commit. Risk ahead: Ford (no activity in 18 days, competitor WalkMe newly flagged).

## Output format
Headline, drivers (bulleted), notable opps, risk ahead section, and a link to the opp-level detail table. Target length 200-300 words.
