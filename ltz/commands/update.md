---
description: Re-analyzes the active plan with new context or changed requirements, updating Goal, Scope, Phases/Tasks, and Progress while preserving completed work.
---

You are executing the "/ltz:update" protocol. Your job is to act as the Architect and re-analyze an existing plan in light of new information or changed requirements. Do NOT write application code.

**Write ownership:** This skill writes ONLY to `.plan/<feature>/plan.md` and `.plan/<feature>/progress.md`. It MUST NOT modify `phases/phase-N.md` or `findings.md` — those are owned by `plan-phase` and `execute` respectively.

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name.
   - If the file is missing or empty, tell me: *"No active plan is set."* Then use `AskUserQuestion` with the question *"Would you like to choose a plan to update?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/ltz:choose-plan` and resume from step 1 after it completes.
   - **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/ltz:choose-plan`, then resume from step 1.

2. **Read the Plan:** Read `.plan/[active-name]/plan.md`, `.plan/[active-name]/progress.md`, and `.plan/[active-name]/findings.md`. Print the full contents of BOTH `plan.md` and `progress.md` inline so the user can see the current state.

3. **Classify Plan Type:** Determine whether this is a **feature plan** (multi-phase) or a **task plan** (single task) by inspecting the structure of `progress.md`:
   - **Feature plan:** `progress.md` has a multi-row status table (e.g. `| Phase 1 — … | … |`).
   - **Task plan:** `progress.md` has a single `Status:` line (no table).

4. **Clarity Check:** Evaluate whether the update request (from ARGUMENTS) contains enough information to meaningfully revise the plan. The request is sufficiently detailed if it has: a clear reason for the change and enough specifics to know what to add, remove, or modify.
   - **If underspecified:** Use `AskUserQuestion` to ask 2-6 targeted questions one at a time. Cover as many as are relevant: what changed and why, new or removed scope boundaries, new constraints or risks, priority shifts, success criteria changes. Wait for answers before proceeding.
   - **If already detailed:** Note "Request is clear — proceeding to update." and continue.

5. **Analyze Context:** Briefly scan relevant project files (e.g. `README.md`, `package.json`) to understand the current tech stack and constraints, same as you would for a fresh plan.

6. **Re-draft the Plan:** Update `plan.md` (and `progress.md` where indicated) following the appropriate flow based on the plan type detected in step 3. **Preserve all completed work** — rows with status `done` in `progress.md` MUST remain unchanged.

   **If Feature Plan (multi-phase):**
   Update the plan following the same structure as `/ltz:plan-feature`:
   - **Goal (in `plan.md`):** Update the 1-2 sentence description if the goal has changed.
   - **Scope (in `plan.md`):** Update "In Scope" and "Out of Scope" bullet points to reflect the new requirements.
   - **Phases (in `plan.md`):** Update the plain bulleted list of phases (no `[ ]` checkboxes — those live in `progress.md`). You may add new phases, remove or rename phases that are still `backlog` / `ready-for-dev`, or reorder them. For each phase, include its MoSCoW priority and dependencies. **Do NOT modify or remove the textual entry for any phase whose row in `progress.md` is `done`.**
   - **Progress (in `progress.md`):** Sync the status table with the updated phase list. New phases start as `backlog` / `[ ]`. Rows with status `done` MUST remain unchanged. Removed phases are deleted from the table.
   - **Constraint:** Keep phases high-level. Do NOT write granular implementation details — those go in `phases/phase-N.md` via `/ltz:plan-phase`.

   **If Task Plan (single task):**
   Update the plan following the same structure as `/ltz:plan-task`:
   - **Goal (in `plan.md`):** Update the 1-2 sentence description if the goal has changed.
   - **Acceptance Criteria (in `plan.md`):** Update the 2-3 bullet points defining success.
   - **Atomic Tasks (in `plan.md`):** Update the bite-sized steps. For each task, explicitly list the file paths to create or modify. **Do NOT modify tasks already marked as complete.**
   - **Verification (in `plan.md`):** Update the exact terminal commands to confirm the task is complete.
   - **Status (in `progress.md`):** If new work was added, overwrite `progress.md` with `Status: \`backlog\``. If the previous status was `done`, do NOT downgrade it.

7. **Review Loop:** Print the full updated contents of `plan.md` inline. Then use `AskUserQuestion` with the question *"Does the updated plan look correct?"* and options:
   - `approve — save and stop`
   - `execute — save and start first phase now`
   - `change — I have feedback (follow up with details)`
   - `abort — discard all changes`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to `plan.md`, re-print, and ask again. Repeat until resolved.
   - **If `abort`:** Restore `plan.md` AND `progress.md` to their original contents (undo every change made during this run). Confirm *"Changes discarded."* and stop.
   - **If `approve`:** Confirm *"Plan updated. Run `/ltz:go` to execute."* and stop.
   - **If `execute`:** Confirm *"Plan updated — starting first phase now."* then immediately run the plan-phase + execute flow for the first ready phase inline.