---
id: bq-query-governed
name: BQ Query (Governed)
description: Translates natural language into governed BigQuery SQL against pendolytics schemas and runs with RLS.
kit: engineering
tools: [query_bigquery, fetch_bq_schema, explain_sql]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# BQ Query (Governed)

## When to use
Any time an engineer, analyst, or ops user wants data from BigQuery without writing SQL by hand. Especially useful for the pendolytics, revos, pulse, and crm datasets in `pendo-reporting`. Built on the pendo-insight POC pattern.

## What it does
Takes a natural-language question, inspects the relevant schemas, drafts SQL, explains what it will do before running, executes under the caller's identity (so row-level security applies), and returns results with a visualization suggestion. Saves the query to a history so it can be re-run or shared.

## Inputs
- question: plain-English question.
- dataset_hint (optional): e.g. "pendolytics" or "revos". If omitted, REX picks based on the schema match.
- dry_run (optional, default false): if true, show the SQL and explanation without executing.

## Instructions (given to the agent)

1. Pick the dataset. Match keywords: "usage/DAU/adoption" → pendolytics, "signals/LIM" → revos, "health/pulse" → pulse, "accounts/opps" → crm.
2. Fetch schemas. `fetch_bq_schema(dataset)` returns table names, column names, and types. Never query columns you haven't confirmed exist.
3. Draft the SQL. Follow these rules:
   - Always use fully-qualified table names (`pendo-reporting.pendolytics.account_daily_usage`).
   - Always include a date filter. Default to last 30 days if the question is ambiguous.
   - Never `SELECT *` on large fact tables. Pick explicit columns.
   - Use parameterized queries for anything user-supplied (@start, @end, @account_id).
   - Add a `LIMIT 1000` on exploratory queries. Remove it only if the user explicitly asks for full output.
4. Explain the SQL in plain English in one short paragraph: "This counts distinct visitors per day for account X over the last 30 days, filtered to production events only."
5. If `dry_run` is true, stop here and show SQL + explanation + estimated bytes scanned. If not, execute with `query_bigquery(sql, params)`.
6. Return the result as a small table (first 20 rows) with a "View all" action, and suggest a chart type: line for time series, bar for categorical, number card for single scalar.
7. Save the query to the user's history with a friendly name. Next time they ask "like I did for Adobe last week" you can recall it.
8. If the query fails due to permissions, explain which dataset/view they lack access to and offer to file an IAM request to Lindy.

## Example

User: "daily active users for Adobe over the last 60 days"

REX: Picks pendolytics. Drafts `SELECT day, daily_active_visitors FROM pendo-reporting.pendolytics.account_daily_usage WHERE account_name = @name AND day >= DATE_SUB(CURRENT_DATE(), INTERVAL 60 DAY) ORDER BY day` with params `{name: 'Adobe Inc'}`. Explains, runs, returns 60 rows and a line chart.

## Output format
SQL block, one-paragraph explanation, results table (first 20 rows), chart suggestion, and Save-to-history / Re-run buttons.
