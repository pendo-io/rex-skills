---
name: pr
description: >
  Commit, push, and create a pull request with an automated technical review comment.
  Extracts issue key from branch name for PR title. Adds a senior-engineer-style
  review as the first comment.
kit: swe
audience: [swe]
workflow: [git, review, pr]
tools: []
tags: [git, pull-request, review, workflow]
version: 1.0.0
disable-model-invocation: true
---

# /pr — Create Pull Request with Review

Create a pull request for the current branch with a technical review comment.

## Steps

### 1. Check current state

- Run `git status` to see uncommitted changes
- Run `git log origin/<default-branch>..HEAD --oneline` to see unpushed commits
- Identify the primary directory/module being changed
- **Extract issue key from branch name**: Check if the branch name contains an issue key pattern (e.g., `proj-50-large-timeseries-queries` → `PROJ-50`). Use regex to match `[a-zA-Z]+-[0-9]+` at the start of the branch name. Store the uppercase key for use in the PR title.

### 2. Commit any uncommitted changes (if any exist)

- Stage relevant files with `git add`
- Create a commit with appropriate message following the pattern:
  - `feat(scope):` for new features
  - `fix(scope):` for bug fixes
  - `docs(scope):` for documentation
  - `refactor(scope):` for refactoring
- Scope should be the primary directory or module (e.g., `auth`, `api`, `frontend`)

### 3. Push to remote

- Push with `git push origin <branch>`
- If that fails, check your remote config and retry with the correct URL

### 4. Create the PR

- Analyze ALL commits on this branch (not just the latest) using `git log origin/<default-branch>..HEAD`
- Review the full diff with `git diff origin/<default-branch>..HEAD`
- Determine if this is a `feat()` or `fix()` based on the nature of changes
- **PR title format**: If an issue key was extracted from the branch name, prefix the title with it:
  - With key: `PROJ-50 feat(api): add rate limiting middleware`
  - Without key: `feat(api): add rate limiting middleware`
- Create PR with `gh pr create`:
  ```
  gh pr create --title "[KEY] <type>(scope): <description>" --body "$(cat <<'EOF'
  ## Summary
  <2-4 bullet points describing what changed and why>

  ## Changes
  <list of specific changes made>

  ## Test plan
  <how to verify the changes work>

  🤖 Generated with [Claude Code](https://claude.com/claude-code)
  EOF
  )"
  ```

### 5. Add technical review comment

- Get the PR number from the create output
- Add a detailed review comment using `gh pr comment <number> --body "..."`
- The review should be written as a senior engineer on the team who:
  - Understands the codebase context
  - Provides constructive, specific feedback
  - Notes what's done well
  - Identifies potential issues or edge cases
  - Suggests improvements (if any)
  - Asks clarifying questions where appropriate
- Keep the tone collaborative, not condescending
- Focus on technical substance, not style nitpicks

### 6. Return the PR URL so the user can view it

## Notes

- If there are no changes to commit or push, inform the user
- If a PR already exists for this branch, inform the user and provide the URL
- Always use HEREDOC for multi-line content to preserve formatting
