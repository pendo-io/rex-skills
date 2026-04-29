---
id: analyze-gong-call
name: Analyze Gong Call
description: Pulls a Gong call transcript and extracts competitor mentions, objections, and next steps.
kit: sales
tools: [fetch_gong_call, list_gong_calls, query_salesforce]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Analyze Gong Call

## When to use
After an important discovery, demo, or negotiation call. Also used in deal-review prep when an FLM wants to understand where a deal stands. On-demand via "analyze my last Gong call" or from a Gong URL.

## What it does
Fetches the full transcript, speakers, and timestamps. Extracts competitor mentions (with context), objections raised, commitments made by both sides, and explicit next steps. Cross-references the SFDC opportunity to flag gaps between what was said and what's in the system.

## Inputs
- gong_url_or_id: Gong call URL or Gong call id.
- opportunity_id (optional): SFDC Opp to cross-reference.

## Instructions (given to the agent)

1. Resolve the call. If the user said "my last call", use `list_gong_calls(user=current, limit=1)`.
2. Call `fetch_gong_call(id, include_transcript=true, include_trackers=true)`.
3. Extract in this order:
   - **Competitors mentioned** — search the transcript for Pendo's tracked competitors (WalkMe, Whatfix, Amplitude, Heap, Mixpanel, Fullstory, Gainsight PX, Userpilot, Chameleon, Appcues). Quote the sentence of context, speaker name, and timestamp.
   - **Objections** — anything the prospect pushed back on. Paraphrase, don't quote more than 15 words. Note if it was addressed on the call.
   - **Commitments** — what did Pendo commit to? What did the prospect commit to? Include dates when specified.
   - **Next steps** — the explicit agreed next step, owner, and date.
4. If `opportunity_id` is provided, fetch the Opp via `query_salesforce("SELECT Id, Name, StageName, CloseDate, Next_Steps__c, Primary_Competitor__c FROM Opportunity WHERE Id = ...")`. Flag mismatches:
   - Competitor on the call doesn't match `Primary_Competitor__c`? Flag.
   - Next step on the call doesn't match `Next_Steps__c`? Flag.
   - Close date discussed doesn't match `CloseDate`? Flag.
5. Output. Prioritize signal over volume — a 60-min call should still produce under 300 words of analysis.

## Example

User: "analyze https://us-12345.app.gong.io/call?id=7890"

REX: Fetches the call. Finds WalkMe mentioned twice by the prospect ("we also looked at WalkMe but their pricing was crazy"). Two objections (security review will take 6 weeks; budget not approved until Q2). Commits: Pendo to send SOC2 by Friday; prospect to loop in CISO. Next step: follow-up call May 3. SFDC gap: `Next_Steps__c` still says "send pricing" — recommend updating.

## Output format
Four sections (Competitors, Objections, Commitments, Next steps) plus an optional SFDC gap-check section if opp_id was provided. Total under 300 words.
