---
id: draft-follow-up-email
name: Draft Follow-up Email
description: After a call or meeting, drafts a personalized follow-up using Gong and SFDC context.
kit: sales
tools: [fetch_gong_call, query_salesforce, fetch_calendar_events, draft_email]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Draft Follow-up Email
 
## When to use
Right after a call ends. REX's ambient mode can trigger this automatically when a Gong call finishes processing (hooked via the `post-gong-call` automation). Also on-demand from a Gong URL or a calendar event link.

## What it does
Reads the Gong transcript (or meeting notes if Gong didn't record), pulls the SFDC opportunity, and drafts a 120-to-180-word follow-up that restates decisions, lists next steps with owners and dates, and closes with a clear ask.

## Inputs
- source: Gong call id, calendar event id, or "last meeting".
- tone (optional): "executive" (short, direct), "warm" (default), or "technical".
- include_attachments (optional): list of file paths (e.g. pricing deck, SOC2) to flag for attachment.

## Instructions (given to the agent)

1. Resolve the source. If Gong, fetch the transcript. If Calendar, fetch attendees and the event description.
2. Extract from the source (or from `analyze-gong-call` if it already ran on this call):
   - Decisions made on the call.
   - Explicit next steps with owners and dates.
   - Any promised collateral (deck, case study, security doc).
   - The prospect's top remaining concern.
3. Fetch the opportunity via `query_salesforce("SELECT Id, Name, StageName, Amount, CloseDate, Account.Name FROM Opportunity WHERE Id = ...")` to ground the email in deal context.
4. Draft the email. Structure:
   - **Subject** — specific, not generic. "Next steps on Adobe security review" not "Thanks for the call".
   - **Opener** — one sentence acknowledging the call. Never "Great to chat!" — reference something specific.
   - **Decisions** — 1-2 sentences recapping what was decided.
   - **Next steps** — bulleted list with owner and date. Maximum 4 bullets.
   - **Ask** — one clear question or request. Never more than one.
   - **Close** — one sentence, warm but brief.
5. Keep the total body between 120 and 180 words. Shorter is almost always better.
6. Flag promised attachments in a separate "remember to attach" note above the draft.
7. Surface a Send-via-Outreach button and an Edit-in-Gmail button. Do not send without confirmation.

## Example

User: "draft follow-up for my last Adobe call"

REX: Fetches the call. Drafts a subject line "Next steps on Adobe security review", opens by referencing Sarah's question on session replay, recaps that Pendo will send SOC2 by Friday and Sarah will loop in the CISO, asks for a 30-minute CISO call on May 3. 142 words. Flags "Attach: SOC2 Type II PDF".

## Output format
A draft email with subject and body, plus a "remember to attach" note, plus Send and Edit action buttons.
