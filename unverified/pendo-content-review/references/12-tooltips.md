---
title: Tooltips
last_updated: 2025-06-26
tags: [ux-writing, tooltips, components, inline-help]
summary: Guidelines for writing clear, contextual tooltips and popup tips that supplement the UI without distraction or redundancy.
---

## Overview

Tooltips are contextual, user-triggered messages that appear on hover, focus, or tap. They provide **optional guidance** — never required info — and support decision-making without cluttering the UI.

Types of tooltip triggers:
- **Hover-based** (web): Show when a user hovers or focuses
- **Popup tips** (touch): Require tap to trigger and manual dismissal

Use tooltips when:
- You need to clarify logic, terminology, or impact
- A control's purpose or behavior isn't obvious
- You're offering optional reassurance or context near a decision point

**Don't use tooltips** when:
- All users need the information to complete a task (use inline text instead)
- The message duplicates existing content
- A clearer label or layout could solve the confusion

Tooltips are helpful for:
- Icon-only controls or toggle behaviors
- Metadata or dashboard logic
- Advanced filtering or reporting settings

## Tooltip triggers and icons

| Trigger type | Icon | Use when... |
|---|---|---|
| Info icon | ℹ️ | Explaining **what** something is (e.g. logic, settings) |
| Help icon | ❓ | Explaining **why** it matters (e.g. recommendations) |

Icon choice is visual, but the content pattern should align with the trigger's purpose.

## Length and content guidelines

| Tooltip type | Limit | Style |
|---|---|---|
| Hover or inline | ≤ 15 words | Sentence fragments allowed; no period needed unless full sentence |
| Popup / tap-triggered | ≤ 40 words | Use full sentences with punctuation |

If more than 40 words are needed:
- Break the message into multiple tooltips
- Or add a link: "Learn more ›" (link to Help Center article), but make sure the hover state allows for the user to select the link

### Formatting

- Use sentence case
- No emojis or decorative icons in text
- Use bullets only for true lists
- Avoid repeating the UI label or control
- Add CTA links only if highly relevant and task-based

## Writing tips

- Focus on what the user needs to know — not how the feature works
- Think of tooltips as optional guidance, not required steps
- Use progressive disclosure — tooltips are a second layer of info
- Start with a verb if prompting an action
- Use plain, literal phrasing that's globally clear
- Avoid filler like "This is a tooltip..." or "You can hover to see more"
- Use a calm, helpful tone — never alarmist or jokey
- Don't use tooltips to solve poor UI labeling
- Prioritize clarity over technical precision unless accuracy is critical
- Use tooltips to support progressive disclosure: Add a second layer of help without overwhelming the UI.
- Don't add tooltips just to restate what's already visible — make them worth the click or hover.
- Tooltips are best when they clarify "what" or "why," not "how." If process help is needed, link out.

## Examples

✅ Good:
> Account-related widgets are only useful if your Pendo setup uses Account IDs. Learn more ›

✅ Good:
> Choose whether to measure by individual groups or grouped accounts. This impacts how onboarding success is calculated and reported.

✅ Good:
> We'll add this name throughout the dashboard so that you can easily reference and understand the context of each widget.

✅ Good:
> The selected date range compares data to the previous period of the same length. Last year includes data from two years ago, and Last six months includes data from 12 months ago.

✅ Good:
> Mark segments as verified to indicate that they're trusted and reliable. Use verification as part of your governance practices by reviewing and validating segment criteria regularly. Learn more ›

❌ Bad:
> This is a tooltip with some text.

❌ Bad:
> You can hover to get more info.

❌ Bad:
> Enter something in this field.

## Implementation notes

- Use **hard-coded tooltips** for long-term guidance
- Use **Pendo guides** for temporary or release-tied messaging
- Always check tooltip content with technical writers before finalizing
