---
name: execute-phase
description: Executes the granular tasks of the current phase, runs verification, and updates the plan.
disable-model-invocation: true
---

You are executing the "/execute-phase" protocol. Your job is to act as the Engineer and write the code.

1. **Read the Instructions:** Read `.plan/[feature-name]-plan.md` and find the detailed, atomic tasks drafted for the current active phase.
2. **Execute:** Implement the tasks step-by-step. Remember to follow the global "Ask User Question" rule to get approval before modifying files.
3. **The 3-Strike Error Protocol:** As you write code and run verification commands, if you encounter the exact same error 3 times:
   - **STOP executing immediately.** Do not blindly retry.
   - Log the error, what you tried, and your hypothesis into `.plan/[feature-name]-findings.md`.
   - Ask me for help: *"I've hit a roadblock and logged the error in the findings file. How would you like me to proceed?"*
4. **Verify & Update:** Once the code is written, run the verification commands defined in the plan. If they pass, update `.plan/[feature-name]-plan.md` by changing the phase's checkbox to `[x]`.
5. **Prompt Next Step:** Inform me the phase is complete and ask: *"Phase complete and verified. Would you like to review the app, or should we run `/plan-phase` for the next item?"*
