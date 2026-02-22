---
description: Create detailed GitHub issues for this project with consistent structure, proper labels, and roadmap integration. Use this skill whenever the user wants to create an issue, file a bug, track a feature, log a task, or says things like "create an issue", "file a ticket", "open an issue for X", "track this", "we need an issue for Y". Also use this when another skill (like create-roadmap) needs to create issues programmatically — the template and process are the same either way.
user-invocable: true
---

# Create Issue

Create a well-structured GitHub issue that fits into the project's existing issue conventions. Issues created by this skill follow a consistent template so that anyone reading the backlog can quickly understand what needs to happen, why it matters, and what it depends on.

This skill works in two modes:
- **Interactive**: User invokes directly — ask them for details
- **Programmatic**: Another skill provides the details — skip the interview and go straight to creation

## Steps

### 1. Gather Information

**Interactive mode** (user invoked directly): Ask for what you need to generate a good issue. Don't ask for everything upfront — start with the basics and fill in context yourself from the codebase.

- **What's the issue about?** Get a title and 1-3 sentence description of intent
- **Where does it fit?** Which roadmap phase or area of the project? (you can often infer this)
- **Any specific labels?** (optional — suggest based on context if the user doesn't specify)

**Programmatic mode** (called by another skill): Title, description, phase, labels, and dependencies are provided as inputs. Skip the interview entirely.

### 2. Build Context

Read the codebase to make the issue richer than what the user provided:

- If `ROADMAP.md` exists, read it to identify the relevant phase, related items, and dependencies
- Check existing issues (`gh issue list --limit 50 --json number,title,labels`) to avoid duplicates and find related work
- Read relevant source files if the issue is about a specific module or subsystem — this helps you write a more grounded Context section

The goal is to write an issue that someone unfamiliar with the conversation could pick up and understand. The codebase context helps you do that.

### 3. Ensure Labels Exist

Check existing labels and create any that are missing:

```bash
gh label list --limit 200
```

Create missing labels:
```bash
gh label create <name> --description "<description>" --color "<hex>"
```

Common label conventions:
- **Phase labels**: `phase:1`, `phase:2`, etc. (ties to roadmap phases)
- **Type labels**: `enhancement`, `bug`, `refactor`, `chore`
- **Area labels**: named after the relevant module or subsystem (e.g., `analytics-worker`, `common`)

### 4. Write the Issue Body

Follow this template — it's what makes the project's issues scannable and consistent:

```markdown
## Summary

**Roadmap Phase:** <phase number> — <phase name>

<1-3 sentences: what this is and why it matters>

## Context

<How this fits into the system. What it enables. What it connects to.>

**Key characteristics:**
- <scope, constraints, key decisions — 3-6 bullets>

## Dependencies

- #<number> — <description>
```

**Template rules:**
- Omit the "Roadmap Phase" line if this issue doesn't belong to a roadmap phase
- The Summary answers "what and why" in 1-3 sentences — not a paragraph
- The Context section is for someone who doesn't know the codebase. Explain how this piece fits into the bigger picture
- Key characteristics are the specifics: what's in scope, what's not, what decisions are embedded in this issue
- Dependencies lists issues that must be completed first. Write `None` if there are none

**Example:**

```markdown
## Summary

**Roadmap Phase:** 1 — Core Foundation

Add input validation to the user registration endpoint — reject malformed requests at the API boundary before they reach the service layer.

## Context

Currently the registration endpoint passes raw input directly to the service layer, which means invalid data (missing fields, bad email format) produces cryptic database errors instead of clear 400 responses. Adding validation at the API boundary gives callers actionable error messages and prevents invalid data from propagating through the system.

**Key characteristics:**
- Validate required fields, email format, and password strength at the handler level
- Return structured error responses with per-field messages
- Must not break existing valid registration flows
- Validation logic should be reusable across other endpoints

## Dependencies

None
```

### 5. Create the Issue

```bash
gh issue create \
  --title "<concise imperative title>" \
  --label "<label1>,<label2>" \
  --body "$(cat <<'EOF'
<issue body>
EOF
)"
```

Capture the issue URL and number from the output.

### 6. Update ROADMAP.md (if applicable)

If the newly created issue corresponds to a `TBD` item in `ROADMAP.md`:
- Replace `TBD` with `#<issue-number>` on the matching line
- Stage and commit:
  ```bash
  git add ROADMAP.md && git commit -m "docs: add issue #<number> to roadmap"
  ```

If the issue doesn't appear in the roadmap, skip this step.

### 7. Report

Tell the user what happened:
- Issue URL (clickable)
- Issue number
- Labels applied
- Whether ROADMAP.md was updated (and which line)
