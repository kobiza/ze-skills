---
user-invocable: false
description: Executes all ready-for-dev phases of the active plan sequentially.
---

You are executing the "/ltz:execute" protocol. Your job is to act as the Engineer and write the code.

1. **Resolve Active Plan:** Read `.plan/.active` to get the current plan name (e.g. `user-auth`).
   - If the file is missing or empty, use `AskUserQuestion` with the question *"No active plan is set. Would you like to choose one?"* and options:
     - `Yes, run /choose-plan`
     - `No, cancel`
   - **If yes:** Invoke `/ltz:choose-plan` and resume from step 1 after it completes. **If no:** Stop.
   - If a plan name is found, use `AskUserQuestion` with the question *"Active plan: **[name]**. Continue with this plan?"* and options:
     - `Yes, continue`
     - `No, choose a different plan`
   - **If "No":** Invoke `/ltz:choose-plan`, then resume from step 1.
2. **Collect Ready Phases:** Read `.plan/[active-name]/plan.md` and collect the full ordered list of phases with status `ready-for-dev` from the `## Progress` table.
   - If none exist, use `AskUserQuestion` with the question *"No phases are ready to execute yet."* and options:
     - `Run /plan-phase to prepare the next phase`
     - `Cancel`
   - **If cancel:** Stop. **If plan-phase:** Invoke `/ltz:plan-phase`, then resume from step 2.
   - If one or more found, announce them: *"Found N phase(s) ready to execute: Phase A, Phase B, … Running them in order."*
3. **Loop — Execute Each Phase:** For each phase in the collected list (in order):
   a. **Execute:** Implement the atomic tasks for this phase step-by-step. Follow the global "Ask User Question" rule to get approval before modifying files.
   b. **3-Strike Error Protocol:** If you encounter the exact same error 3 times:
      - **STOP immediately.** Log the error, what you tried, and your hypothesis into `.plan/[active-name]/findings.md`.
      - Use `AskUserQuestion` with the question *"I've hit a roadblock and logged it in findings.md. How would you like to proceed?"* and options:
        - `Tell me what you tried and I'll guide you`
        - `Skip this task and continue`
        - `Abort the plan`
      - **If "Abort the plan":** Stop the loop entirely. **If "Skip":** Continue to the next task in this phase. **If guided:** Apply the fix and continue.
   c. **Verify & Update:** Run the verification commands for this phase. If they pass:
      - Change the phase's checkbox from `[ ]` to `[x]` in `plan.md` (visual marker).
      - Update the matching row in the `## Progress` table to `done`.
      - Announce: *"Phase [name] complete ✓ — continuing to next phase…"* (or skip this line if it's the last phase)
4. **Wrap Up:** After all phases are complete, use `AskUserQuestion` with the question *"All ready-for-dev phases complete. What would you like to do next?"* and options:
   - `Run /plan-phase to plan the next phase`
   - `Review the changes`
   - `Done for now`
