---
name: plan-feature
description: Analyzes a feature request, establishes constraints, and creates a high-level, prioritized multi-phase roadmap.
disable-model-invocation: true
model: claude-opus-4-6
---

You are executing the "/ltz:plan-feature" protocol. Your job is to act as the Architect. Do NOT write application code. This skill is for multi-phase features — for small single tasks, use `/ltz:plan-task` instead.

1. **Clarity Check:** Before doing anything else, evaluate whether the feature request contains enough information to produce a meaningful roadmap. A request is sufficiently detailed if it has: a clear goal, implied scope boundaries, and at least a hint of constraints or success criteria.
   - **If underspecified:** Use the `AskUserQuestion` tool to ask 2-6 targeted questions one at a time. Cover as many as are relevant: goal & success criteria, who the end user is, scope boundaries (what's explicitly out of scope), tech/framework constraints, known risks or blockers, priority ordering. Wait for my answers before proceeding to Step 2.
   - **If already detailed:** Note "Request is clear — proceeding to roadmap." and continue to Step 2.

2. **Analyze Context:** Briefly scan the project's `README.md` or package files to identify the tech stack, testing framework, and architectural constraints.
3. **Generate Filename:** Extract the core keywords from my feature request and convert them to kebab-case (e.g., `user-auth`, `shopping-cart`).
4. **Scaffold:** Create a `.plan/[feature-name]/` directory (e.g. `.plan/user-auth/`). This folder will hold all files for this plan.
5. **Create Files:** Inside the folder, create two files:
   - `.plan/[feature-name]/plan.md` (The Main Roadmap)
   - `.plan/[feature-name]/findings.md` (The Scratchpad for errors and learnings)
6. **Set Active Plan:** Write the feature name (e.g. `user-auth`) as a single line into `.plan/.active`. This marks it as the current focused plan.
7. **Draft the Roadmap:** Inside `plan.md`, write the high-level architecture. Use the answers from the clarification interview (if one occurred) to inform the Goal statement, Scope bullets, and phase priorities. It MUST strictly include:
   - **Goal:** 1-2 sentences describing what this builds.
   - **Scope:** Bullet points for what is "In Scope" and what is explicitly "Out of Scope".
   - **Phases:** A markdown checklist (`[ ]`) of logical phases. For each phase, include its MoSCoW priority (Must/Should/Could Have) and any dependencies.
   - **Progress:** After the Phases checklist, append a `## Progress` table with one row per phase, all starting as `backlog`:
     ```markdown
     ## Progress
     | Phase | Status |
     |-------|--------|
     | Phase 1 — [name] | `backlog` |
     | Phase 2 — [name] | `backlog` |
     ```
8. **Constraint:** Keep the phases high-level. Do NOT write the granular technical implementation details yet. When deciding how many phases to create:
   - Phases are user-verifiable checkpoints, not structural formality — don't add them just to have structure.
   - A single-phase plan is valid and often preferable for small or cohesive requests.
   - Only split into multiple phases when the user could meaningfully demo, test, or review the output of each phase before the next begins. If the work would feel incomplete or unverifiable mid-way, keep it as one phase.
9. **Review Loop:** Print the full contents of `plan.md` inline in the terminal. Then use `AskUserQuestion` with the question *"Does this roadmap look correct?"* and options:
   - `approve — save and stop`
   - `execute — save and start first phase now`
   - `change — I have feedback (follow up with details)`
   - `abort — cancel`

   - **If `change`:** Ask for their feedback using `AskUserQuestion`, apply the edits to `plan.md`, re-print the updated roadmap, and ask again. Repeat until resolved.
   - **If `approve`:** Confirm *"Roadmap approved. Run `/ltz:plan-phase` to detail the first phase, or `/ltz:execute` to start immediately."* and stop.
   - **If `execute`:** Confirm *"Roadmap approved — starting first phase now."* then immediately run the plan-phase + execute flow for the first phase inline.
   - **If `abort`:** Confirm *"Plan creation cancelled."* and stop (do not delete any files).
