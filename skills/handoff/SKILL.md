---
description: Use when the user wants to check the status of their current task, update session notes, log blockers or key decisions, or says things like "where did I leave off", "what's my current task", "update handoff", "log a blocker", "wrap up for today", or "session status". Also use this skill as a reference whenever another skill needs to read or write handoff.md — it defines the canonical format and update operations.
user-invocable: true
---

# Handoff

Manage the `handoff.md` file that bridges context between Claude Code sessions. This file lives in the project root and is gitignored — it's local-only context so each session can pick up where the last one left off.

## When Invoked Directly

Read `handoff.md` and present the current state to the user. Then ask what they'd like to update:

- **Show status**: Display the current task, branch, plan, status, and what's left
- **Update session summary**: Record what was accomplished in this session
- **Log a blocker**: Add to the Blockers section
- **Record a decision**: Add to Key Decisions with brief rationale
- **Mark progress**: Check off completed items in What's Left
- **Wrap up**: Update the session summary and status to reflect stopping point (e.g., "Paused — completed phases 1-2, phase 3 in progress")

If `handoff.md` doesn't exist, tell the user there's no active handoff and suggest `/roadmap-start` to pick up a task.

## handoff.md Format

This is the canonical format. Other skills that create or update `handoff.md` should follow this structure.

```markdown
# Handoff

## Current Task

- **Issue**: #<number> — <short description>
- **Phase**: <phase name from ROADMAP.md>
- **Branch**: `feat/<branch-name>`
- **Plan**: `.thoughts/plans/<plan-file-name>.md`
- **Status**: <current status — see Status Values below>

## Last Session Summary

<concise bullet points of what was accomplished>

## What's Left

- [ ] <plan phase or step description>
- [ ] <plan phase or step description>
- [ ] PR creation

## Blockers

_None currently._

## Key Decisions

<notable architectural or design decisions with brief rationale>

## Recent PRs

- PR #<number> — <title> (<YYYY-MM-DD>)
```

### Status Values

Use these status strings to communicate where work stands:

| Status | Meaning |
|--------|---------|
| `Plan created — ready for implementation` | Plan is done, no code written yet |
| `In progress — <what's actively being worked on>` | Implementation underway |
| `Paused — <stopping point description>` | Session ended mid-work |
| `Partially complete — <what remains>` | PR submitted but task isn't fully done |
| `Ready for next task` | Task complete, no active work |

### Field Reference

| Field | When to set | Who sets it |
|-------|-------------|-------------|
| **Current Task** | When picking up a new task | `/roadmap-start` |
| **Status** | On any state change | Any skill or `/handoff` directly |
| **Last Session Summary** | After creating a PR or wrapping up | `/roadmap-complete` or `/handoff` |
| **What's Left** | When plan is created; updated as phases complete | `/roadmap-start`, `/handoff` |
| **Blockers** | When a blocker is discovered | `/handoff` directly |
| **Key Decisions** | When a significant design choice is made | `/handoff` directly |
| **Recent PRs** | After a PR is created | `/roadmap-complete` |

## Operations for Other Skills

Other skills reference this section when they need to read or write `handoff.md`.

### Read Current Task

Read `handoff.md` and parse the **Current Task** section. If the file doesn't exist or Current Task is `_None_`, there is no active task.

### Initialize (used by /roadmap-start)

Create `handoff.md` with:
- **Current Task**: populated from the selected roadmap item
- **Status**: `Plan created — ready for implementation`
- **What's Left**: populated from the plan's phases as checkboxes, plus a final `PR creation` item
- All other sections empty/default

### Complete Task (used by /roadmap-complete)

Update `handoff.md` after a PR is created:
- **Last Session Summary**: concise summary of what the PR accomplishes
- **Recent PRs**: append `- PR #<number> — <title> (<YYYY-MM-DD>)`
- If task is fully complete:
  - **Current Task**: set to `_None — pick up the next task with /roadmap-start_`
  - **Status**: `Ready for next task`
  - **What's Left**: clear
- If task is partially complete:
  - Keep **Current Task** populated
  - **Status**: `Partially complete — <what remains>`
  - **What's Left**: check off completed items, leave remaining

### Mark Progress

Check off items in **What's Left** and update **Status** to reflect current state.
