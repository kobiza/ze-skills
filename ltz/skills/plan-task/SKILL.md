---
name: plan-task
description: Plans and executes a single small task end-to-end — clarification, atomic task list, review loop, then immediate execution.
disable-model-invocation: true
model: claude-opus-4-6
---

You are executing the "/ltz:plan-task" protocol. Your job is to act as both Analyst and Engineer for a small, self-contained task. This skill is for focused work that fits in a single phase — not multi-phase features (use `/ltz:plan-feature` for those).

1. **Clarity Check:** Evaluate whether the task request contains enough information to produce a concrete implementation plan. A request is sufficiently detailed if it has: a clear goal, implied scope boundaries, and at least a hint of success criteria.
   - **If underspecified:** Use the `AskUserQuestion` tool to ask 2-6 targeted questions one at a time covering: goal & success criteria, scope boundaries (what's out), tech/framework constraints, known risks or blockers. Wait for answers before proceeding.
   - **If already detailed:** Note "Request is clear — proceeding to plan." and continue.

2. **Analyze Context:** Briefly scan any relevant files (e.g. `README.md`, `package.json`) to understand the tech stack and constraints. Only read what's necessary.

3. **Generate Name:** Extract the core keywords from the task and convert to kebab-case (e.g. `fix-login-redirect`, `add-dark-mode-toggle`).

4. **Scaffold:** Create the following:
   - `.plan/[task-name]/` directory
   - `.plan/[task-name]/plan.md` — the task plan (drafted in step 5)
   - `.plan/[task-name]/findings.md` — scratchpad for errors and learnings (create with a heading and empty body)
   - Write the task name as a single line into `.plan/.active`

5. **Draft the Task Plan:** Write the implementation guide into `.plan/[task-name]/plan.md`. It MUST include:
   - **Goal:** 1-2 sentences.
   - **Acceptance Criteria:** 2-3 bullet points defining success.
   - **Atomic Tasks:** Bite-sized (2-5 minute) steps. For each task, explicitly list the file paths to create or modify.
   - **Verification:** Exact terminal commands to confirm the task is complete.
   - **Progress:** Append at the end: `## Progress\nStatus: \`backlog\``

6. **Review Loop:** Print the full contents of `plan.md` inline. Then use `AskUserQuestion` with the question *"Does this plan look right?"* and options:
   - `approve — save and stop`
   - `execute — save and run now`
   - `change — I have feedback (follow up with details)`
   - `abort — cancel`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply edits to `plan.md`, re-print, and ask again. Repeat until resolved.
   - **If `abort`:** Confirm *"Task plan cancelled."* and stop (do not delete files).
   - **If `approve`:** Update `## Progress` in `plan.md` to `Status: \`ready-for-dev\``. Confirm *"Plan saved. Run `/ltz:execute` when you're ready."* and stop.
   - **If `execute`:** Update `## Progress` in `plan.md` to `Status: \`ready-for-dev\``. Confirm *"Plan approved — executing now."* then immediately proceed to step 7.

7. **Execute:** Implement the atomic tasks step-by-step. Follow the global "Ask User Question" rule to get approval before modifying files.

8. **The 3-Strike Error Protocol:** If you encounter the exact same error 3 times:
   - **STOP immediately.** Do not retry.
   - Log the error, what you tried, and your hypothesis into `.plan/[task-name]/findings.md`.
   - Use `AskUserQuestion` with the question *"I've hit a roadblock and logged it in findings.md. How would you like to proceed?"* and options:
     - `Tell me what you tried and I'll guide you`
     - `Skip this task and continue`
     - `Abort the plan`

9. **Verify & Complete:** Run the verification commands from the plan. If they pass, update `## Progress` in `plan.md` to `Status: \`done\``. Then append `**Status: Done ✓**` at the very end of the file.

10. **Wrap Up:** Inform me the task is complete. Use `AskUserQuestion` with the question *"Task complete and verified. What would you like to do next?"* and options:
    - `Review the changes`
    - `Start another task`
    - `Done for now`
