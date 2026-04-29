---
id: champion-tracking
name: Champion Tracking
description: Given an account, finds UserGems champion-moved data and Pulse contacts.
kit: csm
tools: [query_salesforce, query_bigquery, fetch_usergems_moves]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Champion Tracking

## When to use
When a CSM or AE hears a champion left, when a RetOs alert flags contact churn, or as part of the monthly book review. Also triggered by the `usergems-daily-sync` automation when a tracked contact changes companies.

## What it does
Identifies who the Pendo champions and key users are at an account, pulls UserGems job-change data to see if any have moved, and surfaces where they went so the AE team can follow them into the new company. Also flags contacts at the current account whose usage dropped, indicating a potential fading-champion risk.

## Inputs
- account_name_or_id: SFDC account.
- window_days (optional): default 180 for job-change lookback.

## Instructions (given to the agent)

1. Resolve the account.
2. Build the champion list. A champion is any Contact where one of:
   - `Is_Champion__c = true` in SFDC.
   - Tagged as "power user" in Pendo (top 5% of monthly logins for the account).
   - Attended more than 3 Gong calls with Pendo in the last year.
   - Appeared as a key stakeholder in QBR notes.
3. Fetch UserGems moves. `fetch_usergems_moves(account_id, window_days)`. Important — always use `CreatedDate`, never `LastModifiedDate`, when filtering UserGems SOQL.
4. For each champion:
   - If they moved to a new company, return: name, old title, new company, new title, move date, and whether the new company is in Pendo's TAM (check against the `pendo-reporting.crm.account_tam` table).
   - If they still at the account, check Pendo usage. Query `account_daily_usage` for their visitor_id. If last login is more than 30 days ago, flag as "fading".
5. Return two sections:
   - **Champions who moved** — table with name, old company → new company, new title, new company in TAM yes/no, and a one-click "Create lead in SFDC" action.
   - **Champions at risk** — contacts still at the account but with fading engagement, with last-login date.
6. Also return one synthesized recommendation per moved champion. If the new company is in TAM, draft a three-line warm-intro message for the AE who owns that territory.

## Example

User: "champions for Adobe"

REX: Finds 4 champions. 1 moved (Sarah Chen, VP Product → Head of Product at Figma, moved March 18; Figma is in TAM, owned by AE Mike Torres). 1 fading (Raj Patel, hasn't logged in 47 days). Drafts warm intro for Mike to send Sarah at Figma. Recommends CSM check in with Raj this week.

## Output format
Two sections (Champions who moved, Champions at risk) plus drafted warm intros for any TAM-hit moves.
