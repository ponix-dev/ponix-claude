# ponix-claude

Claude Code plugin for roadmap-driven development workflows — from issue creation through implementation to PR submission.

## Installation

```bash
/plugin marketplace add ponix-dev/ponix-claude
/plugin install ponix-workflows@ponix-claude
```

## Commands

Once installed, the following commands are available:

| Command | Description |
|---------|-------------|
| `/ponix-workflows:create-issue` | Create detailed GitHub issues with consistent structure, labels, and roadmap integration |
| `/ponix-workflows:create-roadmap` | Build a dependency-ordered project roadmap through guided research, interviews, and planning |
| `/ponix-workflows:roadmap-start` | Pick up the next roadmap task, create a branch, and plan the implementation |
| `/ponix-workflows:roadmap-complete` | Run pre-flight checks, update the roadmap, create a PR, and record the session |
| `/ponix-workflows:handoff` | Manage session handoff context — check status, log blockers, record decisions |

## Updating

```bash
claude plugin marketplace add ponix-dev/ponix-claude
claude plugin install ponix-workflows
```
