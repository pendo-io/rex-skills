---
title: Accessibility in UX Writing
last_updated: 2025-03-15
tags: [ux-writing, accessibility, inclusivity, a11y]
summary: Best practices for writing accessible, inclusive UX content that supports all users and meets WCAG standards.
---

## Overview

UX writing plays a critical role in accessibility. Clear, consistent, and inclusive language helps users with a wide range of abilities — including people using screen readers, non-native speakers, and those with cognitive or motor challenges.

This file outlines how to write content that is **perceivable, operable, understandable, and robust**, following accessibility best practices and WCAG principles.

## Why it matters

Accessible content ensures:
- Everyone can complete tasks, regardless of ability
- Assistive technologies can interpret content correctly
- UI patterns work for a global audience, including those unfamiliar with idioms or implied meaning
- We avoid excluding users through unclear, inconsistent, or biased language

## Key writing principles

### ✅ Be clear
- Use literal language. Avoid idioms, slang, or figurative phrasing.
  - ✅ "You can edit this report later."
  - ❌ "You're all set to dive in!"
- Choose simple words and short sentences.
- Avoid jargon unless it's necessary — and define it if used.
- Don't use em dashes. They're hard to read aloud and difficult to localize. Use commas or parentheses instead.

### ✅ Be concise
- Limit each component to **one idea at a time**.
- Don't overload screens or tooltips with unnecessary detail.
- Use progressive disclosure when depth is needed.

### ✅ Be inclusive
- Use person-first, respectful language.
  - ✅ "Visitor who hasn't logged in" instead of "anonymous visitor"
- Avoid terms with ableist roots (e.g. "disable," "crazy," "blind to...").
- Use second person ("you") instead of abstract references like "users."

### ✅ Be consistent
- Use terminology and patterns from the product glossary.
- Maintain sentence case and avoid overcapitalization.
- Ensure the same concept is described the same way across screens and surfaces.

---

## Component-specific guidelines

### Labels and buttons
- Use clear, specific actions: "Save changes," not "OK."
- Avoid vague one-word buttons unless the action is obvious from context.
- Keep button text short — ideally **1–3 words**.

### Tooltips
- Avoid duplicating existing labels — add value.
- Ensure tooltip content makes sense if read out of context by screen readers.
- Don't rely on hover alone — ensure tap support on mobile (popup tips).

### Icons and imagery
- Never rely on color or icon alone to convey meaning.
  - ✅ Add a label or tooltip to icons.
- Don't use emojis in UI copy or tooltips.
- Use alt text when icons convey information (not needed for decorative icons).

### Alerts and banners
- Ensure the alert has a clear purpose and message.
- Include both a visual cue (e.g. color or icon) **and** readable text.
- State the outcome or required action directly.

### Error messages
- Be specific about what went wrong and how to fix it.
- Don't blame the user. Use calm, factual language.
- Place errors close to the field or component causing the issue.
  - ✅ "This field is required."
  - ✅ "Use at least 8 characters."

### Empty states
- Offer context and encourage action.
- Keep the tone friendly, but not jokey or abstract.
- Avoid cultural references or metaphors.
  - ✅ "No Features found. Try tagging an element to start collecting data."

---

## Screen reader considerations

### How screen readers announce content

Screen readers read content linearly, announcing in this sequence:
1. **Element type** — button, link, heading, form field
2. **Label or text content** — the visible or programmatic label
3. **State** — expanded, collapsed, selected, required, checked

This means the label alone carries most of the meaning. Write labels that make sense when heard in isolation, without surrounding visual context.

**Practical implications:**
- "Download" becomes "Download" with no context. "Download report" tells the user exactly what they'll get.
- Error messages are announced as "[field label] + [error text]" — so "Email" + "Invalid" reads as "Email, invalid." Write errors that complete that sentence: "Email, must include @" or "Email address, enter a valid email."
- Button states matter: "Notifications" with state "collapsed" is interpretable. An icon-only button with no label isn't.

