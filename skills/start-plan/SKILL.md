---
name: start-plan
description: Analyzes a feature request, establishes constraints, and creates a high-level, prioritized roadmap.
disable-model-invocation: true
---

You are executing the "/start-plan" protocol. Your job is to act as the Architect. Do NOT write application code.

1. **Analyze Context:** Briefly scan the project's `README.md` or package files to identify the tech stack, testing framework, and architectural constraints.
2. **Generate Filename:** Extract the core keywords from my feature request and convert them to kebab-case (e.g., `user-auth`, `shopping-cart`).
3. **Scaffold:** Create a `.plan/[feature-name]/` directory (e.g. `.plan/user-auth/`). This folder will hold all files for this plan.
4. **Create Files:** Inside the folder, create two files:
   - `.plan/[feature-name]/plan.md` (The Main Roadmap)
   - `.plan/[feature-name]/findings.md` (The Scratchpad for errors and learnings)
5. **Set Active Plan:** Write the feature name (e.g. `user-auth`) as a single line into `.plan/.active`. This marks it as the current focused plan.
6. **Draft the Roadmap:** Inside `plan.md`, write the high-level architecture. It MUST strictly include:
   - **Goal:** 1-2 sentences describing what this builds.
   - **Scope:** Bullet points for what is "In Scope" and what is explicitly "Out of Scope".
   - **Phases:** A markdown checklist (`[ ]`) of logical phases. For each phase, include its MoSCoW priority (Must/Should/Could Have) and any dependencies.
7. **Constraint:** Keep the phases high-level. Do NOT write the granular technical implementation details yet.
8. **Pause & Ask:** Stop everything. Inform me in the terminal that the roadmap is ready. Ask: *"Please review `.plan/[feature-name]/plan.md`. If the scope and high-level phases look correct, run `/plan-phase` to detail the first phase."*
