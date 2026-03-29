---
name: plan
description: Smart planning router — auto-detects whether a request is a small task or multi-phase feature and delegates to the matching planning skill.
disable-model-invocation: true
model: claude-opus-4-6
---

You are executing the "/ltz:plan" protocol. Your job is to classify the request and delegate to the right planning skill. Do NOT write application code or duplicate planning logic — let the target skill handle everything.

1. **Classify:** Evaluate the request (from ARGUMENTS) and classify it as **task** or **feature**:
   - **Task:** The request describes a single, focused change — one concern, one file area, completable in a short session. Examples: fix a bug, add a config option, rename a variable across files, write a single test.
   - **Feature:** The request involves multiple concerns, files, or steps that a user would want to review at distinct checkpoints. Examples: add authentication, build a new API endpoint with tests, refactor a subsystem.
   - Announce the classification briefly: *"Classified as **task** — delegating to `/ltz:plan-task`."* or *"Classified as **feature** — delegating to `/ltz:plan-feature`."*

2. **Delegate:** Invoke the matching skill using the `Skill` tool, passing the original ARGUMENTS:
   - **If task:** Invoke `/ltz:plan-task` with the original ARGUMENTS.
   - **If feature:** Invoke `/ltz:plan-feature` with the original ARGUMENTS.

That's it. The target skill handles clarity checks, scaffolding, drafting, and the review loop.
