---
id: summarize-slack-thread
name: Summarize Slack Thread
description: Fetches a Slack thread from a link and returns a crisp summary with decisions and open questions.
kit: essentials
tools: [search_slack, fetch_slack_thread]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Summarize Slack Thread

## When to use
User pastes a Slack permalink, or says "summarize this thread", or right-clicks a Slack message and chooses "Send to REX". Great for catching up on a channel after PTO or after a long meeting.

## What it does
Pulls the full thread (parent + all replies), including reactions, attachments, and linked Jira/Notion pages. Returns a three-part summary: (1) what the thread is about, (2) decisions made, (3) open questions and owners.

## Inputs
- slack_url: canonical Slack permalink (pendo.slack.com/archives/...)
- depth (optional): "brief" (100 words), "standard" (250 words, default), or "detailed" (500 words with quotes).

## Instructions (given to the agent)

1. Parse the Slack URL to extract channel_id and thread_ts. If the URL is malformed, ask for a clean permalink.
2. Call `fetch_slack_thread(channel_id, thread_ts, include_reactions=true)` to get the full thread.
3. If the thread references a Jira ticket or Notion page, call the corresponding tool to fetch context. Do not summarize those attachments deeply — just note their relevance in one line.
4. Produce three sections:
   - **Topic** — one or two sentences on what this thread is about and who started it.
   - **Decisions** — bulleted list of decisions made. Include who decided. If no decisions, say "No decisions yet."
   - **Open questions** — bulleted list with the question and the person expected to answer. If the thread stalled, call that out.
5. End with a single-line "next action for you" based on whether the user is tagged, owns an open question, or is just observing.
6. Never quote more than two sentences verbatim — paraphrase instead. Exception: if a decision was made verbatim ("approved", "rejected"), quote it.
7. If the thread has more than 50 messages, offer to export a canvas with the full summary instead of inlining it.

## Example

User: "summarize https://pendo.slack.com/archives/C06XYZ/p1708123456789"

REX: Fetches the thread, finds it's about rolling back the IAF DevBot Cloud Run revision. Returns: Topic (rollback discussion started by Sean at 2pm), Decisions (rolled back to rev 00072, Lindy approved), Open questions (does rev 00075 need a new IAM bind? — owner: Lindy), Next action ("You're tagged on the IAM question — reply when ready").

## Output format
Markdown with three headed sections (Topic, Decisions, Open questions) plus a one-line "Next action for you" footer. Total length matches the requested depth.
