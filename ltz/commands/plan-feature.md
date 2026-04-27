---
user-invocable: false
description: Analyzes a feature request, establishes constraints, and creates a high-level, prioritized multi-phase roadmap.
---

You are executing the "/ltz:plan-feature" protocol. Your job is to act as the Architect. Do NOT write application code. This skill is for multi-phase features — for small single tasks, use `/ltz:plan-task` instead.

1. **Clarity Check:** Before doing anything else, evaluate whether the feature request contains enough information to produce a meaningful roadmap. A request is sufficiently detailed if it has: a clear goal, implied scope boundaries, and at least a hint of constraints or success criteria.
   - **If underspecified:** Use the `AskUserQuestion` tool to ask 2-6 targeted questions one at a time. Cover as many as are relevant: goal & success criteria, who the end user is, scope boundaries (what's explicitly out of scope), tech/framework constraints, known risks or blockers, priority ordering. Wait for my answers before proceeding to Step 2.
   - **If already detailed:** Note "Request is clear — proceeding to roadmap." and continue to Step 2.

2. **Analyze Context:** Briefly scan the project's `README.md` or package files to identify the tech stack, testing framework, and architectural constraints.
3. **Generate Filename:** Extract the core keywords from my feature request and convert them to kebab-case (e.g., `user-auth`, `shopping-cart`).
4. **Scaffold:** Create a `.plan/[feature-name]/` directory (e.g. `.plan/user-auth/`) and an empty `phases/` subdirectory inside it. This folder will hold all files for this plan.
5. **Create Files:** Inside the folder, create:
   - `.plan/[feature-name]/plan.md` — strategic roadmap (drafted in step 7). Only planning skills (`plan-feature`, `plan-task`, `update`) ever modify this file again.
   - `.plan/[feature-name]/progress.md` — runtime state (drafted in step 7). Updated by `plan-phase` and `execute` as work advances.
   - `.plan/[feature-name]/findings.md` — scratchpad for errors and learnings.
   - `.plan/[feature-name]/phases/` — empty directory; `plan-phase` will populate it with `phase-N.md` files JIT.
6. **Set Active Plan:** Write the feature name (e.g. `user-auth`) as a single line into `.plan/.active`. This marks it as the current focused plan.
7. **Draft the Roadmap:** Inside `plan.md`, write the high-level architecture. Use the answers from the clarification interview (if one occurred) to inform the Goal statement, Scope bullets, and phase priorities. `plan.md` MUST strictly include — and ONLY include:
   - **Goal:** 1-2 sentences describing what this builds.
   - **Scope:** Bullet points for what is "In Scope" and what is explicitly "Out of Scope".
   - **Phases:** A plain bulleted list (no `[ ]` checkboxes — those are tracked in `progress.md`). For each phase, include its MoSCoW priority (Must/Should/Could Have) and any dependencies.

   Then, in the SEPARATE file `progress.md`, write a status table with one row per phase, all starting as `backlog` and `[ ]`:
   ```markdown
   # Progress — [Feature Name]

   | Phase | Status | Done |
   |-------|--------|------|
   | Phase 1 — [name] | `backlog` | [ ] |
   | Phase 2 — [name] | `backlog` | [ ] |
   ```
   `plan.md` must NOT contain a `## Progress` section. State lives in `progress.md`.
8. **Constraint:** Keep the phases high-level. Do NOT write the granular technical implementation details yet. When deciding how many phases to create:
   - Phases are user-verifiable checkpoints, not structural formality — don't add them just to have structure.
   - A single-phase plan is valid and often preferable for small or cohesive requests.
   - Only split into multiple phases when the user could meaningfully demo, test, or review the output of each phase before the next begins. If the work would feel incomplete or unverifiable mid-way, keep it as one phase.
9. **Review Loop:** Print the full contents of `plan.md` inline in the terminal. Note briefly that `progress.md` was also created with all phases as `backlog`. Then use `AskUserQuestion` with the question *"Does this roadmap look correct?"* and options:
   - `approve — save and stop`
   - `execute — save and start first phase now`
   - `change — I have feedback (follow up with details)`
   - `abort — cancel`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply the edits to `plan.md`, re-print the updated roadmap, and ask again. Repeat until resolved.
   - **If `approve`:** Confirm *"Roadmap approved. Run `/ltz:plan-phase` to detail the first phase, or `/ltz:execute` to start immediately."* and stop.
   - **If `execute`:** Confirm *"Roadmap approved — starting first phase now."* then immediately run the plan-phase + execute flow for the first phase inline.
   - **If `abort`:** Confirm *"Plan creation cancelled."* and stop (do not delete any files).
