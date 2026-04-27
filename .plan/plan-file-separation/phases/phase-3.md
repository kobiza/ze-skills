# Phase 3 — Execute Skill

## Acceptance Criteria
- `/ltz:execute` reads the list of `ready-for-dev` phases from `progress.md` (not from `plan.md`'s `## Progress` table) and pulls each phase's atomic tasks from the corresponding `phases/phase-N.md`.
- On phase completion, the skill updates ONLY `progress.md` (status → `done`, checkbox → `[x]`). It MUST NOT modify `plan.md`.
- The skill body explicitly states it writes only to `progress.md` (updates) and `findings.md` (appends).

## Atomic Tasks

1. **Add ownership note near the top of execute.md.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md` body (just below the "Your job is to act as the Engineer and write the code." sentence): add one paragraph stating the skill writes ONLY to `progress.md` and `findings.md`, and never modifies `plan.md` or `phases/phase-N.md`.

2. **Read ready-for-dev rows from `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md` step 2 (line 17): change "Read `.plan/[active-name]/plan.md` and collect the full ordered list of phases with status `ready-for-dev` from the `## Progress` table" → "Read `.plan/[active-name]/progress.md` and collect the full ordered list of `ready-for-dev` rows. Capture each row's 1-based index as the phase number `N`. For each, also read `.plan/[active-name]/phases/phase-N.md` to get the atomic tasks and verification commands."

3. **Make step 3a reference the phase file.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md` step 3a (line 24): change "Implement the atomic tasks for this phase step-by-step." → "Implement the atomic tasks defined in `phases/phase-N.md` step-by-step."

4. **Redirect step 3c verify-and-update to `progress.md`.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md` step 3c (lines 32–35): replace the two bullets "Change the phase's checkbox from `[ ]` to `[x]` in `plan.md`" and "Update the matching row in the `## Progress` table to `done`" with a single bullet: "In `progress.md`, update the row for phase N — set status to `done` and the Done column to `[x]`. Do NOT modify `plan.md`."

5. **Confirm step 3b (3-strike protocol) still writes to findings.md only.**
   Modify `/Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md` step 3b (line 26): no change in target (already `findings.md`), but reword to make explicit: "Append the error, what you tried, and your hypothesis to `.plan/[active-name]/findings.md`." (was "Log… into".) Keep the rest of the protocol intact.

## Verification

```bash
grep -n "phases/phase\|progress.md\|findings.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md
grep -nEi "in \`plan\.md\`|update.*plan\.md|modify.*plan\.md|edit.*plan\.md|change.*plan\.md" /Users/kobiz/my_projects/ze-skills/ltz/commands/execute.md | grep -viE "MUST NOT|do NOT|never|NOT \`plan"
```

Expected: first grep shows multiple references to `phases/phase`, `progress.md`, and `findings.md`. Second grep returns nothing — every remaining mention of `plan.md` is wrapped in a prohibition.
