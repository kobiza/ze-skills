---
name: execute
description: Executes the current plan or phase step-by-step. If the phase hasn't been planned yet, asks for confirmation then plans and executes immediately.
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
2. **Read the Instructions:** Read `.plan/[active-name]/plan.md` and find the first unchecked `[ ]` phase.
3. **Guard — Check for a Planned Phase:** Look for an `**Atomic Tasks**` section associated with that phase in `plan.md`. If no such section exists, the phase has not been planned yet. Use the `AskUserQuestion` tool with the question *"This phase hasn't been planned yet. Would you like me to plan and execute immediately?"* and options:
   - `Yes, plan and execute now`
   - `No, I'll run /plan-phase first`

   - **If yes:** Run the plan-phase logic inline — draft granular atomic tasks, acceptance criteria, and verification commands for this phase; append them to `plan.md`; print a summary and ask: *"Does this plan look right? Reply with: **approve** to execute, or **change: [feedback]** to revise."* Repeat until approved, then continue to step 4.
   - **If no:** Confirm *"Run `/plan-phase` first to detail this phase."* and stop.
4. **Execute:** Implement the tasks step-by-step. Remember to follow the global "Ask User Question" rule to get approval before modifying files.
5. **The 3-Strike Error Protocol:** As you write code and run verification commands, if you encounter the exact same error 3 times:
   - **STOP executing immediately.** Do not blindly retry.
   - Log the error, what you tried, and your hypothesis into `.plan/[active-name]/findings.md`.
   - Use `AskUserQuestion` with the question *"I've hit a roadblock and logged it in findings.md. How would you like to proceed?"* and options:
     - `Tell me what you tried and I'll guide you`
     - `Skip this task and continue`
     - `Abort the plan`
6. **Verify & Update:** Once the code is written, run the verification commands defined in the plan. If they pass:
   - Change the phase's checkbox from `[ ]` to `[x]` in `plan.md`.
   - Find the matching row in the `## Progress` table and update its status to `done`.
7. **Prompt Next Step:** Inform me the phase is complete. Use `AskUserQuestion` with the question *"Phase complete and verified. What would you like to do next?"* and options:
   - `Run /execute for the next phase`
   - `Run /plan-phase first to review the next phase's tasks`
   - `Review the changes`
   - `Done for now`
