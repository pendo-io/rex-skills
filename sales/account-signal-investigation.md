---
id: account-signal-investigation
name: Account Signal Investigation
description: Given a SFDC account, pulls RevOS signals, LIM events, and Gong mentions, then drafts outreach.
kit: sales
tools: [query_salesforce, fetch_revos_signals, query_bigquery, list_gong_calls, draft_email]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Account Signal Investigation

## When to use
An AE gets a RevOS alert, a hot inbound, or wants to decide whether to prioritize an account this week. Also used during weekly account planning to pick the top 3 accounts to touch.

## What it does
Pulls every recent signal for the account — RevOS account signals, Marketo LIM events, Gong call mentions, SFDC activity — and synthesizes them into a one-page brief with a recommended next move. Optionally drafts the outreach.

## Inputs
- account_name_or_id: SFDC account name or 18-char Id.
- window_days (optional): lookback window, default 30.
- draft (optional, default true): if true, produce a draft email. If false, stop at the brief.

## Instructions (given to the agent)

1. Resolve the account. Call `query_salesforce("SELECT Id, Name, Owner.Name, Account_Tier__c, Arr__c, Renewal_Date__c FROM Account WHERE ... LIMIT 1")`. If the name is ambiguous, show the top 3 matches and ask.
2. In parallel:
   - `fetch_revos_signals(account_id, window_days)` — RevOS `account_signals` table.
   - `query_bigquery("SELECT * FROM pendo-reporting.revos.marketo_sales_insight WHERE AccountId = @id AND EventDate >= @since", {id, since})` — LIM (pricing page, demo requests, webinars, trials).
   - `list_gong_calls(account_id, since=window_days)` — recent calls and emails.
   - `query_salesforce("SELECT Id, Subject, ActivityDate, OwnerId FROM Task WHERE AccountId = ... AND ActivityDate >= LAST_N_DAYS:{window_days}")` — SFDC activity log.
3. Rank signals by recency and strength. A pricing page visit in the last 48h beats a two-week-old webinar attendance.
4. Produce a one-page brief:
   - **Account snapshot** — Name, tier, ARR, renewal date, owner. One line.
   - **What's happening** — the 3 to 5 strongest signals in plain English with dates and who triggered them.
   - **Champions and detractors** — anyone named on a Gong call, tagged as a champion in UserGems, or flagged in LIM.
   - **Recommended move** — one sentence. "Send Sarah Chen a note about the Adobe case study — she viewed pricing twice this week and was on the March 3 webinar."
5. If `draft=true`, produce a 120-word email in the AE's voice, referencing the strongest 1-2 signals naturally. Do not mention the signals as signals ("I saw you visited our pricing page" is creepy). Instead ground the email in business context the signal implies.
6. Output the draft with a one-click Send-via-Outreach action and a Regenerate action.

## Example

User: "investigate Adobe"

REX: Resolves to Adobe Inc. Pulls 4 LIM events (2 pricing page, 1 demo request, 1 G2 comparison), 2 Gong calls with Sarah Chen, no SFDC tasks in 14 days. Recommends reaching out to Sarah with a case study angle. Drafts a 120-word email and shows Send/Regenerate.

## Output format
Four sections (Snapshot, What's happening, Champions/Detractors, Recommended move) plus an optional drafted email with actions.
