# Skill Architecture Evolution

## Goal

Introduce a cohesive `ltz-*` skill namespace ("ltz" = short for "lets") as the primary user-facing API for the planning workflow. `ltz-plan` auto-routes between feature and task planning, `ltz-go` orchestrates the full workflow end-to-end. Existing skills remain unchanged as fine-grained tools.

## Scope

**In Scope:**
- New `/ltz-plan` skill — smart router that auto-selects plan-feature vs plan-task flow based on request complexity
- New `/ltz-go` skill — agent-mode orchestrator that plans + phases + executes in one command; also resumes the active plan if invoked with no arguments
- New `/ltz-update` skill — thin wrapper over update-plan, consistent with the ltz namespace
- New `/ltz-list` skill — thin wrapper over list-plans, consistent with the ltz namespace
- New `/ltz-cleanup` skill — thin wrapper over cleanup-plan, consistent with the ltz namespace
- README update: ltz-* skills featured as primary API; existing skills moved to "Advanced / fine-grained control" section

**Out of Scope:**
- Modifying or removing any existing skill
- Changing the `.plan/` folder structure or plan.md format

## The ltz-* API

| Skill | When to use |
|-------|-------------|
| `/ltz-plan [request]` | Start something new — auto-routes to feature or task |
| `/ltz-go [request]` | Start something new AND run it end-to-end |
| `/ltz-go` (no args) | Resume & complete the active plan from current state |
| `/ltz-update [changes]` | Revise an existing plan's scope/phases/tasks |
| `/ltz-list` | Browse all plans, switch active |
| `/ltz-cleanup` | Delete a plan |

## Flow Mapping

| Old flow | New flow |
|----------|----------|
| `plan-task` → execute | `ltz-plan` → execute OR `ltz-go` |
| `plan-feature` → execute | `ltz-plan` → execute OR `ltz-go` |
| `plan-feature` → `plan-phase` → execute | `ltz-plan` → `plan-phase` → execute OR `ltz-go` |
| `list-plans` → `execute` (resume) | `ltz-list` → execute OR `ltz-go` (no args) |
| `update-plan` → `execute` | `ltz-update` → execute OR `ltz-update` → `ltz-go` |

## Phases

- [x] **Phase 1 — Add `/ltz-plan` smart router** *(Must Have)*
  A skill that evaluates request complexity and internally runs the plan-feature or plan-task flow. User never has to choose between them. Depends on: nothing.

- [x] **Phase 2 — Add `/ltz-go` agent-mode** *(Must Have)*
  A skill that orchestrates the full workflow: auto-plans → auto-phases → executes, with confirmation gates at meaningful decision points (plan review, phase review before execution). Also handles the "resume active plan" case when invoked with no arguments. Depends on: Phase 1.

- [x] **Phase 3 — Add `/ltz-update`, `/ltz-list`, `/ltz-cleanup` wrappers** *(Should Have)*
  Thin wrapper skills that keep the namespace consistent. Each simply delegates to the existing underlying skill. Depends on: Phase 1.

- [x] **Phase 4 — Update README** *(Should Have)*
  Feature ltz-* skills as the primary API. Move existing skills to "Advanced / fine-grained control" section. Update flow diagrams. Depends on: Phases 1-3.

---

## Phase 1 — Add `/ltz-plan` smart router

### Acceptance Criteria
- `/ltz-plan [request]` auto-detects whether the request is a small task or multi-phase feature and runs the matching flow inline
- No disambiguation question — the skill decides silently
- Output is identical to what plan-feature or plan-task would produce (same plan.md structure)

### Atomic Tasks
1. Create `skills/ltz-plan/SKILL.md` with frontmatter (`name: ltz-plan`, `description`, `disable-model-invocation: true`, `model: claude-opus-4-6`)
   - **Create:** `skills/ltz-plan/SKILL.md`
2. Write the prompt body:
   - Step 1: Clarity check (same as plan-feature/plan-task)
   - Step 2: Analyze context (scan README, package files)
   - Step 3: **Classification heuristic** — evaluate request complexity: if the request describes a single, focused change (one file area, one concern, estimatable in <30 min), classify as **task**; otherwise classify as **feature**. Announce the classification briefly (e.g. "Classified as: feature — multi-phase roadmap").
   - Step 4: If **task** → run the full plan-task flow inline (generate name, scaffold, draft atomic tasks, review loop)
   - Step 5: If **feature** → run the full plan-feature flow inline (generate name, scaffold, draft phases, review loop)
   - **Modify:** `skills/ltz-plan/SKILL.md`

### Verification
```bash
# File exists with correct frontmatter
head -6 skills/ltz-plan/SKILL.md
# Prompt contains classification logic
grep -c "task\|feature\|classif" skills/ltz-plan/SKILL.md
```

