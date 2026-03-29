# Update Plan Skill — Rewrite

## Goal

Rewrite the `update-plan` skill so it re-runs the full plan-feature or plan-task analysis flow against an existing active plan, updating its Goal, Scope, Phases/Tasks, and Progress — rather than just offering a lightweight "add/edit/re-prioritize" menu.

## Scope

**In Scope:**
- Rewrite `skills/update-plan/SKILL.md` with the new prompt
- Detect plan type (multi-phase feature vs single task) from the existing `plan.md` structure
- Mirror the clarity check, context analysis, drafting, and review loop from plan-feature/plan-task
- Preserve existing plan state (completed phases stay marked, findings.md untouched)

**Out of Scope:**
- Changes to plan-feature, plan-task, execute, or any other skill
- Changes to the `.plan/` directory structure or conventions
- New frontmatter fields or CLI integration changes

## Phases

- [x] **Phase 1 — Rewrite update-plan SKILL.md** *(Must Have)*
  Replace the existing lightweight amendment prompt with a full analysis flow that: resolves the active plan, reads and classifies it, runs the appropriate plan-feature or plan-task update flow, preserves completed work, and includes the standard review loop.

## Progress

| Phase | Status |
|-------|--------|
| Phase 1 — Rewrite update-plan SKILL.md | `done` |
