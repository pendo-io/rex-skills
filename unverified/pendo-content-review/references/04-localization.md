---
title: Localization
last_updated: 2026-03-15
tags: [ux-writing, localization, internationalization, i18n]
summary: Guidelines to ensure UX copy is clear, literal, and easily translatable for a global audience.
---

## General guidance

- Write in plain, literal English that translates cleanly.
- Avoid idioms, slang, metaphors, jokes, or cultural references.
- Use inclusive, respectful language that works across regions.
- Avoid gendered language and idiomatic turns of phrase unless required for clarity.

## Terminology

- Prefer common, unambiguous terms:
  - ✅ "Select" instead of "pick"
  - ✅ "Enter" instead of "type in"
  - ✅ "Visit" instead of "drop by"
- Avoid U.S.-centric phrases or seasonal references (e.g. use "Q4" or "end of year" instead of "fall").
- Avoid references to holidays, sports, or pop culture.
- Avoid Latin abbreviations and phrases. Write them out in plain English instead.
  - ❌ "e.g." → ✅ "for example" or "Example:" (when used as a label)
  - ❌ "i.e." → ✅ "that is" or rewrite the sentence for clarity
  - ❌ "via" → ✅ "through," "using," or "with"
  - ❌ "per" → ✅ "for each," "a," or rewrite (e.g. "per user" → "for each user")

## Formatting and structure

- Use full sentences; avoid sentence fragments across separate strings.
- Avoid string concatenation — sentences must be translatable as a whole.
  - ❌ `"You have " + [count] + " items"`
  - ✅ `"You have [count] items"`
- Don't hardcode dates, times, currencies, or symbols.
- Anticipate longer translations — avoid designs that rely on short fixed-length copy.
- Avoid formatting that may be ambiguous (e.g. slashes, ampersands, em dashes).
- Use descriptive link text ("View metadata rules") instead of "click here."

## Numbers and units

- Follow [Punctuation and Numbers](05-punctuation-and-numbers.md) guidance.
- Use numerals for values ≥ 10 and spell out zero through nine.
- Use standard, localizable units (min, px, %, MB).
- Avoid non-standard abbreviations (e.g. "w/" for "with").

## Error and friction messages

- Avoid blame, sarcasm, or casual tone.
- Focus on clarity, calmness, and next steps.
- Structure messages with:
  - A plain description of the issue
  - A helpful suggestion or resolution
  - Optional CTA (e.g. "Learn more," "Try again")

## Examples (don't → do)

| Don't | Do |
|---|---|
| Feeling stuck? Let's fix that! | Need help? Here's what to try. |
| Pick your favorite feature | Select a feature |
| Hit the ground running | Get started quickly |
| Looks like something went haywire | Something went wrong |
| You've got 5 messages! | You have 5 messages |
