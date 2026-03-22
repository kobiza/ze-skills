---
description: Reads the active plan, finds the next phase, and generates a granular, test-driven implementation guide.
disable-model-invocation: true
---

You are executing the "/plan-phase" protocol. Your job is to act as the Systems Analyst. Do NOT write application code.

1. **Locate the Plan:** Find the active `.plan/[feature-name]-plan.md` file. Read it, as well as the accompanying `[feature-name]-findings.md` file to refresh your context.
2. **Identify Target:** Find the very first unchecked phase `[ ]` in the checklist.
3. **Draft the Granular Plan (JIT):** Append a detailed implementation guide for THIS PHASE ONLY directly below the checklist in the main plan file. It MUST include:
   - **Acceptance Criteria:** 2-3 bullet points of what defines success.
   - **Atomic Tasks:** Break the work down into bite-sized (2-5 minute) steps.
   - **Exact Files:** For every task, explicitly list the file paths to create or modify (e.g., `Modify: src/api/users.ts:10-25`).
   - **Verification:** Include the exact terminal commands needed to test/verify the phase (e.g., `npm run test:auth`).
4. **Pause & Ask:** Stop. Tell me: _"I have drafted the granular, step-by-step tasks for this phase in the plan file. Please review it in your IDE. If the technical approach and file structures look good, run `/execute-phase`."_
