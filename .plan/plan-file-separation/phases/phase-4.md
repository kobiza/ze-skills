# Phase 4 — Auxiliary Skills

## Acceptance Criteria
- `list`, `choose-plan`, and `go` read all status data from `progress.md` (not from `plan.md`'s `## Progress` table). They never write to `plan.md`.
- `update` reads and may modify both `plan.md` and `progress.md`; the abort branch restores both files; the feature-plan re-draft writes plain bullets (no `[ ]`) to `plan.md` and syncs the status table in `progress.md`.
- `cleanup` continues to delete folders without referencing the internal layout.
- Each touched skill carries a clear write-ownership note at the top.

## Atomic Tasks

1. **`list.md` — read status from `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/list.md` step 3 (line 9): change "read its `plan.md` and count the rows in the `## Progress` table" → "read its `progress.md`. For feature plans (table format) count rows by status; for task plans (single `Status:` line) report the status directly". Add a one-line ownership note at the top of the body: "This skill is read-only — it never modifies `plan.md` or `progress.md`."

2. **`choose-plan.md` — read status from `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/choose-plan.md` step 3 (line 10): same change as task 1 (read `progress.md`, handle both formats). Add the same read-only ownership note at the top of the body.

3. **`go.md` — read `progress.md`, detect plan type from it.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/go.md` Step 2 (lines 21–33):
   - Line 21: also read `.plan/[active-name]/progress.md`.
   - Line 25: change "If task plan (has `Status:` line, not a phase table)" → "If task plan (`progress.md` contains a single `Status:` line, no table)".
   - Line 27: change "Update Status to `ready-for-dev`" → "Overwrite `progress.md` with `Status: \`ready-for-dev\``".
   - Line 30: change "If feature plan (has `## Progress` table)" → "If feature plan (`progress.md` contains a status table)".

4. **`go.md` — re-check progress from `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/go.md` Step 3 line 47: change "re-read `plan.md` to check remaining phases" → "re-read `progress.md` to check remaining phases". Add a read-only ownership note at the top of the body covering plan.md.

5. **`update.md` — read & print both files; classify from `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/update.md`:
   - Step 2 (line 18): also read `progress.md`; print BOTH `plan.md` and `progress.md` inline.
   - Step 3 (lines 20–22): detect plan type from the structure of `progress.md` instead of `plan.md` ("Feature plan: `progress.md` has a multi-row status table"; "Task plan: `progress.md` has a single `Status:` line").

6. **`update.md` — feature path edits.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/update.md` step 6 Feature Plan branch (lines 32–38):
   - "Phases" bullet: update to plain bulleted list in `plan.md` (no `[ ]`). New phases are simply added as bullets; renamed/removed phases are edited or deleted.
   - "Progress" bullet: rename to refer to `progress.md`. Sync the status table there. Completed (`done`) rows MUST remain unchanged. New phases start as `backlog` / `[ ]`.
   - "Do NOT modify `[x]` phases" → "Do NOT modify rows whose status is `done` in `progress.md`."

7. **`update.md` — task path edits.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/update.md` step 6 Task Plan branch (lines 40–46): drop the "Progress: Update the `## Progress` status" bullet from the plan.md spec. Add a separate instruction: "If the status changes, overwrite `progress.md` accordingly. If status was `done`, do NOT downgrade it."

8. **`update.md` — abort restores both files.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/update.md` step 7 abort clause (line 55): change "Restore `plan.md` to its original content" → "Restore `plan.md` AND `progress.md` to their original contents (undo every change made during this run)."

9. **`update.md` — ownership note.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/update.md` body (just below the "Do NOT write application code." sentence in the opening paragraph): add a write-ownership note: "This skill writes ONLY to `.plan/<feature>/plan.md` and `.plan/<feature>/progress.md`. It never modifies `phases/phase-N.md` or `findings.md`."

## Verification

```bash
echo "=== read of progress.md in each aux skill ==="
grep -n "progress.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/list.md /Users/kobiz/my_projects/ze-skills/ltz/commands/choose-plan.md /Users/kobiz/my_projects/ze-skills/ltz/commands/go.md /Users/kobiz/my_projects/ze-skills/ltz/commands/update.md

echo "=== leftover '## Progress' references that read FROM plan.md ==="
grep -nE "## Progress\` (table|status)" /Users/kobiz/my_projects/ze-skills/ltz/commands/list.md /Users/kobiz/my_projects/ze-skills/ltz/commands/choose-plan.md /Users/kobiz/my_projects/ze-skills/ltz/commands/go.md /Users/kobiz/my_projects/ze-skills/ltz/commands/update.md | grep -v "progress.md"

echo "=== full directory tree of the active plan ==="
ls -la /Users/kobiz/my_projects/ze-skills/.plan/plan-file-separation/
ls -la /Users/kobiz/my_projects/ze-skills/.plan/plan-file-separation/phases/
```

Expected: every aux skill mentions `progress.md` at least once. The `## Progress` grep returns nothing or only lines that reference `progress.md`. The plan folder shows `plan.md`, `progress.md`, `findings.md`, and `phases/phase-1.md…phase-4.md`.
