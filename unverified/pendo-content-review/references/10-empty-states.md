---
title: Empty States
last_updated: 2026-03-15
tags: [ux-writing, empty-states, components, guidance]
summary: Guidance for writing helpful, purposeful empty states that orient users and encourage next steps.
---

## Overview

Empty states appear when no content is available for a screen, panel, table, or card. They help orient the user, clarify why the space is empty, and suggest what to do next. Empty states are common during onboarding, filtering, or first-time use.

## When to use

Use an empty state when:
- The user hasn't created or configured anything yet (e.g. no guides, no segments)
- A filter, segment, or selection yields no results
- Data is loading, missing, or unavailable due to permissions
- A user action clears out previously visible content

## Structure guidelines

### Headline

- Use sentence case with no ending punctuation
- Summarize the state in 3–6 words
- Start with "No," "Start by," or a task-based verb
- Be specific when helpful: "No Features found," not "Nothing to see here"

### Body copy

- Keep it short — 1–2 sentences max
- Explain why the space is empty (if known)
- Suggest what will appear here, or what the user can do to populate it
- Avoid repeating the headline
- Offer calm reassurance if applicable (e.g. first-time visit)

### CTA (optional)

- Use a single clear CTA when action is possible
- Prefer buttons over links for task-based actions
- Avoid multiple CTAs unless there's a strong reason

## Writing tips

- Tailor the message to the user's context and role
- Avoid overly casual language or humor
- Don't imply the user did something wrong
- Use full sentences with punctuation in the body, unless brevity helps clarity
- Add a little brand personality or encouragement when appropriate
- Favor soft, helpful language like "Try adjusting..." rather than direct commands
- Avoid empty state copy that adds no value (e.g. "Nothing to see here.")
- Make sure the headline, body, and CTA work together as a purposeful message
- Frame the message around **user benefit** — what they can achieve next.
- Use the empty state to **demonstrate value** or encourage progress.
- Keep the image and message aligned — don't add illustrations that confuse or distract.

## Tone and brand examples

Empty states are a good opportunity to reflect Pendo's brand — productive, supportive, and clear — without sacrificing clarity or localization readiness.

| Scenario | Bad (avoid) | Clear & functional | Brand-aligned (Pendo) |
|---|---|---|---|
| First-time setup | Get started already! Seriously, do something. | **Start by tagging Features** / Tag elements in your app. | **Start by tagging Features** / Tag elements in your app to begin collecting insights. |
| No saved content | Yikes, you haven't saved anything yet! | **No saved views** / Save filters to reuse them later. | **No saved views** / Try saving a view to make these filters easy to revisit. |
| No search results | No luck! Maybe start over? | **No matching results** / Adjust your filters and try again. | **No matching results** / Try adjusting your filters to uncover more data. |
| Permissions | Access denied! You're not allowed here. | **You don't have access** / Contact your admin for help. | **You don't have permission to view this** / Reach out to your admin to request access. |
| Missing data | Nothing here. Weird. | **No data yet** / This space will update when data becomes available. | **No data yet** / This area will fill with data as soon as activity is recorded. |

## Visual and layout notes

- Empty states typically appear in cards, tables, charts, or page panels
- Use Pendo-approved illustrations for full-page empty states (when applicable)
- Avoid stacking empty states with other visual messages (e.g. alerts)
- Keep the layout lightweight and distraction-free

## Avoid

- "Oops," "Whoops," or any language that implies error or blame
- Placeholders like "Lorem ipsum" or icon-only content with no label
- CTAs that don't relate directly to the empty state context
- Passive-aggressive or sarcastic phrasing
- Idiomatic phrases like "check back later" or "stay tuned" — these don't translate and add no value
  - ✅ "This area will update when data becomes available."
