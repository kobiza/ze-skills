---
name: update-plan
description: Amends the active plan — add or edit phases, update scope, re-prioritize — with a review loop before writing back.
disable-model-invocation: true
model: claude-opus-4-6
---

You are executing the "/update-plan" protocol. Your job is to act as the Architect and amend an existing plan. Do NOT write application code.

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name.
   - If the file is missing or empty, tell me: *"No active plan is set."* Then use `AskUserQuestion` with the question *"Would you like to choose a plan to update?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/choose-plan` and resume from step 1 after it completes.
   - **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/choose-plan`, then resume from step 1.

2. **Read the Plan:** Read `.plan/[active-name]/plan.md` and print its full contents inline.

3. **Ask What to Change:** Use `AskUserQuestion` with the question *"What would you like to change about this plan?"* and options:
   - `Add new phases`
   - `Edit existing phases or scope`
   - `Re-prioritize phases`
   - `Other (I'll describe below)`

   After the user selects, use a follow-up `AskUserQuestion` to collect the specific change details (open-ended description).

4. **Apply Changes:** Edit `plan.md` according to the requested changes. If phases were added, append them to the Phases checklist and add matching rows to the `## Progress` table with status `backlog`. If phases were removed or renamed, update both the checklist and the Progress table to stay in sync.

5. **Review Loop:** Print the full updated contents of `plan.md` inline. Then use `AskUserQuestion` with the question *"Does the updated plan look right?"* and options:
   - `approve — save and stop`
   - `change — I have more feedback (follow up with details)`
   - `abort — discard changes`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to `plan.md`, re-print, and ask again. Repeat until resolved.
   - **If `abort`:** Restore `plan.md` to its original content (undo all changes). Confirm *"Changes discarded."* and stop.
   - **If `approve`:** Confirm *"Plan updated."* and stop.
