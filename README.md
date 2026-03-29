# ze-skills

A Claude Code plugin by [Kobi Zakut](https://github.com/kobiza) — structured planning and execution workflow with human-in-the-loop review gates.

## Installation

```bash
# Install the ltz plugin
claude --plugin-dir ./ltz

# Or via npx skills
npx skills add kobiza/ze-skills
```

## Plugin: ltz

### Primary skills

| Skill | Description |
|-------|-------------|
| `/ltz:plan [request]` | Smart router — auto-detects task vs feature and runs the matching planning flow. |
| `/ltz:go` | Continues the active plan to completion — loops plan-phase and execute until done. |
| `/ltz:update [changes]` | Re-analyzes the active plan with new context or changed requirements. |
| `/ltz:list` | Lists all plans with progress indicators and lets you switch the active plan. |
| `/ltz:cleanup` | Deletes a plan folder and clears the active pointer if needed. |

### Fine-grained control

These are the building blocks that primary skills orchestrate. Use them directly when you want explicit control over individual steps.

| Skill | Role | Description |
|-------|------|-------------|
| `/ltz:plan-task` | Analyst | Plans a single small task — clarification, atomic task list, then a review loop. |
| `/ltz:plan-feature` | Architect | Analyzes a feature request and creates a high-level, prioritized multi-phase roadmap, then a review loop. |
| `/ltz:plan-phase` | Systems Analyst | Reads the active plan, finds the next unchecked phase, and drafts a granular atomic task list with verification commands, then a review loop. |
| `/ltz:execute` | Engineer | Executes the current plan or phase step-by-step. If the phase hasn't been planned yet, auto-plans it inline (auto-approved) before executing. |
| `/ltz:choose-plan` | — | Internal helper — switch active plan (used by other skills). |

## Workflow

Every planning skill ends with a **review loop**. You respond with one of:

| Response | Effect |
|----------|--------|
| `change: [feedback]` | Revise the plan and loop again |
| `approve` | Save the plan and stop — nothing runs automatically |
| `execute` | Accept the plan and immediately start executing |

### Plan — `/ltz:plan`

```
/ltz:plan [request] → auto-classifies → plan → review loop
```

Auto-detects whether the request is a small task or multi-phase feature. Creates the plan, then stops for your review.

### Go — `/ltz:go`

```
/ltz:go → reads active plan → plan-phase → review → execute → next phase → done
```

Drives an existing plan to completion. Loops plan-phase and execute for each remaining phase with review gates.

### Fine-grained control

```
/ltz:plan-feature → review loop
                        ↓
                  /ltz:plan-phase → review loop → execute
                        ↑                           ↓
                   (next phase)               (phase done)
```

Two paths after `/ltz:plan-feature`:

- **Explicit**: run `/ltz:plan-phase` to review granular tasks before each phase, then `execute`.
- **Express**: run `/ltz:execute` directly — it detects the phase isn't planned yet, asks for confirmation, then plans and executes immediately.

### Utilities

`/ltz:list` and `/ltz:cleanup` are available at any point in the flow.

## Plan folder structure

Plan state is stored in `.plan/` inside the **target project** (not this repo):

```
.plan/
├── .active                  ← name of the currently focused plan
├── user-auth/
│   ├── plan.md              ← roadmap with phase checklist; granular tasks appended JIT
│   └── findings.md          ← error log and learnings (3-strike protocol)
└── shopping-cart/
    ├── plan.md
    └── findings.md
```

## Error handling

All execution skills follow a **3-strike protocol**: if the same error is hit 3 times, the skill stops, logs the error and hypothesis to `findings.md`, and asks for guidance rather than retrying blindly.
