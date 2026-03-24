---
phase: 03-hospital-scene-layout
plan: "01"
subsystem: ui
tags: [react, svg, touch, hospital-scene, layout]

# Dependency graph
requires: []
provides:
  - Person figures centered in department rooms (x matches room center_x for all four figures)
  - Person figure y-coordinates place heads below metricLabel text (2.65 SVG unit clearance)
  - 12x12 SVG unit transparent touch targets on each person figure (48.96px at 768px viewport)
  - onTouchEnd/onTouchCancel handlers clearing sticky hover on person and department g elements
affects: [03-02, phase-04-content]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "fill='transparent' SVG rects as touch targets (NOT fill='none' — transparent receives pointer events)"
    - "onTouchEnd + onTouchCancel to clear hover state on touch screens (onMouseLeave does not fire on touch)"
    - "Touch target rect placed as FIRST child of interactive g so it receives events before visual elements"

key-files:
  created: []
  modified:
    - src/App.jsx

key-decisions:
  - "x/y swap confirmed: patient-one was at x:60 (maternal center) but belongs in insurance room (center_x=72); patient-two was at x:72 (insurance center) but belongs in maternal room (center_x=60)"
  - "fill='transparent' chosen over fill='none' — SVG hit testing requires painted fill; transparent is painted"
  - "Touch target rect is 12x12 SVG units: 12 * 4.08 px/unit = 48.96px at 768px viewport, exceeding 44px minimum"

patterns-established:
  - "SVG touch targets: use fill='transparent' rect as first child of interactive g element"
  - "Touch hover clearing: add both onTouchEnd and onTouchCancel to clear hover state"

requirements-completed: [HOSP-01, HOSP-02, HOSP-03]

# Metrics
duration: 5min
completed: 2026-03-24
---

# Phase 3 Plan 01: Hospital Scene Layout Summary

**Centered four SVG person figures in their rooms with corrected x/y coordinates and added 12x12 transparent touch target rects with onTouchEnd/onTouchCancel handlers**

## Performance

- **Duration:** 5 min
- **Started:** 2026-03-24T21:57:00Z
- **Completed:** 2026-03-24T22:02:17Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments
- Corrected x/y swap: patient-one moved from x:60 to x:72 (insurance room center), patient-two moved from x:72 to x:60 (maternal room center)
- Shifted all four figure y-values down by 2 SVG units (36->38, 69->71) for 2.65 SVG unit clearance below metricLabel text baseline at dept_y+12
- Added 12x12 SVG unit `fill="transparent"` touch target rects inside each person `<g>`, producing 48.96px tap area at 768px viewport
- Added `onTouchEnd`/`onTouchCancel` to person and department `<g>` elements to clear sticky hover state on touch screens

## Task Commits

Each task was committed atomically:

1. **Task 1: Correct HOSPITAL_PEOPLE x/y coordinates** - `23299c6` (fix)
2. **Task 2: Add touch targets and touch event handlers** - `bb572a5` (feat)

## Files Created/Modified
- `src/App.jsx` - Corrected HOSPITAL_PEOPLE coordinates and added touch infrastructure in HospitalScene

## Decisions Made
- `fill="transparent"` used for touch target rects (not `fill="none"`) — SVG hit testing only works on painted fills; transparent is painted, none is not
- Touch target rect is first child of each person `<g>` so it receives pointer events before visual elements
- `onTouchEnd` + `onTouchCancel` both added because a touch can end in two ways; both must clear hover to avoid sticky state

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
None.

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Hospital scene layout is complete: all figures centered in rooms, no text overlap, touch targets in place
- Phase 4 (content/real patient stories) can proceed without layout concerns
- The sticky hover concern from STATE.md blockers is now resolved for HospitalScene

---
*Phase: 03-hospital-scene-layout*
*Completed: 2026-03-24*
