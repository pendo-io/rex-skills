---
id: ramp-expense-triage
name: Ramp Expense Triage
description: Triages unmatched Ramp expenses against the expected vendor list.
kit: finance
tools: [fetch_ramp_transactions, query_bigquery, fetch_netsuite_vendors, draft_email]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Ramp Expense Triage

## When to use
Every weekday morning via the `ramp-triage-morning` automation. On-demand when an FP&A analyst sees a pile of unmatched transactions in Ramp and wants help sorting them. Also at month-end close.

## What it does
Pulls Ramp transactions without a matched GL code, cross-references against the expected vendor list (from NetSuite), auto-proposes a GL code + cost center for each, and flags truly unknown vendors for human review. Produces a clean queue the analyst can approve in one pass.

## Inputs
- window_days (optional): default 7.
- min_amount (optional): ignore transactions below this threshold, default $25.

## Instructions (given to the agent)

1. Pull unmatched Ramp transactions. `fetch_ramp_transactions(status="needs_review", since=window_days, min_amount=min_amount)`.
2. Fetch the vendor master. `fetch_netsuite_vendors()` — returns vendor name, default GL code, default cost center, typical amount range.
3. For each transaction, attempt to match:
   - Exact name match on vendor → propose that vendor's default GL and cost center.
   - Fuzzy match (>85% similarity, accounting for `*` and abbreviations Ramp inserts) → propose with a "fuzzy" confidence tag.
   - No match → flag as "unknown vendor".
4. For matched vendors, also check the amount. If the transaction amount is more than 3x the typical amount for that vendor, flag as "unusual amount".
5. For unknown vendors, search prior Ramp history — maybe this vendor has been matched before but isn't in NetSuite yet. If found, propose using the prior match.
6. Produce three stacks:
   - **Auto-approve** — high-confidence matches with normal amount. Default action: approve. Show the proposed GL + cost center.
   - **Needs your eye** — fuzzy matches, unusual amounts, or first-time vendors with a prior Ramp history hit. Default action: review.
   - **Unknown vendor** — no match anywhere. Show the merchant string, amount, cardholder, date. Default action: assign or file a vendor-add request.
7. Offer one-click bulk approve for the Auto-approve stack and a draft message to the cardholder for unknowns asking what the charge was for.
8. At the end, report totals — N transactions triaged, M auto-approved, K needing review, $X covered.

## Example

User: "triage Ramp"

REX: 47 unmatched transactions in the last 7 days, totaling $38k. Auto-approve: 31 ($22k) — matches to Slack, Figma, AWS, OpenAI, and others. Needs your eye: 11 ($12k) — one Figma charge 4x normal, two fuzzy Anthropic matches, one first-time Vercel charge with a prior hit. Unknown: 5 ($4k) — drafts messages to each cardholder.

## Output format
Three stacks (Auto-approve, Needs your eye, Unknown vendor) with per-stack bulk-action buttons and a summary total bar at the bottom.
