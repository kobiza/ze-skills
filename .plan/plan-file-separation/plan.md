# Plan File Separation

## Goal
Split the monolithic `.plan/<feature>/plan.md` into role-owned files so that `plan.md` is owned and modified ONLY by top-level planning skills. Phase tactics live in `phases/phase-N.md` and runtime state lives in `progress.md`, giving each skill a clean, single-purpose write target.

## Scope

**In Scope**
- Define the new `.plan/<feature>/` layout (`plan.md`, `progress.md`, `findings.md`, `phases/phase-N.md`).
- Update `CLAUDE.md` to document the new structure and write-ownership rules.
- Refactor every skill in `ltz/commands/` so its file writes match the ownership rules:
  - `plan`, `plan-feature`, `plan-task`, `update` — own `plan.md`.
  - `plan-phase` — owns `phases/phase-N.md`, updates `progress.md`.
  - `execute` — updates `progress.md` and `findings.md` only.
  - `list`, `choose-plan`, `go`, `cleanup` — read-only or directory-level.
- Hard cutover: skills assume the new layout; no auto-migration of existing folders.

**Out of Scope**
- Migration code or compatibility shims for old `.plan/` folders.
- Changes to the public command surface (`/ltz:plan`, `/ltz:go`, etc. keep their names and behaviors).
- Changes to the human-in-the-loop review pattern.
- Bumping the plugin version or shipping (left to a separate decision).

## Phases

- [ ] **Phase 1 — Spec & creation skills [Must]**
  Define the new layout in `CLAUDE.md`, then update `plan.md` (router), `plan-feature`, and `plan-task` so a fresh plan scaffolds the new structure. `plan.md` contains Goal/Scope/Phases as plain bullets (no checkboxes). `progress.md` holds the status table and `[ ]/[x]` markers. Empty `phases/` directory created.

- [ ] **Phase 2 — Phase detailing skill [Must]** _(depends on Phase 1)_
  Refactor `plan-phase` to write the granular implementation guide to `phases/phase-N.md` instead of appending to `plan.md`. On approve/execute, update only `progress.md` (status `backlog` → `ready-for-dev`). Never touch `plan.md`.

- [ ] **Phase 3 — Execute skill [Must]** _(depends on Phase 2)_
  Refactor `execute` to read tasks from `phases/phase-N.md`, update only `progress.md` on completion (status → `done`, checkbox → `[x]`), and append errors to `findings.md`. Strip every reference that writes to `plan.md`.

- [ ] **Phase 4 — Auxiliary skills [Must]** _(depends on Phases 1–3)_
  Update `list`, `choose-plan`, `go`, `update`, and `cleanup` to read status from `progress.md` and respect the new layout. `update` keeps write access to `plan.md`; the rest are read-only against it. Verify the full flow end-to-end on a fresh plan.

## Progress

_Phase status, checkbox markers, and execution state live in `progress.md`. This file (`plan.md`) does not change after creation except via `/ltz:update`._
