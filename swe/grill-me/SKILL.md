---
name: grill-me
description: >
  Relentless design interview before implementation. Gathers context from your
  issue tracker and codebase, then walks the design tree asking clarifying questions
  until shared understanding is reached. Use before writing code.
kit: swe
audience: [swe]
workflow: [planning, design]
tools: []
tags: [design, interview, planning, spec]
version: 1.0.0
---

# /grill-me — Design Interview

Interview the user relentlessly about every aspect of their plan until you reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one by one. If a question can be answered by exploring the codebase, explore the codebase instead of asking.

## Arguments

`/grill-me [topic or description]` — optional free-text describing what you're about to build. If omitted, infer from the current branch/ticket context.

## Step 1: Gather Context (silent — do this before asking anything)

Build situational awareness so your questions are informed, not generic.

1. **Branch/ticket context:** Run `git branch --show-current`. If the branch contains an issue key (e.g., `proj-42-...`), fetch the ticket summary and description from your issue tracker (Jira, Linear, GitHub Issues — use whichever MCP or CLI tool is available). Check for linked tickets or a parent epic.
2. **Codebase scan:** Based on the topic (from args, ticket, or branch name), explore the most relevant files:
   - `find` and `grep` for related code — whatever the work touches (modules, models, tools, prompts, configs, tests, deploy scripts)
   - Read key files to understand the current state of the area being changed
   - Note existing patterns, conventions, and dependencies
3. **Prior art:** Check for related docs, decision records, or design notes in the repo that give context on past choices.

Do NOT dump what you found. Internalize it. Your questions should *demonstrate* that you've read the code — reference specific files, existing patterns, and real constraints.

## Step 2: Open the Interview

Start with a 2-3 sentence summary of what you understand so far (from the ticket, branch, and user's description), then state what's still unclear.

Ask your first 2-3 questions. These should be the highest-leverage questions — the ones whose answers change everything downstream.

## Step 3: Walk the Design Tree

After each answer, do the following:

1. **Acknowledge** what was decided (one sentence).
2. **Explore the codebase** if the answer raises questions you can resolve yourself (e.g., "Does X already exist?" → go look). Report what you found briefly.
3. **Branch deeper** — ask the next 1-3 questions that follow from what was just decided. Prioritize:
   - Questions where the answer constrains other decisions (resolve dependencies first)
   - Questions where the user might not have considered the tradeoff yet
   - Questions specific to *this* codebase, not generic software design

### What to probe

These are branches of the design tree — not a checklist. Explore whichever branches are relevant to the work:

- **Scope & boundaries:** What's in v1 vs. later? What explicitly won't change?
- **Inputs & outputs:** What comes in, what goes out, what happens in between? (Data, API calls, user messages, LLM responses — whatever the medium is.)
- **Dependencies:** What does this depend on? What depends on this? What breaks if this changes?
- **Existing patterns:** Does the codebase already solve a similar problem? Should this follow that pattern or diverge?
- **Failure & edge cases:** What happens when things are missing, late, malformed, wrong, or at unexpected scale? How does the system behave when an external service is down or slow?
- **User/consumer experience:** Who interacts with this and how? What do they see when it works? When it doesn't?
- **Migration & rollout:** Does this replace something? What's the transition plan? Who needs to know?
- **Testing & confidence:** How will you know this works? What does "wrong" look like? What's the eval strategy?
- **Naming & interfaces:** What are the contracts — APIs, tool schemas, prompts, config shapes, table schemas? Which names will be load-bearing?

### Interview style

- Be direct. Don't soften questions with preamble.
- Ask ONE branching question at a time when the answer matters for what you ask next. Batch 2-3 only when they're independent.
- When the user's answer is vague, push back: "That could mean X or Y — which one?"
- When the user says "I don't know yet," that's fine — note it as an open question and move on. Circle back later if a subsequent answer resolves it.
- Don't ask questions the codebase already answers. Go read the code.

## Step 4: Wrap Up

When you sense convergence (the user's answers are becoming "yes, exactly" or "that's what I said"), stop and deliver:

1. **Summary of decisions** — a compact numbered list of what was decided, using concrete names (files, models, fields, endpoints — not abstractions).
2. **Open questions** — anything that was deferred or still unresolved.
3. **Suggested next step** — usually "ready to plan" or "need to spike X first" or "should resolve open question #N before proceeding."

Do NOT generate a plan, spec, or implementation. The output of /grill-me is shared understanding, not a document.
