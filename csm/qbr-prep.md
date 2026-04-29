---
id: qbr-prep
name: QBR Prep
description: Prepares a QBR deck outline from Pendo product usage and SFDC touchpoints.
kit: csm
tools: [query_bigquery, query_salesforce, list_gong_calls, fetch_retos_churn_risk, generate_slides_outline]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# QBR Prep

## When to use
Two weeks before a scheduled QBR, triggered by the `qbr-prep-lookahead` automation when a QBR meeting is found on the CSM's calendar. Also on-demand as "prep the Adobe QBR".

## What it does
Assembles the full QBR narrative — usage and adoption metrics, business outcomes tied to Pendo, open support topics, roadmap items aligned to the customer's goals, and expansion opportunities. Produces a slide-by-slide outline that can be imported into the Pendo QBR template in Slides.

## Inputs
- account_name_or_id: SFDC account.
- qbr_date (optional): defaults to the next QBR on the CSM's calendar with the account.
- quarter (optional): "previous" (default, looks back at the quarter just ended) or "trailing" (last 90 days).

## Instructions (given to the agent)

1. Resolve the account and quarter window. If quarter is "previous", use the company's fiscal quarter that just ended.
2. Fetch in parallel:
   - `query_bigquery(...)` on `pendo-reporting.pendolytics.account_daily_usage` for DAU/MAU, feature adoption, guide views, NPS trend.
   - `query_bigquery(...)` on `pendo-reporting.pulse.health_history` for health trend.
   - `query_salesforce` for open cases, closed-in-quarter cases, and renewal date.
   - `list_gong_calls(account_id, since=quarter_start)` for themes and commitments.
   - `fetch_retos_churn_risk(account_id)` for the current risk snapshot.
3. Organize into the standard Pendo QBR deck outline:
   - Slide 1: Cover (account name, quarter, CSM, sponsor, date).
   - Slide 2: Executive summary — 3 bullets on the quarter.
   - Slide 3: Goals revisit — last QBR's goals and status (from a previous QBR if available in Drive).
   - Slide 4: Business outcomes — what the customer achieved with Pendo, tied to specific product usage.
   - Slide 5: Adoption metrics — DAU/MAU trend, top features, guide completion rate.
   - Slide 6: Support and engagement — case volume, CSAT, Gong call themes.
   - Slide 7: Product roadmap alignment — 2-3 upcoming releases relevant to their goals.
   - Slide 8: Expansion opportunities — seat growth, new product lines, enterprise upgrades.
   - Slide 9: Goals for next quarter — 3 SMART goals.
   - Slide 10: Appendix — raw data.
4. For each slide, provide: title, 3-5 talking points, the specific data to visualize, and a link to the query or source.
5. Flag any slide that you couldn't fully populate with a "needs human input" tag — e.g. if previous QBR goals aren't findable, tell the CSM to paste them.
6. Offer a one-click "Create in Slides" action that instantiates the deck from the Pendo QBR template.

## Example

User: "prep QBR for Adobe"

REX: Finds QBR scheduled May 2 with Sarah Chen. Assembles outline: exec summary (Pulse up 12, launched in 3 new regions, 2 expansion conversations), outcomes (reduced time-to-first-value from 22 to 11 days), adoption (DAU 340, up 18%), expansion (identified 40 more seats in APAC), goals for Q3 (hit 80% adoption in APAC, roll out guides v2). Flags "needs previous QBR goals from Drive".

## Output format
A 10-slide outline with titles, talking points, data callouts, and a "Create in Slides" action.
