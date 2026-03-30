# Add /ltz-brainstorm skill

## Goal

Add a `/ltz-brainstorm` skill that facilitates exploratory, free-form discussion before committing to a plan — asking probing questions, presenting options with trade-offs, and handing off to `/ltz-plan` once a direction is chosen.

## Scope

**In Scope:**
- New `skills/ltz-brainstorm/SKILL.md`
- Flexible flow: starts with Q&A exploration, converges on concrete options, lets user pick a direction
- Hand-off to `/ltz-plan` at the end (optional — user can also stop after the summary)
- Update README to include the new skill in the ltz-* table

**Out of Scope:**
- Changes to any existing skill
- Persistent brainstorm artifacts (no `.plan/` scaffolding during brainstorm — that happens when `/ltz-plan` is invoked)

## Phases

- [x] **Phase 1 — Create `/ltz-brainstorm` skill and update README** *(Must Have)*
  Write the SKILL.md with the brainstorm flow (explore → converge → hand off) and add the skill to the README's ltz-* table.

## Progress

| Phase | Status |
|-------|--------|
| Phase 1 — Create `/ltz-brainstorm` skill and update README | `done` |
