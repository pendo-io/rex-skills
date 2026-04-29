---
id: welcome-tour
name: Welcome Tour
description: Introduces REX to a new Pendo employee and suggests 3 starter skills based on role.
kit: essentials
tools: [query_bigquery, fetch_iaf_metrics]
author: pendo-iaf
version: 1.0.0
created: 2026-04-17
---

# Welcome Tour

## When to use
First time a Pendo employee opens REX, or when they explicitly ask "what can you do" or "how do I get started". Also triggered by the onboarding automation on day one.

## What it does
Greets the user by name, explains what REX is in under 60 words, then inspects their Okta role (or asks) and recommends exactly three skills from the Dojo that match their function. Ends by installing those three skills with a single confirm-button.

## Inputs
- user_email: Pendo email, used to look up role in the employee directory.
- role_override (optional): if the caller already knows the role, skip the lookup.

## Instructions (given to the agent)

1. Call `fetch_iaf_metrics(kind="employee_profile", email=user_email)` to get the user's role, team, and manager. If that fails, ask the user "what team are you on?" and offer: Sales, CSM, Engineering, FLM, Finance, Other.
2. Greet the user by first name. Use one sentence. No exclamation points.
3. Explain REX in one paragraph: "REX is Pendo's AI coworker. It connects to SFDC, Gong, Pendo, BigQuery, and Slack, and runs skills from the Dojo on your behalf. Skills are installable playbooks — think of them like Chrome extensions."
4. Pick three starter skills based on role:
   - Sales / AE → `account-signal-investigation`, `draft-follow-up-email`, `pipeline-pulse`
   - CSM → `churn-risk-investigation`, `qbr-prep`, `champion-tracking`
   - Engineering / IAF → `saver-validation-check`, `bq-query-governed`, `cloud-run-deploy-check`
   - FLM → `flm-coaching-prep`, `team-pipeline-rollup`, `pipeline-pulse`
   - Finance → `ramp-expense-triage`, `forecast-variance-explanation`, `weekly-digest`
   - Other / unknown → `summarize-slack-thread`, `jira-ticket-triage`, `weekly-digest`
5. Offer to install all three with one click. Do not install silently.
6. Tell the user they can find more skills in the Dojo sidebar and that every skill can be disabled anytime.

## Example

User: "what is this"

REX: Greets the user, explains REX in a paragraph, detects from the employee directory that they are an AE on Emma Chen's team, recommends `account-signal-investigation`, `draft-follow-up-email`, `pipeline-pulse`, and shows an Install button.

## Output format
A short markdown response: greeting, one-paragraph explainer, a bulleted list of three recommended skills (each with a one-line description), and a single "Install all three" action button.
