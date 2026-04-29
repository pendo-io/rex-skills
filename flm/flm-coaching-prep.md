---
id: flm-coaching-prep
name: FLM Coaching Prep
description: Aggregates a rep's Gong calls, pipeline, and signals for 1:1 prep.
kit: flm
tools: [list_gong_calls, query_salesforce, fetch_revos_signals, query_bigquery]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# FLM Coaching Prep

## When to use
Before a 1:1 with a direct report. Triggered automatically by the `flm-oneonone-lookahead` automation 30 minutes before any calendar event titled "1:1" with a direct report. Also on-demand as "prep for my 1:1 with Mike".

## What it does
Assembles a coaching prep sheet — pipeline state, deal movement this week, Gong call themes (competitors, objections, talk ratio), stuck deals, and the single best coaching topic to focus on. Keeps it to one screen.

## Inputs
- rep_email: the rep's Pendo email.
- window_days (optional): default 7.

## Instructions (given to the agent)

1. Confirm the rep is a direct report via `query_salesforce("SELECT Id, Email, Manager.Email FROM User WHERE Email = ...")`.
2. Fetch in parallel:
   - `query_salesforce(...)` — rep's open opps with StageName, Amount, CloseDate, Next_Steps__c, LastActivityDate, Primary_Competitor__c.
   - `list_gong_calls(user=rep_email, since=window_days)` — rep's calls with talk ratio, sentiment, competitor mentions.
   - `fetch_revos_signals(owner_email=rep_email, window_days)` — signals the rep has on their book.
   - `query_bigquery(...)` for rep activity — calls per day, emails per day, meetings held.
3. Build the prep sheet:
   - **Pipeline snapshot** — total open $, weighted $, count of commit-stage opps, avg days in current stage.
   - **This week's movement** — stage moves, slipped close dates, closed-won, closed-lost.
   - **Coaching themes** — patterns across Gong calls. Examples: "talk ratio is 62%, high for discovery calls" or "competitor WalkMe mentioned on 3 of 5 calls — does rep have the battle card down?".
   - **Stuck deals** — opps in the same stage for more than 30 days with no activity in 10. List up to 3, each with a suggested un-stick question.
   - **Top topic for this 1:1** — one concrete topic, the why, and a specific coaching question to ask.
4. Keep it under 300 words. Prose-first. Prefer one strong recommendation over five weak ones.
5. If the rep is on PTO this week or is new to the role (less than 90 days), adapt — skip volume critique, focus on ramp metrics.

## Example

User: "prep for 1:1 with Mike Torres"

REX: Pulls Mike's book. Pipeline: $1.2M open, $480k weighted, 3 commits. This week: 1 closed-won ($120k Ford), 1 slipped close date (Oracle), 2 stage moves. Gong: talk ratio 58% across 6 calls, competitor WalkMe on 3 calls. Stuck: Adobe (Discovery for 45 days, no activity in 14). Top topic: "Adobe has gone quiet — let's role-play the re-engagement call."

## Output format
Five sections (Pipeline snapshot, Movement, Coaching themes, Stuck deals, Top topic) in under 300 words.