---

## Phase 2 — Add `/ltz-go` agent-mode

### Acceptance Criteria
- `/ltz-go [request]` plans + details phases + executes the entire request in one command with review gates only at: plan approval and phase-by-phase approval before execution
- `/ltz-go` (no args) detects active plan and resumes from current state (finds next backlog/ready-for-dev phase and continues)
- Uses the same `.plan/` structure and plan.md format as existing skills

### Atomic Tasks
1. Create `skills/ltz-go/SKILL.md` with frontmatter (`name: ltz-go`, `description`, `disable-model-invocation: true`, `model: claude-opus-4-6`)
   - **Create:** `skills/ltz-go/SKILL.md`
2. Write the prompt body — **with arguments (new work)**:
   - Step 1: Run the full ltz-plan flow inline (classify → scaffold → draft plan)
   - Step 2: Review gate — show plan, ask approve/change/abort (no "save and stop" option — ltz-go always continues)
   - Step 3: Loop over each phase: detail it JIT (plan-phase logic), show summary, ask approve/change before executing
   - Step 4: Execute phase (execute logic with 3-strike protocol)
   - Step 5: Mark phase done, continue to next phase
   - Step 6: Wrap up when all phases complete
   - **Modify:** `skills/ltz-go/SKILL.md`
3. Write the prompt body — **without arguments (resume)**:
   - Step 1: Read `.plan/.active`, confirm with user
   - Step 2: Read plan.md, assess current state (find first non-done phase)
   - Step 3: If phase is `ready-for-dev` → detail it JIT, then execute
   - Step 4: If phase is `ready-for-dev` → execute directly
   - Step 5: Continue looping through remaining phases
   - **Modify:** `skills/ltz-go/SKILL.md`

### Verification
```bash
# File exists with correct frontmatter
head -6 skills/ltz-go/SKILL.md
# Prompt covers both with-args and no-args flows
grep -c "ARGUMENTS\|active\|resume\|no arg" skills/ltz-go/SKILL.md
```

---

## Phase 3 — Add `/ltz-update`, `/ltz-list`, `/ltz-cleanup` wrappers

### Acceptance Criteria
- Each wrapper skill delegates to the existing underlying skill's logic
- Consistent ltz-* namespace — users never need to remember the old names
- Wrappers reference ltz-* skills in their follow-up suggestions (e.g. "Run `/ltz-go` to execute" instead of "Run `/execute`")

### Atomic Tasks
1. Create `skills/ltz-update/SKILL.md` — copy update-plan logic, update name/description, replace skill references with ltz-* equivalents
   - **Create:** `skills/ltz-update/SKILL.md`
2. Create `skills/ltz-list/SKILL.md` — copy list-plans logic, update name/description, replace skill references with ltz-* equivalents
   - **Create:** `skills/ltz-list/SKILL.md`
3. Create `skills/ltz-cleanup/SKILL.md` — copy cleanup-plan logic, update name/description, replace skill references with ltz-* equivalents
   - **Create:** `skills/ltz-cleanup/SKILL.md`

### Verification
```bash
# All three files exist
ls skills/ltz-update/SKILL.md skills/ltz-list/SKILL.md skills/ltz-cleanup/SKILL.md
# Each references ltz-* skills, not old names
grep -L "ltz-" skills/ltz-update/SKILL.md skills/ltz-list/SKILL.md skills/ltz-cleanup/SKILL.md | wc -l
# Expected: 0 (all files contain ltz- references)
```

---

## Phase 4 — Update README

### Acceptance Criteria
- ltz-* skills are featured as the primary API at the top
- Existing skills are documented under an "Advanced / Fine-grained control" section
- Flow diagrams updated to show ltz-plan and ltz-go as entry points

### Atomic Tasks
1. Rewrite the "Skills" section: ltz-* skills table first, existing skills table under "Advanced" subheading
   - **Modify:** `README.md`
2. Update the "Workflow" section: primary flow uses `ltz-plan` / `ltz-go`, with a note that fine-grained skills are still available
   - **Modify:** `README.md`
3. Update the Installation section if needed (no changes expected but verify)
   - **Modify:** `README.md`

### Verification
```bash
# README mentions all ltz-* skills
grep -c "ltz-plan\|ltz-go\|ltz-update\|ltz-list\|ltz-cleanup" README.md
# Expected: multiple matches
```

---

## Progress

| Phase | Status |
|-------|--------|
| Phase 1 — Add `/ltz-plan` smart router | `done` |
| Phase 2 — Add `/ltz-go` agent-mode | `done` |
| Phase 3 — Add ltz-update, ltz-list, ltz-cleanup wrappers | `done` |
| Phase 4 — Update README | `done` |
