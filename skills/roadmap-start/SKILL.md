---
description: Use when the user wants to pick up the next task from the roadmap, start working on a roadmap item, or says things like "what should I work on next", "next task", "start a new feature", "what's on the roadmap", or "pick up a task". Also use this when a previous task was just completed and the user wants to continue working. Reads ROADMAP.md, checks handoff.md for session continuity, and kicks off planning.
user-invocable: true
---

# Roadmap Start

Pick up the next roadmap task, create a branch, plan the implementation, and set up handoff context for session continuity.

## Steps

### 1. Check handoff.md for In-Progress Work

Read `.claude/skills/handoff.md` for the canonical format, then check if `handoff.md` exists in the project root and parse the **Current Task** section. If the file doesn't exist or Current Task is `_None_`, there is no active task.

- If there's an active task, present it to the user:
  - Show the task issue, phase, branch, and status
  - Ask: "You have an in-progress task. Continue with it, or pick a new one?"
  - If continuing: check out the existing branch, read the linked plan file, and resume from where the status left off
- If there's no active task, proceed to step 2

### 2. Find the Next Unchecked Roadmap Item

Read `ROADMAP.md` and scan for unchecked items (`- [ ]` lines).

- Group candidates by phase
- Default suggestion: the first unchecked item in the earliest incomplete phase
- Present candidates to the user and let them choose which task to work on
- Each item should show its issue number, description, and phase

### 3. Gather Task Context

Once the user selects a task:

- Extract the GitHub issue number (`#NN`) from the roadmap line
- If the item has no issue number or says `TBD`, suggest creating one with `/create_issue` first and stop
- Fetch the issue details:
  ```bash
  gh issue view <number> --json title,body,labels,comments
  ```
- Read the phase's rationale blockquote from ROADMAP.md for strategic context
- Check `.thoughts/plans/` for any existing plans related to this task (search for the issue number in filenames)

### 4. Create a New Branch

Before switching branches, check for uncommitted changes with `git status`. If there are uncommitted changes, ask the user whether to stash, commit, or abort before proceeding.

Create a feature branch from `main`:

```bash
git checkout main && git pull origin main
git checkout -b feat/<short-kebab-description>
```

- Derive the branch name from the task description
- Format: `feat/<short-kebab-description>` (e.g., `feat/device-ownership-validation`)
- Keep it concise — 3-5 words max

### 5. Create the Plan

Use the `/create_plan` skill to research the codebase and design the implementation. Pass in the issue context gathered in step 3 so the planning starts informed.

The plan file should be written to `.thoughts/plans/YYYY-MM-DD-<issue-number>-<description>.md`.

After the plan is accepted, post it as a comment on the backing GitHub issue so the plan is discoverable outside of Claude Code:

```bash
gh issue comment <number> --body "$(cat <<'EOF'
## Implementation Plan

<plan content>
EOF
)"
```

### 6. Update handoff.md

Read `.claude/skills/handoff.md` for the canonical format and **Initialize** operation, then create `handoff.md` in the project root with the selected task, branch, plan, and status set to `Plan created — ready for implementation`. Populate **What's Left** from the plan's phases/steps as checkboxes.

### 7. Present Next Steps

After everything is set up, present a summary:

```
Task picked up and plan created:

**Task**: #<number> — <description>
**Plan**: <plan file path>
**Branch**: <branch name>

Next steps:
- Run `/implement_plan <plan-path>` to start implementation
- When complete, run `/roadmap-complete` to create a pull request (it will update the roadmap automatically)
```
