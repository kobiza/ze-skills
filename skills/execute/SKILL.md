---
name: execute
description: Executes the next ready-for-dev phase of the active plan step-by-step.
disable-model-invocation: true
---

You are executing the "/execute" protocol. Your job is to act as the Engineer and write the code.

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name (e.g. `user-auth`).
   - If the file is missing or empty, use `AskUserQuestion` with the question *"No active plan is set. Would you like to choose one?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/choose-plan` and resume from step 1 after it completes. **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/choose-plan`, then resume from step 1.
2. **Find the Target Phase:** Read `.plan/[active-name]/plan.md` and look at the `## Progress` table. Find the first phase with status `ready-for-dev`.
   - If no `ready-for-dev` phase exists, use `AskUserQuestion` with the question *"No phases are ready to execute yet."* and options:
     - `Run /plan-phase to prepare the next phase`
     - `Cancel`
   - **If cancel:** Stop. **If plan-phase:** Invoke `/plan-phase`, then resume from step 2.
3. **Execute:** Implement the atomic tasks for the target phase step-by-step. Remember to follow the global "Ask User Question" rule to get approval before modifying files.
4. **The 3-Strike Error Protocol:** As you write code and run verification commands, if you encounter the exact same error 3 times:
   - **STOP executing immediately.** Do not blindly retry.
   - Log the error, what you tried, and your hypothesis into `.plan/[active-name]/findings.md`.
   - Use `AskUserQuestion` with the question *"I've hit a roadblock and logged it in findings.md. How would you like to proceed?"* and options:
     - `Tell me what you tried and I'll guide you`
     - `Skip this task and continue`
     - `Abort the plan`
5. **Verify & Update:** Once the code is written, run the verification commands defined in the plan. If they pass:
   - Change the phase's checkbox from `[ ]` to `[x]` in `plan.md` (visual marker).
   - Find the matching row in the `## Progress` table and update its status to `done`.
6. **Prompt Next Step:** Inform me the phase is complete. Use `AskUserQuestion` with the question *"Phase complete and verified. What would you like to do next?"* and options:
   - `Run /execute for the next phase`
   - `Run /plan-phase first to review the next phase's tasks`
   - `Review the changes`
   - `Done for now`
