---
phase: 04-real-content-and-data-verification
plan: "01"
subsystem: data-layer
tags: [data-accuracy, voter-turnout, state-data, citations, vep, uf-election-lab]
dependency_graph:
  requires: []
  provides: [corrected-state-voter-turnout, state-data-methodology-comments, accurate-level0-citations]
  affects: [STATE_DATA, engagementColor-visual-output, LeftPanel-citations]
tech_stack:
  added: []
  patterns: [inline-source-comment-block, verified-data-replacement]
key_files:
  created: []
  modified:
    - src/App.jsx
decisions:
  - "Kept engagementColor breakpoints (70/64/58) unchanged -- corrected VEP data is more accurate; the visual shift toward greener states is an intended consequence of fixing the data, not a calibration error"
  - "Citation links updated from lgbtmap.org/MAP 2024 to UF Election Lab 2020 VEP as the correct primary source for national voter turnout"
metrics:
  duration: "2 minutes"
  completed: "2026-03-25"
  tasks_completed: 2
  files_modified: 1
---

# Phase 04 Plan 01: State Data VEP Voter Turnout Correction Summary

Replaced all 51 STATE_DATA voterTurnout values with verified 2020 VEP turnout rates from UF Election Lab (Michael McDonald v1.2), added a methodology comment block documenting all three data sources above STATE_DATA, and updated Level 0 citation links to reference correct primary sources.

## Tasks Completed

| # | Task | Commit | Files |
|---|------|--------|-------|
| 1 | Add source methodology comment block above STATE_DATA | 17363c1 | src/App.jsx |
| 2 | Replace all 51 voterTurnout values and update Level 0 citations | 8dcbb1d | src/App.jsx |

## What Was Built

### Task 1: Source Methodology Comment Block

Added a 15-line comment block immediately above `const STATE_DATA = {` documenting all three metrics with their sources, URLs, denominators, and years:

- `voterTurnout`: UF Election Lab 2020 VEP, v1.2 — with denominator (VEP: citizens 18+, excluding ineligible felons) and election cycle (November 2020 Presidential General Election)
- `uninsured`: KFF State Health Facts, ACS-based estimates — Year: 2022/2023 ACS
- `lifeExp`: CDC NCHS, NVSR Vol. 74 No. 12 (December 4, 2025), Table A

### Task 2: voterTurnout Values + Citation Links

Replaced all 51 `voterTurnout` values from VAP-based (or prior-cycle) estimates to verified 2020 VEP rates. Notable corrections:

| State | Old | New | Delta |
|-------|-----|-----|-------|
| CA | 62.1 | 68.8 | +6.7 |
| WA | 70.2 | 75.3 | +5.1 |
| HI | 50.3 | 55.4 | +5.1 |
| NJ | 67.2 | 72.0 | +4.8 |
| DC | 63.6 | 68.9 | +5.3 |
| AK | 63.8 | 68.2 | +4.4 |
| FL | 66.7 | 70.8 | +4.1 |
| CO | 73.1 | 76.7 | +3.6 |
| MN | 76.4 | 79.2 | +2.8 |
| GA | 68.3 | 67.1 | -1.2 |
| MI | 74.6 | 73.3 | -1.3 |
| WI | 76.6 | 75.0 | -1.6 |

Updated Level 0 citation links:
- Voter Turnout: lgbtmap.org/MAP 2024 -> UF Election Lab, 2020 VEP
- Uninsured Rate: "KFF, 2024" -> "KFF ACS, 2022-2023"
- Life Expectancy: "CDC NVSR, 2023" -> "CDC NVSR Vol. 74 No. 12, 2022"

## Decisions Made

1. **engagementColor breakpoints unchanged**: The corrected VEP data shifts values upward by ~3pp on average, pushing more states into green tiers. This is correct behavior -- the visualization now accurately reflects higher civic participation levels. Breakpoints at 70/64/58 remain.

2. **lgbtmap.org removed as source**: The MAP citation was a wrong source for national VEP voter turnout. Replaced with UF Election Lab (Michael McDonald), the authoritative academic source used for national turnout research.

3. **KFF and CDC citations kept but text corrected**: URLs for KFF uninsured and CDC life expectancy were already correct; only the link text was inaccurate (wrong year/source name).

## Deviations from Plan

None -- plan executed exactly as written.

## Known Stubs

None -- all voterTurnout values are fully wired to real data. The engagementColor function receives the corrected values directly.

## Verification Results

All acceptance criteria satisfied:
- AL: voterTurnout:61.5 (was 58.9) -- PASS
- CA: voterTurnout:68.8 (was 62.1) -- PASS
- HI: voterTurnout:55.4 (was 50.3) -- PASS
- MN: voterTurnout:79.2 (was 76.4) -- PASS
- NH: voterTurnout:74.0 (unchanged) -- PASS
- ND: voterTurnout:63.1 (unchanged) -- PASS
- WY: voterTurnout:64.7 (was 61.3) -- PASS
- Zero instances of old values (voterTurnout:58.9, voterTurnout:62.1) -- PASS
- Citation text "UF Election Lab, 2020 VEP" present -- PASS
- Citation text "KFF ACS, 2022-2023" present -- PASS
- Citation text "CDC NVSR Vol. 74 No. 12, 2022" present -- PASS
- href "https://election.lab.ufl.edu/voter-turnout/2020-general-election-turnout/" present -- PASS
- No lgbtmap.org href -- PASS
- No "MAP, 2024" text -- PASS
- engagementColor breakpoints 70/64/58 unchanged -- PASS
- npm run build exits 0 -- PASS
- npm run lint exits 0 -- PASS
- Source comment block contains all required strings -- PASS

## Requirements Progress

- DATA-01: Partially satisfied -- US state voterTurnout corrected; lifeExp and uninsured already matched sources (no changes needed)
- DATA-03: Partially satisfied -- STATE_DATA methodology documented with source, URL, denominator, election cycle, and year for all three metrics

## Self-Check: PASSED

Files created/modified:
- FOUND: src/App.jsx

Commits:
- FOUND: 17363c1 (chore: add STATE_DATA source methodology comment block)
- FOUND: 8dcbb1d (feat: replace all 51 voterTurnout values with 2020 VEP data)
