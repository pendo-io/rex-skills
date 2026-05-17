# Module Definition Interview Guide

Walk through each section one at a time. Acknowledge what the user says, synthesize and
confirm before moving on. Suggest defaults where the user is unsure. Mark TBD rather than
getting stuck. Complete all 9 sections for one module before starting the next.

---

## Section 1 — Job to Be Done (JTBD)

> "Who is the primary user of this module — a rep, a manager, a FLM, or someone else?"
> "What situation are they in when they need this?"
> "What do they want to do in that moment?"
> "What outcome are they trying to achieve?"

Synthesize into:
> *"When [situation], I want to [action], so I can [outcome]."*

Present the synthesized JTBD and confirm. This is the most important section — if it
can't be stated precisely, the experience isn't ready to build.

---

## Section 2 — Trigger

> "What event or condition causes this module to fire?"
> "Is it time-based (daily cron, weekly), event-based (field change, new record), or
> condition-based (threshold crossed)?"
> "What suppression filters apply — what should prevent it from firing even if the
> condition is met?"

Prompt for specifics: exact schedule, SOQL/BQ query shape, field conditions, pilot gates,
record caps. Triggers must be unambiguous and measurable — vague triggers produce vague outputs.

---

## Section 3 — Experience Type

Present the three options and ask which best fits:

| Type | How it works | Best for |
|---|---|---|
| **Action Driver** | Detects condition, executes workflows, notifies Pendozer for accept/reject/edit | Creating/updating data in systems of record, anything requiring human judgment |
| **Intelligence Provider** | Prompted by Pendozers; AI assists but does not act | Prep, research, planning, drafting, open-ended questions |
| **Autonomous** | Subset of Action Drivers; acts without human intervention | Low-risk, high-confidence, repetitive tasks |

Also determines Pendo tracking: Action Driver/Intelligence Provider → P4E;
pure AI agent with no UI surface → Agent Analytics.

---

## Section 4 — Data Sources Required

> "What systems does this agent need to read from?"
> (BigQuery, Salesforce, Slack, Gong, Outreach, etc.)
> "What specific tables, objects, or APIs?"
> "Does it write back anywhere? If so, what fields?"

Capture as a table: System | What | Access Pattern (read/write, batch/real-time).

---

## Section 5 — Output

> "What does the agent actually produce for the user?"
> "Is it a Slack message, a structured document, a ranked list, a score, a CRM entry?"
> "What specific fields/content does it show?"
> "What action buttons or CTAs are included?"

Output types: Slack alert with context, pre-drafted message, structured document, ranked
list, score/status, logged CRM entry. Output must be specific enough that the user knows
what to do immediately.

---

## Section 6 — Action(s)

> "After the agent sends its output, what is the single most important action the user takes?"
> "What does clicking each button actually do — what gets written where?"

Flag if the answer is too vague and push for specificity:
- Good: *"Rep clicks Approve → bot writes AI text to Next_Steps__c field in SFDC."*
- Too vague: *"Rep reviews the information and decides next steps."*

---

## Section 7 — Suppression & Override Logic

> "Are there active states on an account or opportunity that should block this?"
> (active escalation, executive negotiation, etc.)
> "Should there be a daily/weekly cap on how many records a user sees?"
> "Can a user snooze or dismiss? For how long?"
> "Should autonomous actions have a review window for high-stakes cases?"

**Common defaults to suggest:**
- 1 record per user per day cap
- Pilot gate (specific users/managers only)
- Type filters (exclude renewals, services, tier trips)
- Active owner check
- Snooze with reason fed back into future logic

---

## Section 8 — Success Metrics

> "What's the main business outcome we're trying to move? How would we measure it?"
> "What does 'this is working' look like in numbers after 30 days?"
> "What's the adoption signal — % of users completing the queue weekly?"

Push for **three metrics** per module:
- **Outcome metric** — the business result (e.g., "% of flagged opps progressed within 48h > 60%")
- **Adoption metric** — usage signal (e.g., "% of FLMs completing full daily queue > 70%")
- **Experience metric** — UX quality (e.g., "Avg time per record < 30 seconds")

---

## Section 9 — Build vs. Buy

> "Is there a purpose-built tool that already does this well?"
> "What's the closest off-the-shelf alternative, and why doesn't it fit?"

Known tools to reference: Gong (call scoring/summaries), Clari (pipeline forecasting),
Scratchpad (SFDC hygiene), Fin AI (knowledge bots), Outreach (sequencing).

Capture the build rationale in 2–3 sentences.

---

## Pre-Generation Confirmation

Before generating the .docx, show the user a full summary of all sections for all modules
and ask for explicit approval. They can correct anything before the doc is created.
