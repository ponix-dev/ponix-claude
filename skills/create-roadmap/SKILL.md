---
description: Create a dependency-ordered project roadmap through guided research, user interviews, and structured planning. Use this skill when the user wants to create a roadmap, define project direction, plan upcoming phases, build a development plan, or says things like "create a roadmap", "plan the project", "what should we build next", "let's define our phases", "build a roadmap", "map out the work". This is a multi-step orchestration — it researches the codebase, interviews the user in 3 rounds, enters plan mode for approval, generates ROADMAP.md, and creates GitHub issues for every work item.
user-invocable: true
---

# Create Roadmap

Build a project roadmap from scratch through a structured process: research → interview → plan → generate → create issues. The output is a `ROADMAP.md` with dependency-ordered phases, checkable work items linked to GitHub issues, and explicit rationale for ordering decisions.

## Why This Process Works

Roadmaps fail when they're either too vague ("improve performance") or too rigid (detailed task lists that don't explain ordering). The pattern here produces roadmaps that are useful because:

1. **They start from reality** — the research phase grounds everything in what actually exists today
2. **They capture the user's vision** — the interview extracts concrete end-state capabilities, not vague goals
3. **They explain ordering** — every phase has a "why here?" rationale so future readers understand the dependency chain
4. **They're actionable** — every work item links to a GitHub issue with full context
5. **They acknowledge uncertainty** — Open Questions capture things deliberately left unresolved

## Roadmap Structure

The output follows this proven structure:

- **Narrative title**: `Roadmap: A → B → C` — captures the journey
- **"Where You Are"**: Concrete current state, grounded in what the codebase actually does today
- **"Where You're Going"**: End-state vision in 1-2 sentences
- **Dependency-ordered phases**: Ordered by "what must exist before the next thing works"
- **Work items per phase**: Checkboxes with `#NN` issue refs, grouped by sub-theme when a phase has natural clusters
- **"Why here?" blockquotes**: Per-phase rationale explaining ordering
- **Open Questions**: Things deliberately left unresolved

## Flow

### Phase 1: Research (automatic — no user interaction)

Silently gather context before asking the user anything. The goal is to show up to the interview already informed, so the user corrects your understanding rather than explaining from scratch.

- Read `CLAUDE.md`, `README.md`, and key architectural files to understand current capabilities
- Read the project manifest for workspace/package structure
- Run `gh issue list --limit 200 --json number,title,labels,state` to pull the existing backlog
- Run `gh label list` to understand existing categorization
- Check for an existing `ROADMAP.md` — if one exists, ask the user whether to replace or build on it

Build a model of:
- What capabilities exist today (not aspirational — what actually works)
- What's already tracked as issues (and what labels/groupings exist)
- What gaps are implicit in the codebase but not tracked anywhere

### Phase 2: Interview (3 rounds)

The interview is structured in 3 rounds. Each round builds on the previous one, narrowing from broad vision to concrete ordering. Push for specifics — vague answers produce vague roadmaps.

#### Round 1 — Current state + vision

Present what you learned from the codebase. Be specific and concrete:

> "Here's what I understand about where the project is today: [specific capabilities, architecture, what works, what's missing]"

Then ask:
- "Is this accurate? What would you adjust?"
- "Where do you want to end up? Describe the end-state — what new capabilities, integrations, or behaviors should the system have?"

Push for **concrete capabilities** over vague goals:
- "Better performance" → "Sub-100ms p99 latency on envelope ingest at 10k msg/s"
- "Better observability" → "Distributed traces across all NATS consumers with Grafana dashboards"
- "AI integration" → "An agent that can observe device data, query historical context, and send downlink commands"

#### Round 2 — Capability gaps + grouping

Synthesize the delta between current state and vision into concrete gaps:

> "Based on the gap between where you are and where you're going, I see these major capability gaps: [list them]"

Then:
- Ask: "What am I missing? What should be added or removed?"
- Propose thematic groupings — these become phases or sub-sections
- Map existing GitHub issues to gaps where they fit
- Identify items that have no existing issue yet (these will become `TBD` entries, later turned into issues)

#### Round 3 — Ordering + open questions

Propose dependency-ordered phases with rationale for each. The ordering should follow "what must exist before the next thing works" — not priority or difficulty.

> "Here's the proposed phase ordering and why: [phases with rationale]"

Then ask:
- "Does this ordering make sense? Should anything move earlier or later?"
- "What decisions should we deliberately leave open for now?" (these become Open Questions)
- "Are there any items you want to explicitly exclude from the roadmap?"

### Phase 3: Plan (plan mode)

Enter plan mode to formalize everything before writing files or creating issues. This is the user's last checkpoint before execution.

Write the roadmap plan to the plan file:
- Proposed phases with ordering rationale
- Work items per phase (existing issues as `#NN`, new items as `TBD`)
- Labels to create for new issues
- Open questions section
- The exact ROADMAP.md structure you'll generate

Exit plan mode for user approval. No files are created and no issues are opened until the plan is approved.

### Phase 4: Generate ROADMAP.md

Write `ROADMAP.md` following this template:

```markdown
# Roadmap: <Current State> → <Mid Journey> → <End State>

## Where You Are

<Concrete current state — what exists today, grounded in reality. Not aspirational.>

## Where You're Going

<End-state vision in 1-2 sentences.>

---

## Phase 1: <Phase Name>

<1-2 sentence description of what this phase accomplishes.>

- [ ] #NN — <description>
- [ ] #NN — <description>
- [ ] TBD — <description>

> **Why first:** <rationale for why this phase comes before the others>

---

## Phase 2: <Phase Name>

<description>

### <Sub-theme> (optional — use when a phase has natural clusters)

- [ ] #NN — <description>

### <Another Sub-theme>

- [ ] TBD — <description>

> **Why second:** <rationale>

---

## Open Questions to Resolve Along the Way

- **<Question>** — <context and why it's deliberately left open>
```

Formatting rules:
- Items with existing GitHub issues get `#NN` references
- Items without issues get `TBD` markers (replaced in the next phase)
- Each phase gets a "Why here?" blockquote explaining its ordering
- Use `---` horizontal rules between phases for visual separation
- Sub-themes within a phase get `###` headings
- Tables are fine for structured comparisons (e.g., tool descriptions, trigger types)
- Include a Mermaid diagram if it helps illustrate the end-state architecture or data flow

### Phase 5: Create Issues for TBD Items

For each `TBD` item in the generated ROADMAP.md, create a GitHub issue using the `/create-issue` pattern:

1. Derive the issue title from the roadmap item description
2. Generate the issue body with:
   - **Summary**: from the roadmap item + phase context
   - **Context**: how it fits into the phase and the broader roadmap
   - **Key characteristics**: scope and constraints derived from the roadmap
   - **Dependencies**: other issues in earlier phases or the same phase that must come first
3. Apply labels: phase label + relevant type/area labels (create labels if they don't exist)
4. Create via `gh issue create`
5. Update ROADMAP.md to replace that item's `TBD` with the new `#NN`

After all issues are created, commit the final roadmap:
```bash
git add ROADMAP.md && git commit -m "docs: create ROADMAP.md with issue references"
```

### Phase 6: Report

Present the results:
- Link to the ROADMAP.md file
- Total phases created
- Issues created (with URLs) vs. existing issues mapped
- Open questions captured
- Suggested next step: "Run `/roadmap-start` to pick up the first task"
