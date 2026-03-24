---
phase: 01-story-data-decoupling
plan: "01"
subsystem: data-layer
tags: [data-decoupling, person-story, hospital-people, refactor]
dependency_graph:
  requires: []
  provides: [STORY-01]
  affects: [PersonStory, HOSPITAL_PEOPLE]
tech_stack:
  added: []
  patterns: [data-driven-rendering, falsy-guard]
key_files:
  created: []
  modified:
    - src/App.jsx
decisions:
  - "summary field placed after bg and before timeline on each person object to maintain existing data layout order"
  - "callout box wrapped in person.summary guard rather than always rendering (hides box instead of showing blank)"
  - "shared tagline stays hardcoded in component per D-01 (universal message, not per-person content)"
metrics:
  duration: "1 minute"
  completed: "2026-03-24"
  tasks_completed: 2
  files_modified: 1
requirements:
  - STORY-01
---

# Phase 01 Plan 01: PersonStory Data Decoupling Summary

**One-liner:** Data-driven callout rendering using person.summary field, eliminating all person.id conditionals from PersonStory component.

## What Was Built

PersonStory now renders its callout summary text entirely from the `person.summary` data field rather than from hard-coded `person.id` conditional checks. All 4 person objects in `HOSPITAL_PEOPLE` received a `summary` string field containing their respective callout text. The callout box is guarded with `person.summary&&(...)` so a person with no summary field simply hides the box instead of rendering blank text.

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Add summary field to HOSPITAL_PEOPLE data | 987c41f | src/App.jsx |
| 2 | Replace person.id conditionals with person.summary in PersonStory | 2578b2b | src/App.jsx |

## Verification Results

1. Zero person.id conditionals: `grep -c 'person\.id===' src/App.jsx` returns 0 — PASS
2. Summary field on all 4 people: `grep -c 'summary:' src/App.jsx` returns 4 — PASS
3. PersonStory reads from data: `grep 'person\.summary' src/App.jsx` matches 2 lines (guard + render) — PASS
4. Build succeeds: `npm run build` exits 0 — PASS
5. Lint passes: `npm run lint` exits 0 — PASS
6. Robustness: Changing any person's `id` value does not affect their story display — CONFIRMED (summary comes from `person.summary`, not id match)

## Key Changes

**HOSPITAL_PEOPLE (lines 201-204 of src/App.jsx):**
Each of the 4 person objects gained a `summary:` field placed after `bg` and before `timeline`:
- maria: "Her son got screened because parents organized. Her housing got fixed because she testified. Her dental care stalled because nobody voted."
- dr-okafor: "She came home to serve her community. Low reimbursement rates — set without local input — mean she carries triple the recommended patient load."
- james: "He almost lost years of his life to a coverage gap. A navigator program, funded through local advocacy, caught him in time."
- sofia: "She waited 8 months for a $120 filling. The ER visit cost $1,800. The dental program died in an election with 6% turnout."

**PersonStory callout box (lines 542-549 of src/App.jsx):**
Replaced 4 `person.id===` conditional expressions with single `{person.summary}` expression, wrapped the entire callout div in `{person.summary&&(...)}` guard. Shared tagline remains hardcoded.

## Deviations from Plan

None - plan executed exactly as written.

## Known Stubs

None - all 4 person objects have populated summary fields. The callout box renders real data for every person in the current HOSPITAL_PEOPLE array.

## Self-Check: PASSED

- src/App.jsx modified: FOUND
- Commit 987c41f (Task 1): FOUND
- Commit 2578b2b (Task 2): FOUND
- 4 summary fields: FOUND
- 0 person.id=== occurrences: CONFIRMED
- person.summary guard + render: FOUND (lines 542, 545)
- Build passes: CONFIRMED
- Lint passes: CONFIRMED
