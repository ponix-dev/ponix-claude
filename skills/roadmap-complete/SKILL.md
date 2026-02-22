---
description: Use when the user has finished implementing a roadmap task and wants to wrap it up — create a PR, update the roadmap, and record the session in handoff.md. Trigger phrases include "I'm done", "ready to submit", "ship it", "roadmap complete", "send it for review", "make a PR", or "open a pull request". Runs pre-flight checks (fmt, clippy, tests), marks completed roadmap items, creates the PR, and updates the handoff context automatically.
user-invocable: true
---

# Roadmap Complete

Complete a roadmap task: run pre-flight quality checks, update the roadmap, create a pull request, and record the session in the handoff. Fix any check failures before proceeding.

## Steps

### 1. Pre-flight Checks

Run `/preflight` to verify formatting, linting, and tests pass. Fix any failures before proceeding.

### 2. Gather Context

- Run `git status` to see all changed files (never use `-uall` flag)
- Run `git diff` to see staged and unstaged changes
- Run `git log` to understand recent commit style
- Run `git diff main...HEAD` (or the appropriate base branch) to see the full diff for the PR

### 3. Update ROADMAP.md

This step happens before PR creation so the roadmap update is included in the PR diff — reviewers can see which roadmap items are being completed alongside the code that completes them.

- Read `handoff.md` to identify the current task (if it exists)
- Read `ROADMAP.md` and cross-reference with the git diff to determine which items this PR completes
- Change `- [ ]` to `- [x]` for completed items
- If unsure whether an item is fully addressed by this PR, ask the user before marking it
- If any items were updated, stage and commit:
  ```bash
  git add ROADMAP.md && git commit -m "docs: mark completed roadmap items"
  ```
- If no roadmap items apply, skip this step

### 4. Create the Pull Request

Use the PR template format from `.github/PULL_REQUEST_TEMPLATE.md`. Fill in all sections based on the changes.

The PR title should follow conventional commit format and be under 70 characters:
- `feat: add device validation endpoint`
- `fix: resolve timeout in CDC worker`
- `refactor: extract shared gRPC middleware`
- `chore: update dependencies`

Push the branch first, then create the PR:

```bash
git push -u origin <branch-name>
```

```bash
gh pr create --title "<conventional commit style title>" --body "$(cat <<'EOF'
## Summary

<1-3 sentence description of what this PR does and why>

## Changes

<bulleted list of key changes>

## Type of Change

- [x] `<type>`: <description>

## Test Plan

- [x] Unit tests pass (`mise run test:unit`)
- [x] Format check passes (`mise run fmt:check`)
- [x] Clippy check passes (`mise run clippy:check`)

## Additional Context

<any extra context or notes>
EOF
)"
```

If `handoff.md` has a current task with a GitHub issue number, link the PR to the issue by adding `Closes #<number>` to the summary section.

### 5. Update handoff.md

Read `.claude/skills/handoff.md` for the canonical format and **Complete Task** operation, then update `handoff.md` locally (do not commit it — it's gitignored). Record the PR number, title, session summary, and whether the task is fully or partially complete. If partially complete, describe what remains and keep the current task populated.

### 6. Report

Present the results to the user:

- Return the PR URL
- List any roadmap items that were marked complete (if any)
- Note that handoff.md was updated with the session summary
