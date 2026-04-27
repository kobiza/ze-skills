# Phase 1 — Spec & Creation Skills

## Acceptance Criteria
- `CLAUDE.md` documents the new `.plan/<feature>/` layout (`plan.md`, `progress.md`, `findings.md`, `phases/phase-N.md`) and a write-ownership table making clear which skill writes which file.
- After `/ltz:plan-feature`, the resulting `plan.md` contains Goal / Scope / Phases as plain bullets (no `[ ]` checkboxes, no `## Progress` table). A sibling `progress.md` holds the status table with every phase as `backlog`. An empty `phases/` directory is created.
- After `/ltz:plan-task`, the resulting `plan.md` contains Goal / Acceptance Criteria / Atomic Tasks / Verification only (no `## Progress` section). A sibling `progress.md` holds `Status: backlog`. Tasks do not get a `phases/` directory.

## Atomic Tasks

1. **Update layout diagram in CLAUDE.md.**
   Modify `/Users/kobiz/my_projects/ze-skills/CLAUDE.md` lines 44–50: expand the `.plan/` tree to show `plan.md`, `progress.md`, `findings.md`, and `phases/phase-N.md`. Add a one-line description per file explaining its role.

2. **Add a "Write ownership" subsection to CLAUDE.md.**
   In `/Users/kobiz/my_projects/ze-skills/CLAUDE.md`, immediately after the layout diagram, add a small table mapping each skill to the files it writes:
   - `plan`, `plan-feature`, `plan-task`, `update` → `plan.md` (creators/owners), plus `progress.md` and `findings.md` on initial scaffold.
   - `plan-phase` → `phases/phase-N.md` (creates), `progress.md` (updates).
   - `execute` → `progress.md` (updates), `findings.md` (appends).
   - `list`, `choose-plan`, `go`, `cleanup` → read-only on `plan.md`; `cleanup` deletes the folder.

3. **Update key-behaviors bullets in CLAUDE.md.**
   In `/Users/kobiz/my_projects/ze-skills/CLAUDE.md` lines 52–59, rewrite the descriptions for `/ltz:plan-phase` and `/ltz:execute` so they reference `phases/phase-N.md` and `progress.md` instead of `plan.md`.

4. **Scaffold the new file set in plan-feature.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md` step 5 (lines 15–17): change "Create Files" to create four artifacts — `plan.md`, `progress.md`, `findings.md`, and an empty `phases/` directory.

5. **Strip the Progress table from plan-feature's plan.md spec.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md` step 7 (lines 19–30): remove the `## Progress` sub-bullet and its example block from the `plan.md` template. Replace the `[ ]` checklist instruction with plain bullets.

6. **Add a Progress-file spec to plan-feature.**
   In `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md` step 7, add a sibling instruction: write the `## Progress` table (with `Phase | Status` columns and a `[ ]/[x]` Done column) into `progress.md`, with every phase row starting as `backlog` and `[ ]`.

7. **Update plan-feature's review-loop language.**
   In `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md` step 9, ensure "Print the full contents of `plan.md`" still reads correctly given the new content (Progress table no longer present in plan.md). Adjust copy if needed so the user sees both files were created.

8. **Add progress.md to plan-task scaffold.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-task.md` step 4 (lines 16–20): include `progress.md` with body `Status: \`backlog\`` in the scaffolded files. Tasks do NOT get a `phases/` directory.

9. **Strip the `## Progress` section from plan-task's plan.md spec.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-task.md` step 5 (lines 22–27): remove the `Progress: Status: \`backlog\`` bullet so `plan.md` has Goal / Acceptance Criteria / Atomic Tasks / Verification only.

10. **Redirect plan-task status writes to progress.md.**
    Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/plan-task.md` steps 6 and 9 (lines 37–38, 50): change every "Update `## Progress` in `plan.md`" to "Overwrite `progress.md` with `Status: \`<new-status>\``". Remove the "append `**Status: Done ✓**` at the very end of the file" line — that wrote to plan.md.

## Verification

```bash
grep -n "progress.md\|phases/" /Users/kobiz/my_projects/ze-skills/CLAUDE.md
grep -nE "^- |table" /Users/kobiz/my_projects/ze-skills/CLAUDE.md | head -30
grep -nc "## Progress" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md
grep -nc "## Progress" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-task.md
grep -n "progress.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-feature.md
grep -n "progress.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/plan-task.md
```

Expected: CLAUDE.md mentions both `progress.md` and `phases/`; the two skill files have **zero** `## Progress` matches and **at least one** `progress.md` reference each.
