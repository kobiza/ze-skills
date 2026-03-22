# ze-skills

Agent skills by [Kobi Zakut](https://github.com/kobiza), compatible with the [`npx skills`](https://github.com/vercel-labs/skills) CLI.

## Skills

### `start-plan`
Analyzes a feature request, establishes constraints, and creates a high-level, prioritized roadmap. Scaffolds a per-plan folder under `.plan/[feature-name]/` and sets it as the active plan.

### `plan-phase`
Reads the active plan, finds the next unchecked phase, and generates a granular, test-driven implementation guide.

### `execute-phase`
Executes the granular tasks of the current active phase, runs verification, and updates the plan checklist.

### `list-plans`
Lists all plans in `.plan/` with progress indicators, and lets you focus on (activate) any one of them.

### `cleanup-plan`
Deletes a plan folder and clears the active pointer if the deleted plan was focused.

---

## Workflow

```
/start-plan → /plan-phase → /execute-phase
                  ↑               ↓
              (next phase)   (phase done)
```

Use `/list-plans` at any time to switch between plans.
Use `/cleanup-plan` to remove a completed or abandoned plan.

### Plan folder structure

```
.plan/
├── .active              ← name of the currently focused plan
├── user-auth/
│   ├── plan.md
│   └── findings.md
└── shopping-cart/
    ├── plan.md
    └── findings.md
```

---

## Installation

```bash
# Install all skills
npx skills add kobiza/ze-skills

# Install a specific skill
npx skills add kobiza/ze-skills --skill start-plan
npx skills add kobiza/ze-skills --skill plan-phase
npx skills add kobiza/ze-skills --skill execute-phase
npx skills add kobiza/ze-skills --skill list-plans
npx skills add kobiza/ze-skills --skill cleanup-plan
```
