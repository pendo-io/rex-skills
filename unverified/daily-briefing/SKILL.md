---
name: daily-briefing
description: >
  Run a structured morning briefing that covers Slack activity, Google Calendar, open
  action items from recent meeting notes (Granola/Notion), and a curated reading list
  tailored to data analytics and SaaS. Use when the user says "start my day",
  "morning briefing", "catch me up", "what's on my plate today", "daily standup",
  or anything that sounds like they want a morning overview of their day.
kit: essentials
audience: [ae, csm, flm, swe, finance, ops, exec]
workflow: [prep, monitoring]
tools: [slack_search_public_and_private, list_gcal_events, notion-search, web_search]
tags: [productivity, morning, briefing, standup, calendar, slack]
source_kind: pendo-curated
author: jonathan.rice
version: 1.0.0
created: 2026-05-13
---

# Daily Briefing

Run a structured morning briefing that covers Slack activity, today's calendar, open
action items from recent Granola meeting notes, a curated reading list, and a
consolidated action items list. Deliver it as a clean, scannable summary — not a wall
of text.

---

## When to Run

Trigger this skill when the user says things like:
- "Start my day"
- "Morning briefing" / "daily briefing"
- "Catch me up"
- "What's on my plate today?"
- "What do I have going on today?"

---

## Execution Order

Run Sections 1–5 in parallel where possible, then compile into a single briefing.
Section 6 (Action Items) consolidates output from all prior sections.

### Section 1 — Slack Catch-Up

Search Slack for recent unread or active activity the user should know about.
Focus on signal, not noise.

**Priority channels to always check** (search each for messages from the last 18 hours):
- The user's primary team channel
- Any DMs where the user was mentioned or received a message
- Any threads the user is part of that have new replies

**How to search:**
- Use `slack_search_public_and_private` with `after` set to yesterday
- Filter for DMs and mentions directed at the user
- Look for messages that require a response or contain a question directed at the user

**What to surface:**
- Direct messages the user hasn't replied to
- Mentions of the user they may not have seen
- Key updates or decisions made in their primary channels
- Anything that looks like it needs their input or action

**What to skip:**
- Purely informational updates with no action needed
- Channels they're in but not active in
- Bot messages and automated notifications

---

### Section 2 — Calendar Prep

Pull today's calendar events and prep the user for each meaningful meeting.

**Steps:**
1. Use `list_gcal_events` with `time_min` set to start of today and `time_max` set to end of today
2. Filter out declined events, all-day blocks, and routine recurring 1:1s unless something notable is happening
3. For each real meeting, note: time, key attendees (if relevant), and a one-line prep note if context warrants it

**Keep it tight** — one line per meeting is usually enough.

---

### Section 3 — Unscheduled Meetings Detection

While reviewing Slack and Gmail, watch for discussions about meetings that haven't
been scheduled yet. Look for signals like:
- "Let's find time to chat about..."
- "Can we set up a call on..."
- "I'll grab some time with you"
- Someone proposing a specific date/time that isn't on the calendar yet

**If you find an unscheduled meeting:**
1. Check the calendar to confirm it isn't already scheduled
2. Surface it in the briefing under a **📆 Unscheduled Meetings** section
3. Ask the user for confirmation before creating the event — show proposed title, attendees, suggested time, and duration
4. Once confirmed, create the calendar event using the Google Calendar tool

**Never create a calendar event without explicit confirmation.**

---

### Section 4 — Granola Meeting Notes Review

Check recent Granola notes in Notion for outstanding follow-ups, next steps, and to-dos.

**Steps:**
1. Search Notion for recent Granola notes using `notion-search` with query "Granola meeting notes"
2. Fetch the 3–5 most recent notes (sorted by timestamp)
3. For each note, scan for:
   - Explicit to-dos or action items (look for phrases like "follow up", "send", "review",
     "schedule", "action item", "next steps", "I'll", "[name] will")
   - Open questions that weren't resolved in the meeting
   - Commitments the user made to others
   - Things others committed to the user (so they can follow up if needed)

**What to surface:**
- Only items that are still likely unresolved
- Group by meeting; flag the meeting name and date alongside each item

**What to skip:**
- Notes from more than 5 days ago unless they contain something explicitly unresolved
- Purely administrative standups with no open items

---

### Section 5 — Reading List

Search the web for 3–5 pieces of content worth the user's time. Content should be
practical, current, and high signal — tailored to data analytics, AI, and SaaS.

**How to search:**
- Run 2–3 targeted web searches across different topic areas
- Prioritize content published in the last 7 days; allow up to 30 days for evergreen pieces

**Topic areas to rotate through (pick 2–3 per day):**
- How data teams can use AI to drive business value
- AI-powered analytics: LLMs and agents for insights, automated reporting, decision support
- SaaS metrics and product analytics: NRR, churn modeling, customer health scoring, PLG
- AI for customer success: churn prediction, health scoring, automated playbooks
- Practical AI use cases for analysts: AI-assisted SQL, Python AI workflows, prompt engineering
- Revenue operations and go-to-market analytics
- AI product launches or research relevant to SaaS data teams
- Emerging ideas for data teams: new techniques, early-stage tools, forward-looking thinking

**What to avoid:**
- Pure data engineering / pipeline / infrastructure content (dbt internals, Spark, Airflow)
- Vendor marketing disguised as editorial
- Paywalled content with no preview
- Content older than 30 days unless it's a landmark piece

**What to include per item:**
- Title and source
- One-sentence summary of what it covers and why it's relevant
- Link

---

### Section 6 — Action Items & Follow-Ups

Surface things the user needs to act on today. Pull from multiple sources:

**From Slack:**
- Unanswered questions directed at the user
- Things they said they'd follow up on ("I'll look into that", "let me check")
- Requests they made that haven't been acknowledged yet

**From Gmail:**
- Unread emails or emails needing a reply from the last 3 days
- Flag anything with a deadline or time-sensitive ask
- Skip newsletters, automated notifications, and anything clearly not requiring action

**From Calendar:**
- Any prep work implied by today's meetings

**From Granola Notes (Section 4):**
- Roll up any unresolved action items into this consolidated list

---

## Output Format

Deliver the briefing as a clean, conversational summary using this structure:

---

**Good morning. Here's your day.**

**📬 Slack**
[2–5 bullets of the most important Slack activity. Lead with anything needing a response.]

**📅 Calendar**
[List today's meetings in time order. One line each.]

**📆 Unscheduled Meetings** *(only include if any found)*
[List any meetings discussed but not yet on the calendar. Ask for confirmation before creating.]

**📝 From Your Notes** *(only include if unresolved items found)*
[Bullet list of open action items from recent Granola meeting notes. Include meeting name in parentheses.]

**📖 Worth Reading**
[3–5 curated links. Title, source, one-sentence description of why it's relevant. Keep it tight.]

**✅ Action Items**
[Numbered list of things to do today, in priority order. Include items from Granola notes alongside Slack and email items.]

---

## Tone & Style

- Conversational but efficient — like a sharp EA giving a morning rundown
- Don't over-explain. If something is obvious, skip the context.
- Flag anything urgent with a brief note on why
- If a section is empty (e.g., no meetings today), say so in one line and move on
- Never say "I found X messages" — just tell the user what matters

---

## Notes

- If it's Monday, also check for anything unresolved from Friday
- If the user asks for more detail on any item, drill in using the appropriate tool
- After delivering the briefing, ask if they want to act on anything (draft a reply, prep for a meeting, etc.)
