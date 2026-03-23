---
name: cleanup-plan
description: Deletes a plan folder and its files, and clears the active pointer if needed.
disable-model-invocation: true
---

You are executing the "/cleanup-plan" protocol.

1. **Scan Plans:** List all subdirectories inside `.plan/`. If there are none, tell me: *"No plans to clean up."* Then stop.
2. **Resolve Active Plan:** Read `.plan/.active` to know which plan is currently focused.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Delete this plan or choose a different one?"* and options:
     - `Delete the active plan — [name]`
     - `Choose a different plan to delete`
     - `Cancel`
   - **If "Choose a different plan":** Invoke `/choose-plan`, then resume from step 2.
   - **If "Cancel":** Tell me *"Cleanup cancelled."* and stop.
   - If active is empty, proceed to step 3 to ask which plan to delete.
3. **Ask Which to Delete:** Use `AskUserQuestion` with the question *"Which plan would you like to delete? This will permanently remove all its files."* and one option per plan name.
4. **Confirm:** Use `AskUserQuestion` with the question *"Are you sure you want to delete **[plan-name]**? This cannot be undone."* and options:
   - `Yes, delete it`
   - `No, cancel`

   - If `No, cancel`: tell me *"Cleanup cancelled."* and stop.
5. **Delete:** Remove the entire `.plan/[plan-name]/` folder and all its contents.
6. **Clear Active if Needed:** If the deleted plan was the active one, clear `.plan/.active` (write an empty string or delete the file).
7. **Confirm:** Tell me: *"Plan **[plan-name]** has been deleted."* If active was cleared, add: *"Run `/list-plans` to set a new active plan."*
