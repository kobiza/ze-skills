# Hide internal skills in user-facing messages

## Goal

Stop pointing the user at internal (non-user-invocable) skills in confirmation messages. Only `/ltz:plan` and `/ltz:go` should be surfaced as "for running" entry points; `/ltz:list`, `/ltz:cleanup`, `/ltz:update` remain valid as utility/management references.

## Acceptance Criteria

- No user-facing confirmation/error string in any `ltz/commands/*.md` file mentions an internal skill (`/ltz:plan-task`, `/ltz:plan-feature`, `/ltz:plan-phase`, `/ltz:execute`, `/ltz:choose-plan`).
- Internal skill names still appear where they are used as instructions to Claude (e.g. "Invoke `/ltz:plan-phase`", "delegating to `/ltz:plan-task`") — those are not user-facing.
- After the fix, the running flow funnels users to `/ltz:plan` or `/ltz:go` only.

## Root cause

Internal skills are marked `user-invocable: false` in their frontmatter, but several skill prompts still print messages like *"Run `/ltz:execute` when you're ready"*. Those strings are produced by the running skill and shown to the user verbatim, so the user keeps seeing internal commands they cannot (and should not) run.

## Atomic Tasks

1. **Fix `ltz/commands/plan-task.md` (line ~41)** — Change *"Plan saved. Run `/ltz:execute` when you're ready."* → *"Plan saved. Run `/ltz:go` when you're ready."*

2. **Fix `ltz/commands/plan-feature.md` (line ~47)** — Change *"Roadmap approved. Run `/ltz:plan-phase` to detail the first phase, or `/ltz:execute` to start immediately."* → *"Roadmap approved. Run `/ltz:go` to start executing."*

3. **Fix `ltz/commands/plan-phase.md` (line ~21)** — Change *"All phases are already planned. Run `/ltz:execute` to start the next ready phase."* → *"All phases are already planned. Run `/ltz:go` to start the next ready phase."*

4. **Fix `ltz/commands/plan-phase.md` (line ~33)** — Change *"Tasks approved. Run `/ltz:execute` to implement this phase."* → *"Tasks approved. Run `/ltz:go` to implement this phase."*

5. **Fix `ltz/commands/choose-plan.md` (line ~10)** — Change *"No plans found. Start one with `/ltz:plan-task` (small task) or `/ltz:plan-feature` (multi-phase feature)."* → *"No plans found. Start one with `/ltz:plan`."*

6. **Fix `ltz/commands/update.md` (line ~58)** — Change *"Plan updated. Run `/ltz:go` to execute, or `/ltz:plan-phase` to detail the next phase."* → *"Plan updated. Run `/ltz:go` to execute."*

## Verification

Run from repo root:

```bash
# Should print zero lines — no user-facing strings ("Run `/ltz:<internal>`" or
# "Start one with `/ltz:<internal>`") should reference internal skills anymore.
grep -nE '(Run|Start one with) `/ltz:(plan-task|plan-feature|plan-phase|execute|choose-plan)`' ltz/commands/*.md
```

```bash
# Sanity: /ltz:plan and /ltz:go should be the only "Run `/ltz:...`" tokens
# that survive in user-facing strings.
grep -nE 'Run `/ltz:[a-z-]+`' ltz/commands/*.md
```

## Progress

Status: `done`

**Status: Done ✓**
