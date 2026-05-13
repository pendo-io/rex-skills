---
title: Content Types and Structure
last_updated: 2026-02-25
tags: [ux-writing, content-types, structure, layout]
summary: Defines types of in-product UX content and how to structure them for clarity, consistency, and reduced cognitive load.
---

## Content types

| Type | Purpose | Examples |
|---|---|---|
| Labels | Identify fields, toggles, or objects | "Visitor type," "Tag Feature" |
| Tooltips | Provide optional, clarifying info triggered by hover or click | "Auto-save is on." |
| Toast messages | Confirm actions or show status briefly | "Settings saved." |
| Success feedback | Confirm task completion or progress inline | "Metadata mapping saved." |
| Empty states | Guide users when no data is present | "No Features found." |
| Errors | Explain issues and next steps | "This field is required." |
| Warnings | Alert users to risks or system conditions that need attention | "This change applies to all users." |
| Confirmations | Confirm user decisions or risks | "Delete this item?" |
| Callouts / Alerts | Offer optional tips, background info, or system-level notifications | "Note: This setting doesn't apply to mobile." |
| Instructional text | Provide inline guidance for completing tasks | "Select a Feature to continue." |
| Walkthroughs | Guide users step-by-step using in-app flows | Pendo Guides onboarding sequence |
| Page-level help | Link to detailed docs or conceptual overviews | "Learn how metadata mapping works." |

---

## How to choose the right type

Each content type supports a different level of complexity and user need. Choose based on:

- Whether the content is essential, optional, or instructional
- How persistent the content should be (always visible vs. dismissible)
- What task or decision the user is trying to complete
- The level of explanation needed (short label vs. deeper context)
- Where the user is in the flow (entry point vs. advanced task)

---

## Structural guidance

- Keep content short and scan-friendly.
- Use sentence case for all in-product content.
- Capitalize only proper nouns, branded terms, and tagged entities (Pages, Features, Track Events).
- Place key info first — especially in titles, buttons, and CTAs.
- Use consistent phrasing patterns across similar elements.
- Don't overload components with nested logic or multiple ideas.
- Use progressive disclosure to reveal only what's needed.
- Link out only when essential for context or completion.
- Reuse existing terms from the product glossary where applicable.
- Avoid repeating the same copy across types (e.g. don't duplicate tooltips and labels).
- Always include visible labels when using icons — never rely on visual cues alone.
- Don't rely on color or icon-only messaging without supporting text for accessibility.

---

## Form fields: labels, helper text, and placeholders

Forms are a structured content type. Each field should follow a clear hierarchy to reduce cognitive load and improve accessibility.

### Field structure model

| Element | Purpose |
|---|---|
| **Label** | What this field is |
| **Helper text (optional)** | Why it matters or what to include |
| **Placeholder (optional)** | Example of expected content or format |

Use only what's necessary. Not every field needs helper text or a placeholder.

### When to use helper text

Use persistent helper text when:
- The label alone doesn't clarify expectations.
- The field affects system behavior or analytics in a non-obvious way.
- Higher-quality input improves outcomes.
- The field's purpose could be misinterpreted (e.g., internal vs. AI-facing usage).

Avoid helper text when:
- The label is self-explanatory.
- The field is operational (e.g., dropdown selection).
- The placeholder example provides sufficient clarity.
- Adding helper text would create unnecessary visual density.

Prefer one strong section-level introduction over repeating similar helper text under every field.

### Placeholder guidelines

Placeholders are primarily for examples — not instructions.

**Do:**
- Provide short, concrete examples.
- Demonstrate expected tone or format.
- Use "Example:" instead of "e.g." (avoid Latin abbreviations).
- Keep examples globally neutral when possible.

**Don't:**
- Hide critical workflow instructions in placeholders.
- Use placeholders as the only source of essential guidance.
- Write multi-sentence explanations.
- Use metaphors or internal jargon.

| Weak | Strong |
|---|---|
| Enter a user prompt | How do I update my billing information? |
| Describe the use case | Example: Questions about invoices or refunds |
| e.g. Sales assistant | Example: Sales proposal assistant |

Concrete examples improve input quality and reduce ambiguity.

### When brief clarifying context in placeholders is acceptable

A short clarifying sentence may appear in a placeholder **when**:
- The label could be misinterpreted.
- The clarification does not affect system behavior.
- The information does not need to persist after input.
- The sentence is brief and followed by an example.

Do not include critical behavioral logic or multi-step instructions in placeholders.

If guidance must remain visible after typing, use helper text instead.

### Section-level guidance

When multiple related fields require shared context:
- Add a short section introduction.
- Avoid repeating the same explanation under each field.
- Keep per-field guidance literal and concise.

Use progressive disclosure intentionally. Forms should feel focused — not instructional-heavy.

### Tooltips in forms

Tooltips should reinforce value, not provide required instructions.
- Keep them short.
- Avoid implementation details (e.g., "model," "LLM").
- Use them when guidance is optional or conditional.

Do not rely on tooltips for essential workflow information.

---

## Additional notes

- **Instructional text** is best for task-specific help that fits within the UI. Avoid cluttering screens with nonessential explanation.
- **Tooltips** should clarify logic or purpose — not repeat UI text. Use when the info is optional, contextual, or too conditional to show inline.
- **Toast messages** are best for short confirmations or status updates — not for warnings or errors.
- **Success feedback** may appear inline, in modals, or near the impacted element. Use short, direct language that confirms completion.
- **Warnings** call attention to potential risks or high-impact changes. Place them close to the relevant UI context and explain consequences clearly.
- **Callouts / Alerts** include inline banners or contextual notices that inform or orient without breaking task flow. Use sparingly to avoid noise.
- **Confirmations** should use clear, direct headers — never "Are you sure?" Instead, pose the specific action as a yes/no question. Follow with a brief consequence statement if applicable.
- **Errors** should appear near the problem, state what went wrong, and help the user recover.
- **Walkthroughs** are ideal for onboarding and multi-step flows. Use intentionally to support user success — not as a catch-all help solution.
- **Page-level help** should only be used when users need broader conceptual or configuration guidance. Prefer inline help first.
