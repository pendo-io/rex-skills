---
title: Error Messages
last_updated: 2025-06-26
tags: [ux-writing, errors, messages, recovery]
summary: Guidance for writing clear, helpful error messages that explain what went wrong and what to do next.
---

## Overview

Brief, human, and helpful copy for when something goes wrong. Error messages should reduce frustration and help users recover quickly. Error messages don't need to be overly empathetic — just clear, helpful, and reassuring when appropriate.

## When to use

- User-entered data is invalid or incomplete
- Something fails to load or process
- Permissions or access issues
- System or network problems

## Style guidelines

- Say what happened, in plain language
- Avoid blame; say "We couldn't" or "There was an issue," not "You didn't"
- Offer a solution or next step where possible
- Use sentence case and punctuation
- Don't use slang or jokes
- Reassure the user when appropriate: highlight what succeeded, even if something failed

## Structure

1. **Problem summary** — what happened
2. **Cause or condition** (if helpful and known)
3. **What to do next** — a suggestion, link to support or docs, or CTA

## Writing tips

- Focus on recovery — help users get back on task
- Place messages close to the field or action that failed
- Be specific, but brief: name the issue and suggest a fix
- Avoid "you" phrasing if it risks sounding accusatory
- When useful, include links to solutions or related help
- Don't start with "You..." — it places blame. Instead, use neutral phrasing like "We couldn't..." or "Something went wrong."
- If helpful, add a link to a solution or relevant help article.
- Match tone to severity: minor errors don't need excessive empathy.

## Examples

**Form field**
> This field is required.

**Upload failed**
> We couldn't upload your file. Try again or choose a different format.

**Access denied**
> You don't have permission to view this page. Contact your admin for access.

**Timeout or technical error**
> Something went wrong. Refresh the page or try again later.

## Avoid

- ❌ "Oops!" or "Uh-oh!"
- ❌ Technical error codes (unless relevant)
- ❌ Blaming language: "You entered it wrong"
- ❌ Dead ends without guidance
