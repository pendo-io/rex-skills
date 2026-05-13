---
title: Confirmations
last_updated: 2025-06-26
tags: [ux-writing, confirmations, dialogs, components]
summary: Guidelines for writing confirmation messages that clearly describe the action and its consequences.
---

## Overview

Confirmation dialogs ask users to verify an action before continuing. These messages are usually used for irreversible, destructive, or high-impact tasks. They give users a final chance to cancel or proceed with clarity.

## When to use

Use a confirmation dialog when the action:
- Permanently deletes or removes something
- Affects other users or live environments
- Alters critical settings, permissions, or access
- Can't be undone or has no rollback option

## Content guidelines

### Structure

A good confirmation includes:
- A **clear, specific prompt** — often phrased as a question (but avoid vague ones like "Are you sure?")
- An optional **consequence statement** if the action is destructive or irreversible
- A pair of **buttons**: one for canceling, one for confirming

### Messaging style

- Use **sentence case** with punctuation
- Don't start the message by repeating the modal heading
- State the action or consequence clearly, in 1–2 short sentences
- Include names, numbers, or context when helpful
- Avoid confirmshaming or vague phrasing (e.g. don't use "Are you sure?")
- If the action can be undone, say how or where
- Use line breaks between the primary prompt and supporting sentence if space allows

### Tone

- Calm, confident, and factual
- Respectful and user-centered — never pushy or alarming
- Avoid filler, hype, or casual jokes

## CTA guidelines

- Use **imperative verbs** that restate the action
  - ✅ Delete guide
  - ✅ Remove user
  - ✅ Leave team
- Don't use vague labels like "Yes" or "OK"
- Use "Cancel" or "Go back" as the secondary CTA
- Visually separate destructive actions from neutral ones
- For permanent actions, consider explicit labels like "Yes, delete permanently" (but only when needed)

## Example patterns

### Destructive — single item

**Heading:** Delete segment?
**Message:** This segment will be permanently removed and can't be recovered.
**Buttons:**
- Cancel
- Delete

### Destructive — multiple items

**Heading:** Delete dashboards?
**Message:** You're about to delete these # dashboards. They'll be removed for anyone with access and can't be recovered.
**Buttons:**
- Cancel
- Delete

**Heading:** Delete guides?
**Message:** You're about to delete these # guides. They'll be removed from analytics and visitor view and can't be recovered.
**Buttons:**
- Cancel
- Delete

### Reversible action

**Heading:** Change access?
**Message:** This update will change access levels for multiple apps across your subscription.
**Buttons:**
- Cancel
- Remove

### Risky update

**Heading:** Save changes?
**Message:** This update will affect other team members immediately.
**Buttons:**
- Cancel
- Save changes

## Writing tips

- Include relevant details (names, counts, outcomes)
- Never use exclamation points
- Don't rely on "Are you sure?" — be specific and helpful instead
- Use sentence case consistently, even in button labels
- Keep it short but complete — avoid partial sentences or vague terms
- Default focus should typically go to **Cancel** for safety and accessibility
