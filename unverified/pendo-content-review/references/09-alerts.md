---
title: Alerts
last_updated: 2025-06-19
tags: [ux-writing, alerts, components, warnings]
summary: Guidance for writing in-app alerts, including when to use info vs warning styles, tone guidance, and layout patterns.
---

## Overview

Use alerts to call attention to important information in the product. These messages appear as inline banners, often placed at the top of a page or inside a modal near the relevant UI element. Most alerts are single-line messages without a headline.

Alerts follow two primary styles:
- **Info (blue)** — for tips, explanations, or neutral guidance
- **Warning (yellow)** — for cautionary messages or edge-case risks

## When to use each type

| Alert type | Use when... |
|---|---|
| **Info** (blue) | Sharing neutral, helpful context (e.g. metadata tips, reminders, account notices) |
| **Warning** (yellow) | Alerting users to risk, limited data, or potential downstream impact |

Avoid using alerts for:
- Confirmations (use toast messages instead)
- Errors or form validation (use inline messages)

## Message style

- Sentence case with punctuation
- One or two short, direct sentences
- Don't restate visible labels or UI
- Use plain, literal phrasing that translates easily

## Headlines

Use a short heading to summarize the alert's purpose when it appears on a page. This can be a standalone bolded headline or a bold run-in phrase, depending on layout.

- Use sentence case with no punctuation
- Summarize the core issue or context (don't repeat the body)
- Omit the heading in modals unless it adds critical clarity

**Page-level examples:**

**Standalone headline style:**
> **Event B has no data**
> For more reliable results, consider adjusting the date range or selecting a different success event.

**Run-in heading style:**
> **20 apps are missing permissions.** These apps will default to Data User access only.

**Modal example (no heading):**
> Changes you make here will overwrite the users' existing permissions.

## Icons and links

- Icons may be included **only** if the component is designed to show one. Don't add emojis or custom symbols in text.
- If linking to more help (e.g. KB articles), use a plain CTA at the end (e.g. "Learn more").
- Keep links contextual — not standalone.

✅ Example:
> Your access is limited based on your plan. Learn more ›

❌ Don't:
> Your access is limited. 🚨 Click here for help!

## Placement

- Alerts typically appear at the top of the page or modal
- If the alert relates to a specific element (e.g. roles, apps, permissions), place it near that element

## Examples

**Info (blue)**
- 20 apps aren't included in a permission set. These apps will default to Data User access only.
- Give meaning to your data with metadata mappings. Map metadata fields to standard attributes to ensure consistency across Pendo.

**Warning (yellow)**
- Event B has no data from the past 180 days. For more reliable results, consider adjusting the date range or selecting a different success event.
- Multiple applications are selected with different assigned roles.
