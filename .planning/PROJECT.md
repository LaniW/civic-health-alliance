# Civic Health Alliance

## What This Is

An interactive data visualization that maps the relationship between civic engagement and health outcomes across the US, drilling from national to individual level. Built as a single-page React app with SVG-based maps and animations. Fully touch-navigable with verified public health data and real patient stories from clinical interviews.

## Core Value

The visualization must present accurate, real data and stories — real patient experiences from clinical interviews, verified statistics from cited sources — and be fully navigable on touch screens.

## Requirements

### Validated

- ✓ 4-level drill-down (Title → US Map → NYC Map → Hospital → Person Story) — existing
- ✓ Hexagonal US state map colored by life expectancy + voter turnout — existing
- ✓ NYC community district map (59 districts, SVG paths) — existing
- ✓ Hospital scene with departments, wait times, and person figures — existing
- ✓ Person story timeline with health events and civic actions — existing
- ✓ Left panel with chapter narrative, legends, and data sources — existing
- ✓ Hover interactions showing state/district/person details — existing
- ✓ Zoom transitions with fade animation between levels — existing
- ✓ PersonStory renders from data fields (person.summary), not hard-coded person IDs — Validated in Phase 1: Story Data Decoupling
- ✓ Hospital scene: person figures centered within their department rooms — Validated in Phase 3: Hospital Scene Layout
- ✓ Hospital scene: no text overlap between labels/metrics and person figures — Validated in Phase 3: Hospital Scene Layout
- ✓ Hospital scene: 44px+ touch targets on all person figures — Validated in Phase 3: Hospital Scene Layout
- ✓ Hospital scene: remote geometry/content merged with local technical features — Validated in Phase 03.1: Remote/Local Merge
- ✓ NavBar: three-tab navigation (US Map, NYC Map, Health Center) with bidirectional zoom — Validated in Phase 03.1: Remote/Local Merge
- ✓ Patient stories reflect real clinical experiences from Stella Saffo transcript (consent confirmed) — Validated in Phase 4: Real Content and Data Verification
- ✓ All US state voterTurnout values match UF Election Lab 2020 VEP rates — Validated in Phase 4: Real Content and Data Verification
- ✓ NYC district MN11 life expectancy corrected to match DOHMH CHP 2021 — Validated in Phase 4: Real Content and Data Verification
- ✓ Source methodology documented in code comments for STATE_DATA and NYC_DISTRICTS — Validated in Phase 4: Real Content and Data Verification
- ✓ Citation links at Level 0 and Level 1 reference correct primary sources — Validated in Phase 4: Real Content and Data Verification
- ✓ NavBar: back arrow button (44px+ touch target) for returning to previous level — Validated in Phase 2: Touch Navigation
- ✓ NavBar: tappable Health Center breadcrumb at level 3 for backward navigation — Validated in Phase 2: Touch Navigation
- ✓ touchAction:manipulation on root div eliminates 300ms tap delay — Validated in Phase 2: Touch Navigation
- ✓ Swipe left/right gesture navigation between adjacent levels — Validated in Phase 2: Touch Navigation

### Active

None — all milestone v1.0 requirements validated.

### Out of Scope

- Code decomposition / refactoring into multiple files — not part of this work
- Adding new zoom levels or data dimensions — current structure is final
- Backend / API / database — data stays embedded in JS

## Context

- App is a single-file monolith (`src/App.jsx`, ~867 lines) — all data, components, and styling inline
- v1.0 shipped: touch navigation (back arrow, breadcrumbs, swipe), verified data, real patient stories
- Touch screen is a primary use case (kiosk/presentation) — back arrow, breadcrumbs, and swipe gestures all functional
- Real patient stories from Stella Saffo transcript — 3 patient experiences (Medicaid/pancreatitis, HIV care continuity, pregnant person housing), consent confirmed
- Stats verified against primary sources: UF Election Lab (voter turnout, 2020 VEP), KFF ACS (uninsured rate, 2022-2023), CDC NVSR Vol. 74 No. 12 (life expectancy, 2022), NYC CFB (NYC voter turnout, 2020), NYC DOHMH CHP 2021 (NYC life expectancy, 2010-2019 avg)

## Constraints

- **Tech stack**: React + Vite, no additional dependencies — keep it minimal
- **Single file**: All changes go in `src/App.jsx` for now
- **Data accuracy**: All displayed numbers must match cited sources exactly
- **Touch usability**: All navigation must work without a mouse or browser back button

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Replace fictional stories with real transcript | Authenticity matters — real patient voices are more impactful | Done — consent confirmed, stories publish as-is |
| Back arrow + tappable breadcrumbs (both) | Touch screen users need explicit navigation controls | Done — back arrow, breadcrumbs, swipe gestures all implemented |
| Swipe capped at level 2 (no swipe into story) | Story selection requires choosing a specific person | Done — swipe left/right between levels 0-2 only |
| useRef for touch coordinates (not useState) | Mutable refs avoid re-renders during gesture tracking | Done — touchStartX/touchStartY refs in App |
| Single-file monolith preserved through v1.0 | Minimizes complexity for initial milestone | Done — all 867 LOC in src/App.jsx |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd:transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-03-25 after v1.0 milestone*
