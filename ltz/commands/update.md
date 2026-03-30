---
description: Re-analyzes the active plan with new context or changed requirements, updating Goal, Scope, Phases/Tasks, and Progress while preserving completed work.
---

You are executing the "/ltz:update" protocol. Your job is to act as the Architect and re-analyze an existing plan in light of new information or changed requirements. Do NOT write application code.

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

2. **Read the Plan:** Read `.plan/[active-name]/plan.md` and `.plan/[active-name]/findings.md`. Print the full contents of `plan.md` inline so the user can see the current state.

3. **Classify Plan Type:** Determine whether this is a **feature plan** (multi-phase) or a **task plan** (single task) by inspecting the structure of `plan.md`:
   - **Feature plan:** Has a `## Progress` table with multiple phase rows (e.g. `| Phase 1 — … |`).
   - **Task plan:** Has a `## Progress` section with a single `Status:` line (no table).

4. **Clarity Check:** Evaluate whether the update request (from ARGUMENTS) contains enough information to meaningfully revise the plan. The request is sufficiently detailed if it has: a clear reason for the change and enough specifics to know what to add, remove, or modify.
   - **If underspecified:** Use `AskUserQuestion` to ask 2-6 targeted questions one at a time. Cover as many as are relevant: what changed and why, new or removed scope boundaries, new constraints or risks, priority shifts, success criteria changes. Wait for answers before proceeding.
   - **If already detailed:** Note "Request is clear — proceeding to update." and continue.

5. **Analyze Context:** Briefly scan relevant project files (e.g. `README.md`, `package.json`) to understand the current tech stack and constraints, same as you would for a fresh plan.

6. **Re-draft the Plan:** Update `plan.md` following the appropriate flow based on the plan type detected in step 3. **Preserve all completed work** — phases/tasks marked `[x]` or with status `done` MUST remain unchanged.

   **If Feature Plan (multi-phase):**
   Update the plan following the same structure as `/ltz:plan` (feature path):
   - **Goal:** Update the 1-2 sentence description if the goal has changed.
   - **Scope:** Update "In Scope" and "Out of Scope" bullet points to reflect the new requirements.
   - **Phases:** Update the markdown checklist. You may add new `[ ]` phases, remove or rename `[ ]` (backlog/ready-for-dev) phases, or reorder them. For each phase, include its MoSCoW priority and dependencies. **Do NOT modify `[x]` phases** — they are complete.
   - **Progress:** Sync the `## Progress` table with the updated checklist. New phases start as `backlog`. Completed phases stay `done`. Removed phases are deleted from the table.
   - **Constraint:** Keep phases high-level. Do NOT write granular implementation details.

   **If Task Plan (single task):**
   Update the plan following the same structure as `/ltz:plan` (task path):
   - **Goal:** Update the 1-2 sentence description if the goal has changed.
   - **Acceptance Criteria:** Update the 2-3 bullet points defining success.
   - **Atomic Tasks:** Update the bite-sized steps. For each task, explicitly list the file paths to create or modify. **Do NOT modify tasks already marked as complete.**
   - **Verification:** Update the exact terminal commands to confirm the task is complete.
   - **Progress:** Update the `## Progress` status. If it was `done`, do NOT change it. If new work was added, set it back to `backlog`.

7. **Review Loop:** Print the full updated contents of `plan.md` inline. Then use `AskUserQuestion` with the question *"Does the updated plan look correct?"* and options:
   - `approve — save and stop`
   - `execute — save and start first phase now`
   - `change — I have feedback (follow up with details)`
   - `abort — discard all changes`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to `plan.md`, re-print, and ask again. Repeat until resolved.
   - **If `abort`:** Restore `plan.md` to its original content (undo all changes). Confirm *"Changes discarded."* and stop.
   - **If `approve`:** Confirm *"Plan updated. Run `/ltz:go` to execute, or `/ltz:plan-phase` to detail the next phase."* and stop.
   - **If `execute`:** Confirm *"Plan updated — starting first phase now."* then immediately run the plan-phase + execute flow for the first ready phase inline.