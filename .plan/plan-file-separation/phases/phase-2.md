# Phase 2 — Phase Detailing Skill

## Acceptance Criteria
- `/ltz:plan-phase` reads the next `backlog` phase from `progress.md` (not from `plan.md`).
- Granular implementation guide is written to a NEW file `.plan/<feature>/phases/phase-N.md`, where `N` is the 1-based row index of the target phase. Nothing is appended to `plan.md`.
- On `approve` or `execute`, only the matching row in `progress.md` is updated (`backlog` → `ready-for-dev`). The skill explicitly states it never modifies `plan.md`.

## Atomic Tasks

1. **Read `progress.md` in addition to `plan.md` / `findings.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` step 2 (line 17): change "Read `.plan/[active-name]/plan.md` and `.plan/[active-name]/findings.md`" to also read `.plan/[active-name]/progress.md`.

2. **Find the next backlog row in `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` step 3 (lines 18–19): rewrite "Look at the `## Progress` table" so the skill scans `progress.md`'s status table for the first `backlog` row, and captures its 1-based index as `N`.

3. **Redirect the granular guide to `phases/phase-N.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` step 4 (lines 20–24): replace the "append … directly below the checklist in the plan file" instruction with: create a NEW file `.plan/[active-name]/phases/phase-N.md` containing the same Acceptance Criteria / Atomic Tasks / Exact Files / Verification structure. Do NOT touch `plan.md`.

4. **Update review-loop `approve` branch.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` step 5 `approve` clause (line 31): change "In `plan.md`, find the `## Progress` table and update the current phase's status" to "In `progress.md`, update the row for phase N from `backlog` to `ready-for-dev`". Add explicit "Do NOT modify `plan.md`."

5. **Update review-loop `execute` branch.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` step 5 `execute` clause (line 32): change the status-update target to `progress.md` (same row update as task 4). Add explicit "Do NOT modify `plan.md`."

6. **Add an ownership note near the top of the skill.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md` body (just below the "Your job is to act as the Systems Analyst" sentence): add one line stating that `plan-phase` writes to `phases/phase-N.md` and `progress.md` only, and never modifies `plan.md`.

## Verification

```bash
grep -n "phases/phase\|progress.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md
grep -nEi "append.*plan\.md|update.*in `plan\.md`|modify.*plan\.md|edit.*plan\.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-phase.md | grep -vi "do NOT\|never"
```

Expected: first grep finds at least one mention of `phases/phase` and several of `progress.md`. Second grep returns no lines (every remaining mention of writing to `plan.md` is wrapped in a "Do NOT" / "never" instruction).
