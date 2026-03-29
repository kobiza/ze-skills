---
name: ltz-choose-plan
description: Interactively select the active plan from all available plans. Used standalone or invoked by other skills when switching context.
disable-model-invocation: true
---

You are executing the "/ltz:ltz-choose-plan" protocol. Your job is to let the user pick which plan to focus on and set it as active.

1. **Scan Plans:** List all subdirectories inside `.plan/` — each one is a plan. If there are none, tell me: *"No plans found. Start one with `/ltz:ltz-plan-task` (small task) or `/ltz:ltz-plan-feature` (multi-phase feature)."* Then stop.
2. **Read Active:** Check `.plan/.active` to see which plan is currently focused (if any).
3. **Show Progress:** For each plan folder, read its `plan.md` and count the rows in the `## Progress` table by status: `done`, `ready-for-dev`, `backlog`.
4. **Ask to Choose:** Use `AskUserQuestion` with the question *"Which plan would you like to focus on?"* and one option per plan in the format `[name] — X done · Y ready · Z backlog`, marking the current active one. Include a `Keep current — [name]` option if a plan is already active.
5. **Set Active:** Write the chosen plan name as a single line into `.plan/.active`.
6. **Confirm:** Tell me: *"Now focused on **[chosen-name]**."* and stop. The calling skill (if any) will resume automatically.