### Writing for screen readers
- Write UI text so it makes sense **when read aloud or out of context**.
  - ✅ "Download report" instead of "Download"
- Always include visible labels — not just placeholders.
- For confirmation dialogs or modals, make sure the heading and message aren't redundant.
- Don't hide critical information inside tooltips or icons only.

---

## Cognitive load and reading level

- Aim for plain language, ideally 6th–8th grade reading level.
- Use bullet points, numbered steps, and clear headings to improve scannability.
- Avoid overloading a single screen or paragraph with too many choices or concepts.

---

## Inclusive language examples

| Instead of… | Use… |
|---|---|
| "Disabled setting" | "This setting is off" or "Not available" |
| "Crazy high" | "Very high" or "Unusually high" |
| "Click here" | "Select **Download** to save your report" |
| "Guys" (e.g. "Hey guys") | "Everyone" or omit entirely |
| "Simple" or "Easy" | Focus on steps: "Takes just two steps" |
| "Targeting" (e.g. "targeting users") | "Sending to," "showing to," or "for" |
| "Triggered" (e.g. "triggered by") | "Shown after," "appears when," or "starts when" |

---

## ARIA and assistive technologies

While UX writers at Pendo typically don't implement ARIA attributes directly, we contribute the language that appears in ARIA-supported roles and patterns.

### ARIA matters because:
- It helps screen readers describe what each part of the UI does.
- It adds meaning when visible cues (like color or icon-only buttons) aren't enough.
- It ensures complex components — like modals, tab sets, or live regions — are understandable and navigable for assistive tech users.

### Writing tips for ARIA content:
- Be specific and literal: Avoid vague labels like "info" or "button"
- Match visible UI text where possible
- Keep labels short but descriptive
- Don't duplicate or overload with redundant info
- Clarify purpose: Use `aria-describedby` to explain how or why to use a control if needed

If a component lacks sufficient context for a screen reader, flag it for design or engineering follow-up. We're responsible for helping ensure all users can understand and interact with the interface — visibly or audibly.

For more guidance, see [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/) and [WebAIM's ARIA landmarks guide](https://webaim.org/techniques/aria/).

---

## Localization and text expansion

When writing for a global product, plan for text expansion in translation. English is typically the shortest form of any given string.

**Approximate expansion by language:**

| Language | Expansion |
|---|---|
| German | +30–40% |
| French | +15–20% |
| Spanish | +15–20% |
| Italian / Portuguese | +20–25% |

**Design implications:**
- **Buttons:** Allow for 150–200% of English text length when designing component widths.
- **Titles and headings:** Plan for 130–150% expansion.
- **Avoid hardcoded character limits** in UI strings unless the field itself has a functional constraint (e.g., SMS, usernames).

**Writing tips for localization:**
- Keep sentences short — shorter English strings produce more accurate, less ambiguous translations.
- Avoid idioms, metaphors, and culturally specific references (they don't translate).
- Use gender-neutral constructions where possible to reduce translator overhead.
- Don't embed variables inside sentences where word order varies across languages (e.g., "You have [X] days left" may need restructuring in some languages).

---

## Pre-publish checklist

Before shipping any UX text, verify:
- [ ] All interactive elements have clear, descriptive labels
- [ ] Links describe destination ("View pricing," not "Click here")
- [ ] Error messages are specific and actionable
- [ ] Color is not the only indicator of meaning
- [ ] Text meets contrast requirements (4.5:1 for body text; 3:1 for large text and UI elements)
- [ ] Sentences average 15–20 words or fewer
- [ ] Reading level is appropriate for audience (6th–8th grade for general; 9th–10th for professional tools)
- [ ] No idioms, metaphors, or cultural references
- [ ] Required fields are marked with more than just color
- [ ] Form instructions appear before input fields
- [ ] Success and error states include text, not just icons
