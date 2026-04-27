---
user-invocable: false
description: Reads the active plan, finds the next backlog phase, generates a granular implementation guide, and loops for review before handing off to execute.
---

You are executing the "/ltz:plan-phase" protocol. Your job is to act as the Systems Analyst. Do NOT write application code.

**Write ownership:** This skill writes ONLY to `.plan/<feature>/phases/phase-N.md` (creates) and `.plan/<feature>/progress.md` (updates). It MUST NOT modify `.plan/<feature>/plan.md` — `plan.md` is owned exclusively by the planning skills (`plan`, `plan-feature`, `plan-task`, `update`).

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name (e.g. `user-auth`).
   - If the file is missing or empty, use `AskUserQuestion` with the question *"No active plan is set. Would you like to choose one?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/ltz:choose-plan` and resume from step 1 after it completes. **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/ltz:choose-plan`, then resume from step 1.
2. **Locate the Plan:** Read `.plan/[active-name]/plan.md`, `.plan/[active-name]/progress.md`, and `.plan/[active-name]/findings.md` to refresh your context.
3. **Identify Target:** Scan the status table in `progress.md` and find the first row with status `backlog`. Capture its 1-based row index as `N` — this is the phase number.
   - If no `backlog` row exists, tell me: *"All phases are already planned. Run `/ltz:go` to start the next ready phase."* and stop.
4. **Draft the Granular Plan (JIT):** Create a NEW file `.plan/[active-name]/phases/phase-N.md` containing the detailed implementation guide for THIS PHASE ONLY. Do NOT touch `plan.md`. The file MUST include:
   - **Acceptance Criteria:** 2-3 bullet points of what defines success.
   - **Atomic Tasks:** Break the work down into bite-sized (2-5 minute) steps.
   - **Exact Files:** For every task, explicitly list the file paths to create or modify (e.g., `Modify: src/api/users.ts:10-25`).
   - **Verification:** Include the exact terminal commands needed to test/verify the phase (e.g., `npm run test:auth`).
5. **Review Loop:** Print an inline summary of what was drafted: the phase name, acceptance criteria, number of atomic tasks, and the verification command(s). Then use `AskUserQuestion` with the question *"Does this phase plan look right?"* and options:
   - `approve — save and stop`
   - `execute — save and run now`
   - `change — I have feedback (follow up with details)`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to `phases/phase-N.md` (NOT `plan.md`), re-print the summary, and ask again. Repeat until resolved.
   - **If `approve`:** In `progress.md`, update the row for phase N from `backlog` to `ready-for-dev`. Do NOT modify `plan.md`. Then confirm *"Tasks approved. Run `/ltz:go` to implement this phase."* and stop.
   - **If `execute`:** In `progress.md`, update the row for phase N from `backlog` to `ready-for-dev`. Do NOT modify `plan.md`. Then confirm *"Tasks approved — executing now."* and immediately proceed to implement the phase inline (follow the execute protocol: implement tasks, run verification, then in `progress.md` mark the row `done` with `[x]`).
