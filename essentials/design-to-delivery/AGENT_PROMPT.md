# Coding Agent Briefing Template

Use this template when spawning a coding agent for a ticket in Step 6.
Fill in all sections — the more context the agent has, the better the output.

---

## Template

```
You are implementing a Glass/Rex feature for Pendo's internal AI platform.

## Your ticket
**Title:** {ticket_title}
**Jira:** {jira_url}

**What to build:**
{ticket_description}

**Acceptance criteria:**
{acceptance_criteria}

## Why this exists (from the design call)
{transcript_excerpt}

## Repo
{repo_name} — {repo_url}
Branch off: main
Target PR: main (open as draft)

## Relevant existing code
{pointers_to_relevant_files_or_patterns}
Search the repo before writing anything new — prefer extending existing patterns
over introducing new ones.

## Dependencies
{dependency_context}
(If none: "This ticket has no dependencies.")

## Quality requirements
Before opening the PR:
1. Run all available checks: tests, lint, build
   (check package.json / Makefile / README for the right commands)
2. Fix any failures — do not open a PR with failing checks
3. If you cannot fix a failure after reasonable attempts, stop and report
   the blocker clearly instead of opening a broken PR

## PR format
- Title: {ticket_title}
- Body: what was built, how to test it, link to Jira ticket
- Open as draft
- Do not merge
```

---

## Tips for filling the template

**Relevant existing code**: before spawning, grep the repo for the skill name, feature
area, or key terms from the ticket. Include 2–4 file paths and a one-line note on why
each is relevant.

**Dependency context**: if this ticket depends on others, include what those tickets
produced — file paths, exported names, interfaces. The agent needs this to integrate
correctly without re-reading the whole repo.

**Transcript excerpt**: keep it to 2–4 lines — just enough to convey the intent and
constraints from the design discussion. Longer excerpts dilute signal.
