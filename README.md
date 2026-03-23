# ze-skills

Agent skills by [Kobi Zakut](https://github.com/kobiza), compatible with the [`npx skills`](https://github.com/vercel-labs/skills) CLI.

## Installation

```bash
# Install all skills
npx skills add kobiza/ze-skills

# Install a specific skill
npx skills add kobiza/ze-skills --skill <skill-name>
```

## Skills

### Planning & execution

| Skill | Role | Description |
|-------|------|-------------|
| `/plan-task` | Analyst | Plans a single small task — clarification, atomic task list, then a review loop. |
| `/plan-feature` | Architect | Analyzes a feature request and creates a high-level, prioritized multi-phase roadmap, then a review loop. |
| `/plan-phase` | Systems Analyst | Reads the active plan, finds the next unchecked phase, and drafts a granular atomic task list with verification commands, then a review loop. |
| `/execute` | Engineer | Executes the current plan or phase step-by-step. If the phase hasn't been planned yet, auto-plans it inline (auto-approved) before executing. |

### Utilities

| Skill | Description |
|-------|-------------|
| `/list-plans` | Lists all plans with progress indicators and lets you switch the active plan. |
| `/cleanup-plan` | Deletes a plan folder and clears the active pointer if needed. |

## Workflow

Every planning skill ends with a **review loop**. You respond with one of:

| Response | Effect |
|----------|--------|
| `change: [feedback]` | Revise the plan and loop again |
| `approve` | Save the plan and stop — nothing runs automatically |
| `execute` | Accept the plan and immediately start executing |

### Small task

```
/plan-task → review loop → execute
```

Use `/plan-task` for focused, single-phase work. After the review loop, type `execute` to run it.

### Large feature

```
/plan-feature → review loop
                    ↓
              /plan-phase → review loop → execute
                    ↑                       ↓
               (next phase)           (phase done)
```

Two paths after `/plan-feature`:

- **Explicit**: run `/plan-phase` to review granular tasks before each phase, then `execute`.
- **Express**: run `/execute` directly — it auto-plans the phase inline (auto-approved) and executes immediately.

### Utilities

`/list-plans` and `/cleanup-plan` are available at any point in the flow.

[View interactive diagram on Excalidraw →](https://excalidraw.com/#json=m9_ye9_fsTQRB58UTOzfW,58at-6s7XUQrgz-wys0IjQ)

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
