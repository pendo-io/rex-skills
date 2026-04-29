---
id: saver-validation-check
name: SAVER Validation Check
description: Hits the SAVER API and explains which SFDC validation rules would block a proposed write.
kit: engineering
tools: [fetch_saver_rules, query_salesforce]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# SAVER Validation Check

## When to use
Before writing to an SFDC record from an IAF service — SHB, RevOS, RetOs, or any new bot. Also useful when a user reports "why did my update fail in SFDC" and you need to reproduce the rule that blocked it.

## What it does
Hits the SAVER API (`saver-api` on Cloud Run, pendo-reporting, us-east1) with a proposed record diff and returns the list of validation rules that would fire, the fields that need to be set, and the evaluation trace for each rule. Useful during development and as a pre-flight check in production writes.

## Inputs
- sobject: SFDC object API name (e.g. Opportunity, Account, Contact).
- record_id (optional): if updating an existing record, the 18-char Id. Omit for insert simulation.
- proposed: JSON dict of fields and values being set.
- mode (optional): "shadow" (default, just report), "parallel" (report and compare to SFDC response), "primary" (block the write if SAVER says it'll fail).

## Instructions (given to the agent)

1. If `record_id` is provided, fetch the current record state via `query_salesforce` so SAVER has a baseline.
2. Call the SAVER API: POST to `$SAVER_BASE_URL/api/v1/validate` with headers `X-API-Key: $SAVER_API_KEY` and JSON body `{sobject, record_id, proposed, mode}`.
3. Parse the response. SAVER returns:
   - `rules_evaluated` — count of active rules considered for this sobject.
   - `rules_that_would_fire` — list of rules, each with `rule_name`, `error_message`, `trace` (the formula evaluation steps), and `missing_fields` (fields the caller would need to set to pass).
   - `verdict` — "pass" or "fail".
4. If verdict is "fail", produce:
   - A one-line "this write would be blocked by N rule(s)".
   - A table of rules with rule name, error message, and the simplest fix (which fields to set).
   - The minimal diff that would make the write pass, rendered as a JSON patch.
5. If verdict is "pass", just confirm and note which rules were evaluated. Reassurance has value.
6. Remind the user that SAVER's Next_Steps__c field handling is known-good (not CRE — this is a common gotcha). `Total_of_Competitors__c` is a rollup and cannot be set directly.
7. Never attempt the write yourself. SAVER is a read-only advisor.

## Example

User: "check if I can set StageName = 'Closed Won' on 006XYZ"

REX: Fetches the opp's current state. Calls SAVER. Response: verdict=fail, 2 rules fire — "Close Won requires Primary_Competitor__c" and "Close Won requires Actual_Use_Case__c". Returns a patch: `{"StageName": "Closed Won", "Primary_Competitor__c": "<required>", "Actual_Use_Case__c": "<required>"}`.

## Output format
Verdict (pass/fail), rule breakdown table if fail, minimal fix patch if fail, and a pointer to the SAVER explorer UI for deeper inspection.
