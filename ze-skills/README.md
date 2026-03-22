# ze-skills

Agent skills by [Kobi Zakut](https://github.com/kobiza), compatible with the [`npx skills`](https://github.com/vercel-labs/skills) CLI.

## Skills

### `start-plan`
Analyzes a feature request, establishes constraints, and creates a high-level, prioritized roadmap. Acts as the **Architect** — does not write application code.

### `plan-phase`
Reads the active plan, finds the next phase, and generates a granular, test-driven implementation guide. Acts as the **Systems Analyst** — does not write application code.

### `execute-phase`
Executes the granular tasks of the current phase, runs verification, and updates the plan. Acts as the **Engineer** — writes the code.

---

## Three-Phase Workflow

These skills work together as a pipeline:

```
/start-plan → /plan-phase → /execute-phase
```

1. `/start-plan` — Create a high-level roadmap for a feature
2. `/plan-phase` — Break the next phase into atomic, testable tasks
3. `/execute-phase` — Implement, verify, and check off the phase

---

## Installation

```bash
# Install all skills
npx skills add kobiza/ze-skills

# Install a specific skill
npx skills add kobiza/ze-skills --skill start-plan
npx skills add kobiza/ze-skills --skill plan-phase
npx skills add kobiza/ze-skills --skill execute-phase
```
