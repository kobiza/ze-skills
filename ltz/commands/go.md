---
description: Continues the active plan to completion — assesses current state, then loops plan-phase and execute until all phases are done.
---

You are executing the "/ltz:go" protocol. Your job is to drive an existing plan to completion by delegating to existing skills. Do NOT create new plans or duplicate planning/execution logic.

**Write ownership:** `/ltz:go` itself does not write to `plan.md` — it only orchestrates other skills. For task plans, it writes status to `progress.md` directly. For feature plans, it delegates all phase-file and progress-file writes to `plan-phase` and `execute`.

## Step 1 — Resolve Active Plan

Read `.plan/.active` to get the current plan name.

- If the file is missing or empty: *"No active plan. Run `/ltz:plan` to start one or `/ltz:list` to browse."* Stop.
- If a plan name is found, use `AskUserQuestion` with *"Active plan: **[name]**. Continue with this plan?"* and options:
  - `Yes, continue`
  - `No, choose a different plan`
  - `Cancel`
- **If "No":** Invoke `/ltz:choose-plan`, then restart from Step 1.
- **If "Cancel":** Stop.

## Step 2 — Assess State

Read `.plan/[active-name]/plan.md`, `.plan/[active-name]/progress.md`, and `.plan/[active-name]/findings.md` to understand the current state.

Determine the plan type from the structure of `progress.md` and announce the status:

**If task plan** (`progress.md` contains a single `Status:` line, no table):
- If Status is `done` → *"This task is already complete."* Stop.
- If Status is `backlog` → Announce *"Task is planned but not yet started. Starting execution."* Overwrite `progress.md` with `Status: \`ready-for-dev\``, then go to Step 3.
- If Status is `ready-for-dev` → Announce *"Task is ready. Resuming execution."* Go to Step 3.

**If feature plan** (`progress.md` contains a status table):
- If all phases are `done` → *"All phases are complete. Nothing left to do."* Stop.
- Count and announce: *"Plan has N phases: X done, Y remaining."*
- Go to Step 3.

## Step 3 — Execute Loop

**If task plan:**
Invoke `/ltz:execute` — it handles the full task implementation, verification, and status update. Then go to Step 4.

**If feature plan:**
Loop over each phase that is NOT `done`, in order:

1. **If the phase is `backlog`:** Invoke `/ltz:plan-phase` — it finds the next backlog phase, details it with atomic tasks and verification commands, and runs a review loop.
   - If the user chooses `execute` in plan-phase's review loop, it executes the phase inline and marks it done. Continue to the next phase.
   - If the user chooses `approve`, the phase becomes `ready-for-dev`. Continue to step 2 below.
2. **If the phase is `ready-for-dev`:** Invoke `/ltz:execute` — it runs all ready-for-dev phases, marks them done.
3. After execution completes, re-read `progress.md` to check remaining phases.
   - If more non-done phases exist → Announce *"Phase complete ✓ — continuing to next phase."* Loop back to step 1.
   - If all phases are `done` → Go to Step 4.

## Step 4 — Wrap Up

Announce *"All done!"* and use `AskUserQuestion` with *"What would you like to do next?"* and options:
- `Review the changes`
- `Start something new`
- `Done for now`