---
title: Punctuation and Numbers
last_updated: 2026-03-01
tags: [ux-writing, numbers, punctuation, style]
summary: Covers punctuation, number formatting, units, and symbol usage for localization-friendly UX copy.
---

## General rules

- Use **periods** for full sentences.
- Don't use ending punctuation on:
  - Button labels
  - Headings
  - List items (unless full sentences)
- Avoid em dashes and slashes. Use "to" or commas instead.
- Use contractions where appropriate (e.g. "you'll," "we're").
- Avoid starting full sentences with numerals in tooltips or instructional text. In short UI messages (like toasts or status labels), starting with a number is OK.
  - ✅ 3 items deleted (toast)
  - ✅ 12 apps failed to sync (status message)
  - ❌ 25 users completed the task.
  - ✅ A total of 25 users completed the task.
- Use "after" instead of "once" when describing a sequence of events. Reserve "once" for contexts where it means a single occurrence or could be read as counting.
  - ✅ "After you save, the changes will apply."
  - ❌ "Once you save, the changes will apply."
- Write out "average" in full. Don't use "avg."
  - ✅ "Shows the average time on page"
  - ❌ "Shows avg time on page"

## Numbers

- Use **numerals** for all numbers in product UI (tooltips, labels, modals, alerts, toasts, and metrics).
- Use **numerals** for steps, inputs, counts, and measurements.
  - ✅ You've connected 3 apps
  - ✅ Step 2 of 4
- In longer instructional text:
  - Spell out numbers **zero through nine** for general references
  - Use numerals for **10 and above**, or when referring to precise values, data, or user-entered inputs
- Use commas in numbers 1,000 and up (e.g. 12,000 visitors).
- When abbreviating, use lowercase "k" for thousands, uppercase "M" for millions, and uppercase "B" for billions. Try providing exact number when possible, especially upon hover.

## Units and symbols

- Use abbreviations for units: **5 min**, **16 px**, **3 MB**
- Abbreviated units are always singular (e.g. 5 min — not 5 mins)
- Insert a space between the number and unit (except for symbols like %)
- Don't add spaces before symbols like %, °, or °C
- Use **%** symbol in UI, dashboards, and charts
- Use **percent** in body copy or when writing full sentences
- Use **percentage** when referring to field definitions, settings, or calculated values
  - ✅ A high percentage of visitors dropped off.
  - ✅ Set a percentage threshold for adoption.
  - ✅ The UI displays this value as a percent.
- Use **+** and **−** for signed values with no space before the digit (e.g. −3)
- Use "to" instead of hyphens or slashes for numeric ranges (e.g. 5 to 10 minutes)

## Dates and times

- Use full month names: **July 2025**, not **7/25**
- Avoid ordinal numbers (e.g. use **June 1**, not **June 1st**)
- Use **AM/PM** format with a space (e.g. **9:30 AM**)
- Use "noon" or "midnight" instead of "12:00"
- Use colons for time (e.g. 9:30 AM), not periods (e.g. 9.30 AM)
- For time zones, use: **Eastern Time (UTC−5)** or simplified **ET** if space is limited
