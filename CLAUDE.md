# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code plugin by Kobi Zakut — structured planning and execution workflow. The plugin is called `ltz` and skills are namespaced as `/ltz:<skill-name>`.

## Plugin structure

```
ltz/
├── .claude-plugin/
│   └── plugin.json          ← plugin manifest (name, version, description, author)
├── skills/
│   └── <skill-name>/
│       └── SKILL.md         ← YAML frontmatter + prompt body
└── README.md
```

Each skill lives in `ltz/skills/<skill-name>/SKILL.md` with YAML frontmatter:

```markdown
---
name: <skill-name>          # matches the folder name
description: <one-liner>    # shown to users; also used to trigger the skill
disable-model-invocation: true  # prevents recursive invocation
---

<prompt body>
```

The prompt body is the full instruction set Claude receives when the skill is invoked. Skills act as role-specific agents (Architect, Analyst, Engineer) and must not write application code unless their role explicitly calls for it.

## The planning workflow

These skills implement a structured, human-in-the-loop planning loop. The primary API:

```
/ltz:ltz-plan [request] → auto-classifies → plan → review loop
/ltz:ltz-go             → resumes active plan → phase detail → execute → done
```

Fine-grained skills (`/ltz:ltz-plan-feature`, `/ltz:ltz-plan-task`, `/ltz:ltz-plan-phase`, `/ltz:ltz-execute`) are available for explicit control over individual steps.

**Plan state is stored in `.plan/` inside the target project** (not this repo):

```
.plan/
├── .active              ← name of the currently focused plan
└── <feature-name>/
    ├── plan.md          ← roadmap with phase checklist; granular tasks appended JIT
    └── findings.md      ← error log and learnings (3-strike protocol)
```

Key behaviors:
- `/ltz:ltz-plan`: auto-detects task vs feature, scaffolds `.plan/<name>/`, drafts plan — then pauses for human review.
- `/ltz:ltz-go`: drives active plan to completion via plan-phase → execute loop with review gates.
- `/ltz:ltz-update`: re-analyzes active plan with changed requirements, preserving completed work.
- `/ltz:ltz-list`: scans `.plan/`, shows progress table, lets user switch active plan.
- `/ltz:ltz-cleanup`: deletes a plan folder, clears `.plan/.active` if needed.
- `/ltz:ltz-plan-phase`: reads active plan, finds first unchecked `[ ]` phase, appends granular atomic tasks + verification commands JIT — then pauses.
- `/ltz:ltz-execute`: implements tasks step by step; on 3 identical errors, stops and logs to `findings.md`; marks phase `[x]` on success.

## Adding or modifying skills

1. Create `ltz/skills/<skill-name>/SKILL.md` with the frontmatter above.
2. The `name` field must match the directory name.
3. Skills always pause and ask the user before proceeding to the next stage — preserve this human-in-the-loop pattern.
4. `.claude/` is gitignored and should not be committed.
