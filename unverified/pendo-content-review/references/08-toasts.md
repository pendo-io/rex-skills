---
title: Toasts
last_updated: 2026-03-15
tags: [ux-writing, toasts, notifications, components]
summary: Guidelines for writing toast messages that confirm actions, provide next steps, or reinforce task context.
---

## Overview

Toasts are brief, non-blocking messages that confirm actions or notify users of changes. They appear temporarily in the top-right corner of the UI and disappear automatically.

Use them to:
- Confirm completed actions (e.g., saving, deleting, creating)
- Let users know what happens next
- Offer optional next steps (e.g., "View now")

## Anatomy

Toasts should always include both a header and a supporting message:

- **Header** (required) — A short, bold summary of the action. No punctuation.
- **Message** (required) — A full sentence providing context, follow-up info, or next steps.
- **CTA link** (optional) — Only if there's a meaningful immediate action.
- **Icon** (optional) — Only if baked into the component design.

Don't publish a toast with a header only. The message provides the context that makes the header useful.

## Header guidelines

- Use sentence case (no punctuation)
- Keep it short — ideally 3–5 words summarizing the action
- Don't include item names or overly detailed info
- Use plural for bulk actions (e.g. "3 guides deleted")

✅ Good:
> Segment saved
> 3 guides deleted
> Settings updated

❌ Avoid:
> Saved successfully!
> Woohoo, it worked!
> The system has completed your action
> Feedback response for Q2 survey saved
> Segment "New Visitors" created successfully!

## Message guidelines

- Provide context or follow-up info if helpful (e.g. where something appears, what changed)
- Use full sentences with punctuation
- Include a CTA link only if it's a meaningful follow-up
- Don't restate the header unless it's necessary for clarity

✅ Good:
> You can now view this segment in your analytics reports.
> This widget appears on the **Product Trends** dashboard.

❌ Avoid:
> The segment has been saved successfully.

## CTA links

Use links like "View now" only if:
- The user can immediately benefit from going there
- The link is clear and direct (never "Click here")
- It appears at the end of the message and is easy to ignore

✅
> View now ›

## Style summary

| Element | Guidance |
|---|---|
| Header | Short, bold, sentence case, no period |
| Message | Full sentence(s), context if helpful |
| Link | Optional, only if actionable |
| Icon | Only if baked into the design component |

## Placement

Toasts always appear in the top-right corner of the UI. They should not block interactions or require dismissal.

## Tone

- Be clear and calm — never celebratory or emotional
- Avoid filler like "successfully," "just," or "easily"
- Prioritize what's useful to the user now

## When not to use toasts

- For blocking errors (use alerts or inline error text)
- For confirmations that need choices (use modals)
- For attention-grabbing warnings (use alerts)
