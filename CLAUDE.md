# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code plugin by Kobi Zakut — structured planning and execution workflow. The plugin is called `ltz` and skills are namespaced as `/ltz:<skill-name>`.

## Plugin structure

```
ltz/
├── .claude-plugin/
│   └── plugin.json          ← plugin manifest (name, version, description, author)
└── commands/
    └── <command-name>.md    ← YAML frontmatter + prompt body
```

Each command lives in `ltz/commands/<command-name>.md` with YAML frontmatter:

```markdown
---
description: <one-liner>    # shown to users in autocomplete
---

<prompt body>
```

The prompt body is the full instruction set Claude receives when the command is invoked. Commands are invoked as `/ltz:<command-name>`. They act as role-specific agents (Architect, Analyst, Engineer) and must not write application code unless their role explicitly calls for it.

## The planning workflow

These skills implement a structured, human-in-the-loop planning loop. The primary API:

```
/ltz:plan [request] → auto-classifies → plan → review loop
/ltz:go             → resumes active plan → phase detail → execute → done
```

Fine-grained commands (`/ltz:plan-feature`, `/ltz:plan-task`, `/ltz:plan-phase`, `/ltz:execute`) are available for explicit control over individual steps.

**Plan state is stored in `.plan/` inside the target project** (not this repo):

```
.plan/
├── .active                  ← name of the currently focused plan
└── <feature-name>/
    ├── plan.md              ← strategy: Goal, Scope, Phases (plain bullets, no checkboxes). Immutable after creation.
    ├── progress.md          ← state: phase status table and `[ ]/[x]` markers. Mutable as work advances.
    ├── findings.md          ← error log and learnings (3-strike protocol).
    └── phases/
        └── phase-N.md       ← per-phase tactics (atomic tasks, file paths, verification). Written JIT.
```

Task plans (single-step) omit the `phases/` directory; their `progress.md` is just `Status: <state>`.

### Write ownership

`plan.md` must only be modified by the planning skills listed below — execution skills never touch it. This keeps the strategic spec stable across iterations.

| Skill            | Writes                                                          | Reads                                       |
|------------------|-----------------------------------------------------------------|---------------------------------------------|
| `plan` (router)  | (delegates only)                                                | —                                           |
| `plan-feature`   | `plan.md`, `progress.md`, `findings.md`, `phases/` (creates)    | —                                           |
| `plan-task`      | `plan.md`, `progress.md`, `findings.md` (creates)               | —                                           |
| `update`         | `plan.md`, `progress.md`                                        | `plan.md`, `progress.md`, `findings.md`     |
| `plan-phase`     | `phases/phase-N.md` (creates), `progress.md` (updates)          | `plan.md`, `progress.md`, `findings.md`     |
| `execute`        | `progress.md` (updates), `findings.md` (appends)                | `plan.md`, `progress.md`, `phases/*`        |
| `list`, `choose-plan`, `go` | —                                                    | `plan.md`, `progress.md`                    |
| `cleanup`        | deletes the plan folder                                         | `.active`                                   |

Key behaviors:
- `/ltz:plan`: auto-detects task vs feature, scaffolds `.plan/<name>/`, drafts plan — then pauses for human review.
- `/ltz:go`: drives active plan to completion via plan-phase → execute loop with review gates.
- `/ltz:update`: re-analyzes active plan with changed requirements, preserving completed work.
- `/ltz:list`: scans `.plan/`, reads each plan's `progress.md` for status, lets user switch active plan.
- `/ltz:cleanup`: deletes a plan folder, clears `.plan/.active` if needed.
- `/ltz:plan-phase`: reads active plan, finds first `backlog` phase in `progress.md`, writes granular atomic tasks + verification commands to `phases/phase-N.md` JIT — then pauses.
- `/ltz:execute`: reads tasks from `phases/phase-N.md`, implements step by step; on 3 identical errors, stops and logs to `findings.md`; on success, updates only `progress.md` (status → `done`, checkbox → `[x]`).

## Adding or modifying commands

1. Create `ltz/commands/<command-name>.md` with frontmatter containing only `description`.
2. The filename (without `.md`) becomes the command name, invoked as `/ltz:<command-name>`.
3. Commands always pause and ask the user before proceeding to the next stage — preserve this human-in-the-loop pattern.
4. `.claude/` is gitignored and should not be committed.
