# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of Claude Code skills (slash commands) by Kobi Zakut, published for use with the [`npx skills`](https://github.com/vercel-labs/skills) CLI. Skills are installed into other projects via:

```bash
npx skills add kobiza/ze-skills
npx skills add kobiza/ze-skills --skill <skill-name>
```

## Skill structure

Each skill lives in `skills/<skill-name>/SKILL.md` with YAML frontmatter:

```markdown
---
name: <skill-name>          # matches the folder name; used by npx skills CLI
description: <one-liner>    # shown to users; also used to trigger the skill
disable-model-invocation: true  # prevents recursive invocation
---

<prompt body>
```

The prompt body is the full instruction set Claude receives when the skill is invoked. Skills act as role-specific agents (Architect, Analyst, Engineer) and must not write application code unless their role explicitly calls for it.

## The planning workflow

These skills implement a structured, human-in-the-loop planning loop:

```
/start-plan → /plan-phase → /execute-phase
                  ↑               ↓
             (next phase)   (phase done)
```

**Plan state is stored in `.plan/` inside the target project** (not this repo):

```
.plan/
├── .active              ← name of the currently focused plan
└── <feature-name>/
    ├── plan.md          ← roadmap with phase checklist; granular tasks appended JIT
    └── findings.md      ← error log and learnings (3-strike protocol)
```

Key behaviors:
- `start-plan`: scaffolds `.plan/<name>/`, writes `.plan/.active`, drafts high-level phases with MoSCoW priorities — then pauses for human review.
- `plan-phase`: reads active plan, finds first unchecked `[ ]` phase, appends granular atomic tasks + verification commands JIT — then pauses.
- `execute-phase`: implements tasks step by step; on 3 identical errors, stops and logs to `findings.md`; marks phase `[x]` on success.
- `list-plans`: scans `.plan/`, shows progress table, lets user switch active plan.
- `cleanup-plan`: deletes a plan folder, clears `.plan/.active` if needed.

## Adding or modifying skills

1. Create `skills/<skill-name>/SKILL.md` with the frontmatter above.
2. The `name` field must match the directory name for `npx skills` compatibility.
3. Skills always pause and ask the user before proceeding to the next stage — preserve this human-in-the-loop pattern.
4. `.claude/` is gitignored and should not be committed.
