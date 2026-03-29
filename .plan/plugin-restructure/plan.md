# Plugin Restructure — ltz

## Goal

Restructure ze-skills from a flat skills repo into the official Claude Code plugin format. The plugin is called `ltz`, giving skills a clean namespaced API: `/ltz:plan`, `/ltz:go`, `/ltz:execute`, etc. Remove ltz-brainstorm.

## Scope

**In Scope:**
- Create `ltz/` plugin directory with `.claude-plugin/plugin.json`
- Move all skills from `skills/` into `ltz/skills/`, renaming `ltz-*` skills to drop the prefix (e.g. `ltz-plan` → `plan`)
- Update skill `name` fields in frontmatter to match new directory names
- Update cross-skill references (e.g. `/ltz-plan` → `/ltz:plan`, `/plan-feature` → `/ltz:plan-feature`)
- Remove `ltz-brainstorm` (not carried over)
- Remove the root `skills/` directory
- Update README.md and CLAUDE.md

**Out of Scope:**
- Adding `.mcp.json`, agents, hooks, or LSP servers
- Changing actual skill prompt logic (only names and references change)
- Publishing to a marketplace (future task)

## Target structure

```
ze-skills/
├── ltz/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── skills/
│   │   ├── plan/SKILL.md           # /ltz:plan (was /ltz-plan)
│   │   ├── go/SKILL.md             # /ltz:go (was /ltz-go)
│   │   ├── update/SKILL.md         # /ltz:update (was /ltz-update)
│   │   ├── list/SKILL.md           # /ltz:list (was /ltz-list)
│   │   ├── cleanup/SKILL.md        # /ltz:cleanup (was /ltz-cleanup)
│   │   ├── plan-feature/SKILL.md   # /ltz:plan-feature
│   │   ├── plan-task/SKILL.md      # /ltz:plan-task
│   │   ├── plan-phase/SKILL.md     # /ltz:plan-phase
│   │   ├── execute/SKILL.md        # /ltz:execute
│   │   └── choose-plan/SKILL.md    # /ltz:choose-plan
│   └── README.md
├── CLAUDE.md
└── README.md
```

## Phases

- [x] **Phase 1 — Restructure to plugin format** *(Must Have)*
  Create plugin directory, move and rename skills, update frontmatter names, update all cross-skill references to use `/ltz:` namespace, remove ltz-brainstorm and root skills/, update README and CLAUDE.md.

## Progress

| Phase | Status |
|-------|--------|
| Phase 1 — Restructure to plugin format | `done` |
