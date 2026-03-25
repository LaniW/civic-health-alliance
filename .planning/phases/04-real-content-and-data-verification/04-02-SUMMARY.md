---
phase: 04-real-content-and-data-verification
plan: "02"
subsystem: data-layer
tags: [data-accuracy, nyc-districts, life-expectancy, voter-turnout, citations, consent, story-content]

dependency_graph:
  requires:
    - phase: 04-01
      provides: corrected-state-voter-turnout, state-data-methodology-comments
  provides:
    - corrected-nyc-district-mn11-le
    - nyc-districts-methodology-comments
    - accurate-level1-citations
    - story-consent-resolved
  affects: [NYC_DISTRICTS, NYCMap, LeftPanel-citations, LeftPanel-hover-card]

tech_stack:
  added: []
  patterns: [inline-source-comment-block, verified-data-replacement, consent-gate-decision]

key_files:
  created: []
  modified:
    - src/App.jsx

key-decisions:
  - "consent-confirmed (Path A): HOSPITAL_PEOPLE stories publish as-is -- Dr. Saffo name and quotes remain attributed. STORY-02 satisfied."
  - "MN11 East Harlem life expectancy corrected to 78.5 (was 77.9) based on confirmed NYC DOHMH CHP 2021 value"
  - "Level 1 citations updated from equity.nyc.gov/NYC Equity 2024 to NYC CFB Community Profiles 2020 and NYC DOHMH CHP 2021 as correct primary sources"
  - "Hover card voter turnout year label corrected from 2022 to 2020 -- data is from 2020 General Election per NYC CFB"

requirements-completed: [DATA-02, DATA-03, STORY-02]

duration: 5min
completed: 2026-03-24
---

# Phase 04 Plan 02: NYC Districts Source Comments, MN11 Fix, and Consent Gate Summary

**NYC_DISTRICTS methodology documented with DOHMH CHP 2021 and NYC CFB sources, MN11 East Harlem LE corrected to 78.5, Level 1 citations updated to correct primary sources, and STORY-02 consent confirmed -- stories publish as-is.**

## Performance

- **Duration:** 5 min
- **Started:** 2026-03-24T00:00:00Z
- **Completed:** 2026-03-24
- **Tasks:** 3 (including 1 checkpoint:decision)
- **Files modified:** 1

## Accomplishments

- Added 12-line methodology comment block above NYC_DISTRICTS documenting NYC DOHMH Bureau of Vital Statistics (le) and NYC Campaign Finance Board (vt) sources with URLs, denominators, and election cycle
- Fixed MN11 (East Harlem) life expectancy from 77.9 to 78.5 -- the one confirmed discrepancy from DOHMH CHP 2021 research
- Updated Level 1 citation links: replaced equity.nyc.gov (wrong source) with NYC CFB Community Profiles 2020 and NYC DOHMH CHP 2021 (correct primary sources)
- Fixed hover card voter turnout year label from "(2022)" to "(2020)" -- data is from the 2020 General Election, not 2022
- Consent gate (Task 2 checkpoint:decision) resolved with user selection: consent-confirmed (Path A) -- HOSPITAL_PEOPLE stories publish as-is without modification

## Task Commits

Each task was committed atomically:

1. **Task 1: Add NYC_DISTRICTS source comments, fix MN11 LE, update citations and hover label** - `ae91a6f` (feat)
2. **Task 2: Consent confirmation for patient story content (STORY-02)** - checkpoint:decision, resolved by user
3. **Task 3: Apply consent decision (Path A -- no code changes)** - no commit needed (Path A requires no changes)

## Files Created/Modified

- `src/App.jsx` - NYC_DISTRICTS methodology comment block added, MN11 le corrected, Level 1 citation links updated, hover card year label corrected

## Decisions Made

1. **Consent-confirmed (Path A)**: User confirmed consent for kiosk display of clinical transcript stories. HOSPITAL_PEOPLE stories publish as-is. Dr. Saffo's name and one attributed quote remain in the narrative. STORY-02 requirement is satisfied.

2. **MN11 LE corrected to 78.5**: Single confirmed discrepancy from 04-RESEARCH.md. All other district values remain unchanged pending full individual audit against CHP PDFs (manual task, out of scope).

3. **equity.nyc.gov replaced as source**: That URL was the wrong source for NYC district voter turnout. NYC Campaign Finance Board Community Profiles is the correct primary source for district-level registered-voter turnout in the 2020 General Election.

4. **nyc.gov/assets/doh 2023sum.pdf replaced as LE source**: The DOHMH CHP 2021 profiles page (a816-health.nyc.gov/hdi/profiles/) is the correct source for the 10-year average life expectancy values in NYC_DISTRICTS.

## Deviations from Plan

None -- plan executed exactly as written. Path A (consent-confirmed) was the user's selection, which requires no code changes to HOSPITAL_PEOPLE.

## Known Stubs

None -- all data corrections are wired directly to the visualization. NYC_DISTRICTS values flow to NYCMap colors (via leColor()) and LeftPanel hover cards. No placeholders or empty values.

## Issues Encountered

None.

## Next Phase Readiness

- DATA-01 satisfied (Phase 04-01): all US state voterTurnout values corrected to 2020 VEP rates
- DATA-02 satisfied: MN11 LE discrepancy fixed, methodology documented
- DATA-03 satisfied (combined 04-01 + 04-02): both STATE_DATA and NYC_DISTRICTS have source methodology comments
- STORY-02 satisfied: consent confirmed, stories publish as-is
- Phase 04 complete: all planned requirements (DATA-02, DATA-03, STORY-02) resolved

Remaining active requirements from PROJECT.md:
- NavBar back arrow button (touch screen navigation) -- pending Phase 05
- Tappable breadcrumbs -- pending Phase 05
- Full 59-district audit of NYC_DISTRICTS values against DOHMH CHP PDFs -- manual task deferred

## Self-Check: PASSED

Files verified:
- FOUND: src/App.jsx
- FOUND: .planning/phases/04-real-content-and-data-verification/04-02-SUMMARY.md

Commits verified:
- FOUND: ae91a6f (feat(04-02): add NYC_DISTRICTS source comments, fix MN11 LE, update citations and hover label)

---
*Phase: 04-real-content-and-data-verification*
*Completed: 2026-03-24*
