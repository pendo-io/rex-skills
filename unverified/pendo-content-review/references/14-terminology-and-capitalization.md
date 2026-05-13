---
title: Terminology and Capitalization
last_updated: 2026-03-26
tags: [ux-writing, terminology, capitalization]
summary: Rules for capitalization and term usage across UI copy to support clarity, consistency, and localization.
---

## Overview

Use this guide to apply consistent capitalization and terminology in Pendo's UI content. It supports localization, improves scannability, and ensures proper noun usage aligns with product structure and branding.

Pendo uses sentence case as the standard, meaning we only capitalize the beginning of a sentence or headline and proper nouns. This rule applies to all labels, except for global navigation within the UI.

## Quick reference: making capitalization decisions

**1. Are you writing general content about a feature or capability?**
→ Use lowercase (sentence case rules apply)
- "Create a guide to onboard users"
- "Use app discovery to find unmanaged apps"
- "Tag features to track engagement"

**2. Are you directing users to click or navigate to a specific UI element?**
→ Bold it and match the exact UI casing
- "Navigate to the **App discovery** page"
- "Go to the **Guides** page"
- "Select **Agent mode**"

**3. Are you writing a button or CTA?**
→ Preferred: avoid using the feature name. Use generic action verbs and add feature context in surrounding copy.
- Instead of "Try Agent mode" → "Get started" or "Try it now"
- Instead of "Open Data Explorer" → "Explore data" (with "Data Explorer" in the heading or description)

If you must reference the feature in the button, match the exact UI label casing. Document exceptions for rare, high-visibility entry points.

**4. Are you writing a header or page title?**
→ Use sentence case (capitalize first word + proper nouns only)
- "Tag maintenance" not "Tag Maintenance"
- "Configure agent mode settings" not "Configure Agent Mode Settings"

## Common mistakes

| Incorrect | Correct |
|---|---|
| "Create a Guide" | "Create a guide" |
| "Use Segments" | "Use segments" |
| "The App Discovery feature" | "The app discovery feature" |
| "Tag Maintenance" (as a header) | "Tag maintenance" |

## Capitalization guidelines

### Sentence case = default

Use sentence case for: buttons and labels, form fields and placeholders, tooltips and hover text, empty states and alerts, toasts and dialogs, headers and page titles.

Use title case only for: global navigation titles, product names or named experiences (e.g., Visual Design Studio).

### Proper noun criteria

Capitalize a term only if it meets one of the following:
- It refers to a **product, app, tool, or named experience** (e.g., Data Explorer, Resource Center, Pendo Support Bot)
- It is **sold separately, trademarked, or marketed as a distinct capability**
- It would be **confusing if lowercase**, especially when used alongside a generic version (e.g., "features" vs. "Features" in Pendo)

Also consider capitalizing if the functionality is equivalent to existing proper nouns (e.g., "Track Events" aligns with "Pages" and "Features").

**Note:** "Tool" or "named experience" refers to something that functions as a standalone capability — not every labeled feature or UI element.

### Avoid overcapitalization

- Don't capitalize generic elements like "settings," "visitor," "account," or "dashboard."
- Never capitalize words for emphasis or "specialness."
- Don't use all caps except for acronyms or UI constraints.

## Special terms

| Term | Capitalize? | Notes |
|---|---|---|
| Feature | Yes | Tagged UI element in Pendo |
| Page | Yes | Tagged group of URLs in Pendo |
| Track Event | Yes | System-defined event type in Pendo |
| guide | No | Lowercase unless referencing the full product label |
| user | No | Use "you" in UI or "visitor" for end users; reserve "user" for internal roles |
| onboarding | No | Unless in a title or product name |
| app | No | Short for application |
| segment | No | Lowercase in all contexts |
| dashboard | No | Lowercase in all contexts |
| app discovery | No | Lowercase unless referencing the specific UI label |

For all other terms, check the Product terminology glossary.

## Capitalization in different contexts

### General running copy

In running copy (paragraphs, descriptions, tooltips), only capitalize the first word of a sentence, proper nouns (per criteria above), and people/places/official product names.

Distinguish between proper nouns and common nouns:
- "Tag **Features** in your app to track which features your users engage with" (proper noun "Features" = the Pendo tracking object; common noun "features" = general capabilities)
- "Create a guide to onboard your users" (not "Create a Guide" — "guide" is not a proper noun)

### Referencing UI elements

When directing users to a specific UI element in documentation or instructional copy, bold the label and match the exact UI casing:
- "Go to the **Guides** page to create a guide"
- "Select **Agent mode** to enable the feature"

When discussing the feature generally (not directing to a UI location), use sentence case without bold:
- "With app discovery, you can identify unmanaged apps"

### Gray areas and exceptions

Some situations require judgment calls. When encountering edge cases:
- Try to reword to avoid the awkward capitalization pattern
- Handle case-by-case rather than creating complex rules
- Document your decision and reasoning for future reference
- Stay aligned with your team on these decisions

Example: "Configure Onboarding questions" → better as "Configure questions" if "Onboarding" is already clear from context.

## Why sentence case matters

- **Readability:** Too many capitalized words reduce scannability and make UI feel cluttered.
- **Consistency:** Unnecessary caps create inconsistency, especially when the term isn't widely recognized.
- **Accessibility:** Sentence case is easier to parse for screen readers, people with dyslexia (overcapitalization disrupts word shape recognition), and non-native English speakers.
- **User trust:** Capitalizing generic terms can imply undue importance or confuse meaning. When everything looks important, nothing is.

Only capitalize what earns it.
