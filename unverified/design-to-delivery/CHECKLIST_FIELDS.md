# Scoping Checklist Field Reference

Template doc: `1N2XsrW6EifvxBs_bDVoGCiVZR_9jDrrctzhSdijZxDw`

---

## Pre-Launch: AI Experience Development Checklist

Use the gathered context to fill each field. Where context doesn't answer a field,
ask the user directly. Every field must be filled before Phase 3 begins.

### 1. Job to Be Done (JTBD)
Format: *"When [situation], I want to [action], so I can [outcome]."*
This is the most important field. If it can't be stated precisely, the experience
isn't ready to build.

### 2. Trigger
The specific event or condition that causes this experience to fire. Must be
unambiguous and measurable — vague triggers produce vague outputs.

### 3. Experience Type
| Type | How it works | Best for |
|---|---|---|
| **Action Driver** | Detects condition, executes workflows, notifies Pendozer for accept/reject/edit | Creating/updating data in systems of record, anything requiring human judgment |
| **Intelligence Provider** | Prompted by Pendozers; AI assists but does not act | Prep, research, planning, drafting, open-ended questions |
| **Autonomous** | Subset of Action Drivers; acts without human intervention | Low-risk, high-confidence, repetitive tasks |

Also determines Pendo tracking: Action Driver/Intelligence Provider → P4E;
pure AI agent with no UI surface → Agent Analytics.

### 4. Data Sources Required
All systems the experience reads from. Map data availability before committing to build.

### 5. Output
What the experience produces. Must be specific enough that the Pendozer knows what
to do immediately. Reference the output type table in the checklist doc.

### 6. Action(s)
The single thing the Pendozer does after receiving the output. If the answer is
"it depends," the output isn't specific enough yet.

### 7. Suppression & Override Logic
When should the experience *not* fire? Include snooze logic, escalation exclusions,
and human review windows for autonomous actions.

### 8. Success Metric
One measurable outcome metric + one adoption metric. Both required.

### 9. Build vs. Buy
Is there a purpose-built tool that already does this? Evaluate before building.

### 10. Experience Evaluation (pre-launch, with Platform team)
Track during build to validate quality:
- **Tool Calls:** model selects correct tools with >90% accuracy
- **Reasoning Steps:** chain-of-thought is logically consistent
- **Retrieval Quality:** RAG context precision and recall
- **Output Quality:** brand voice, accuracy, safety guardrails

### 11. Ongoing Monitoring (set up at launch, with Platform team)
| Category | Requirement |
|---|---|
| Data & Tracking | BQ event table live; Pendo tracking (P4E or Agent Analytics) added |
| Dashboard | IAF Dashboard reading from BQ event table, live at launch |
| Governance | Completed checklist doc; calendar invites sent for recurring check-ins |

---

## Post-Launch: AI Experience Evaluation Checklist (CC/CD Framework)

Fill this section during Phase 2 as a plan; execute it post-launch.

### 1. Calibration Loop Plan
Define the Surface → Engage → Resolve metrics:
| Metric | Definition | Data Points |
|---|---|---|
| Surface | AI suggestions made visible | Volume of suggestions, baseline |
| Engage | Humans interact with suggestion | Click-through rate, time spent |
| Resolve | Desired action completed | Accept/Edit/Reject ratios, time to resolve |

Define agency progression plan:
- Version 1: High control, low agency
- Version 2: Medium control, medium agency
- Version 3: Low control, high agency

### 2. Feedback & Evaluation
- Thumbs up/down + free-form textbox in the UI
- For Action Drivers: measure acceptance rate, compare suggestion to system-of-record outcome
- Identify 2 user champions who can explain the experience and its value

### 3. Business KPI + UX KPI
Must define both. Cannot launch without measurable KPIs for both dimensions.

### 4. Collaboration
Identify at least one other team member who can manage the experience. No single
points of failure.
