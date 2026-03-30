---
user-invocable: false
description: Reads the active plan, finds the next backlog phase, generates a granular implementation guide, and loops for review before handing off to execute.
---

You are executing the "/ltz:plan-phase" protocol. Your job is to act as the Systems Analyst. Do NOT write application code.

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name (e.g. `user-auth`).
   - If the file is missing or empty, use `AskUserQuestion` with the question *"No active plan is set. Would you like to choose one?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/ltz:choose-plan` and resume from step 1 after it completes. **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/ltz:choose-plan`, then resume from step 1.
2. **Locate the Plan:** Read `.plan/[active-name]/plan.md` and `.plan/[active-name]/findings.md` to refresh your context.
3. **Identify Target:** Look at the `## Progress` table and find the first phase with status `backlog`.
   - If no `backlog` phase exists, tell me: *"All phases are already planned. Run `/ltz:execute` to start the next ready phase."* and stop.
4. **Draft the Granular Plan (JIT):** Append a detailed implementation guide for THIS PHASE ONLY directly below the checklist in the plan file. It MUST include:
   - **Acceptance Criteria:** 2-3 bullet points of what defines success.
   - **Atomic Tasks:** Break the work down into bite-sized (2-5 minute) steps.
   - **Exact Files:** For every task, explicitly list the file paths to create or modify (e.g., `Modify: src/api/users.ts:10-25`).
   - **Verification:** Include the exact terminal commands needed to test/verify the phase (e.g., `npm run test:auth`).
5. **Review Loop:** Print an inline summary of what was drafted: the phase name, acceptance criteria, number of atomic tasks, and the verification command(s). Then use `AskUserQuestion` with the question *"Does this phase plan look right?"* and options:
   - `approve — save and stop`
   - `execute — save and run now`
   - `change — I have feedback (follow up with details)`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to the appended section in `plan.md`, re-print the summary, and ask again. Repeat until resolved.
   - **If `approve`:** In `plan.md`, find the `## Progress` table and update the current phase's status from `backlog` to `ready-for-dev`. Then confirm *"Tasks approved. Run `/ltz:execute` to implement this phase."* and stop.
   - **If `execute`:** In `plan.md`, update the current phase's `## Progress` status to `ready-for-dev`. Then confirm *"Tasks approved — executing now."* and immediately proceed to implement the phase inline (follow the execute protocol: implement tasks, run verification, mark phase `[x]`, update Progress to `done`).
