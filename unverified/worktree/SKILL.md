---
name: worktree
description: >
  Create git worktrees and branches from issue tracker tickets. Looks up the ticket,
  generates a branch name, creates worktree(s) under <repo>/.claude/worktrees/<branch>,
  and shifts the conversation to work there. Keeps the main branch clean while you work.
kit: swe
audience: [swe]
workflow: [git, branching]
tools: []
tags: [git, worktree, branching, workflow]
version: 1.0.0
---

# Worktree Setup Skill

Create git worktrees for one or more repos, branched off the latest default branch.

## Configuration

Before using, define your repos in this table:

| Repo | Path | Default Branch |
|------|------|---------------|
| `my-app` | `/path/to/my-app` | `main` |
| `my-lib` | `/path/to/my-lib` | `main` |

Update the repo names, paths, and default branches to match your project.

## Argument Parsing

Arguments: `[repos...] [ticket]`

- **Repos** (1+): one or more repo names from the table above
- **Ticket** (required): issue key from your tracker (e.g., `PROJ-42`, `#123`)

Examples:
- `/worktree my-app PROJ-42` — one worktree in my-app
- `/worktree my-lib PROJ-106` — one worktree in my-lib
- `/worktree my-app my-lib PROJ-106` — worktrees in both repos

## Steps

### 1. Look up the ticket

Fetch the ticket summary from your issue tracker (Jira, Linear, GitHub Issues — use whichever MCP or CLI tool is available). Use the summary to generate a descriptive branch suffix (lowercase, hyphenated, 3-5 words max).

**Branch name format:** `<ticket-lowercase>-<descriptive-suffix>`
Example: `proj-42-add-user-export`, `proj-106-fix-auth-redirect`

### 2. For each repo requested

```bash
cd <repo-path>
git checkout <default-branch>
git pull origin <default-branch>
mkdir -p .claude/worktrees
git worktree add .claude/worktrees/<branch-name> -b <branch-name> origin/<default-branch>
```

### 3. Confirm

Tell the user:
- Which worktree(s) were created and where
- The branch name
- That the conversation is now working in the worktree(s)

From this point forward, all file operations for the affected repo(s) should use the worktree path(s):
- `<repo>/.claude/worktrees/<branch-name>/` instead of `<repo>/`

## Error Handling

- If the branch already exists, ask the user if they want to check it out instead of creating a new one.
- If the worktree directory already exists, inform the user and ask how to proceed.
- If ticket lookup fails, ask the user for a short branch description instead.
