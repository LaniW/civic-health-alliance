---
phase: 03-hospital-scene-layout
verified: 2026-03-24T22:30:00Z
status: passed
score: 3/3 must-haves verified
re_verification: false
---

# Phase 3: Hospital Scene Layout Verification Report

**Phase Goal:** The hospital scene is visually clean — person figures sit inside their rooms, text is readable, and every figure is tappable on touch screens
**Verified:** 2026-03-24T22:30:00Z
**Status:** passed
**Re-verification:** No — initial verification

---

## Goal Achievement

### Observable Truths

| # | Truth | Status | Evidence |
|---|-------|--------|----------|
| 1 | Every person figure is visually centered within its assigned department room | VERIFIED | All four x-values match room center_x exactly: dr-saffo x=29 (hivid 14+30/2=29), patient-one x=72 (insurance 56+32/2=72), patient-two x=60 (maternal 46+28/2=60), patient-three x=30 (casemanagement 18+24/2=30) |
| 2 | No text overlaps between department labels/metrics and person figures | VERIFIED | All four y-values place figure heads 2.65 SVG units below metricLabel baseline: upper rooms person_y=38, head_top=36.65, metricLabel_baseline=34, clearance=2.65; lower rooms person_y=71, head_top=69.65, metricLabel_baseline=67, clearance=2.65 |
| 3 | Every person figure has a 48.96px+ touch target at 768px viewport | VERIFIED | One `<rect x={p.x-6} y={p.y-2} width={12} height={12} fill="transparent" style={{cursor:"pointer"}}/>` exists as first child of each person `<g>` in HOSPITAL_PEOPLE.map at src/App.jsx:565; 12 * 4.08 px/unit = 48.96px at minimum 768px viewport |

**Score:** 3/3 truths verified

---

### Required Artifacts

| Artifact | Expected | Status | Details |
|----------|----------|--------|---------|
| `src/App.jsx` | Corrected HOSPITAL_PEOPLE coordinates and touch target rects in HospitalScene | VERIFIED | File exists, substantive (755 lines), coordinate corrections present at lines 208/223/238/253, touch rect at line 565 |

**Artifact check — contains pattern `x:72,y:38`:** Matched at line 223 (patient-one, the swapped figure).

---

### Key Link Verification

| From | To | Via | Status | Details |
|------|----|-----|--------|---------|
| HOSPITAL_PEOPLE x/y values | PersonFig anchor position in HospitalScene | p.x and p.y passed as props | VERIFIED | `src/App.jsx:566`: `<PersonFig x={p.x} y={p.y} color={p.color} variant={...}/>` — p.x and p.y flow directly from data to render |
| HOSPITAL_PEOPLE x/y values | Invisible touch rect position in HospitalScene | rect x={p.x-6} y={p.y-2} | VERIFIED | `src/App.jsx:565`: `<rect x={p.x-6} y={p.y-2} width={12} height={12} fill="transparent" .../>` — exactly matches the specified pattern |
| Touch rect fill="transparent" | SVG pointer event hit area | SVG hit testing on fill paint | VERIFIED | `fill="transparent"` used (not `fill="none"`); confirmed at line 565. No `fill="none"` instances on touch target rects — the four other `fill="none"` occurrences are on map polygon outlines and SVG stroke-only paths, not touch targets |

---

### Data-Flow Trace (Level 4)

Not applicable. This phase modifies SVG coordinate data constants and adds interactive SVG geometry. There is no async data source; all data is inline JavaScript object literals. The "data flow" is the direct reference from `HOSPITAL_PEOPLE[i].x` / `.y` to JSX props — verified in key links above.

---

### Behavioral Spot-Checks

| Behavior | Command | Result | Status |
|----------|---------|--------|--------|
| Production build succeeds with no errors | `npm run build` | 29 modules transformed, built in 507ms, exit 0 | PASS |
| Lint passes with no warnings or errors | `npm run lint` | No output (clean), exit 0 | PASS |
| Task commits exist in git history | `git log --oneline 23299c6 bb572a5` | Both commits present | PASS |
| Old wrong coordinates absent | `grep 'x:60,y:36\|x:72,y:69' src/App.jsx` | 0 matches | PASS |
| All four corrected coordinates present | `grep 'x:29,y:38\|x:72,y:38\|x:60,y:71\|x:30,y:71'` | 4 matches, one per person | PASS |
| Touch target rect template exists | `grep 'width={12} height={12}'` | 1 match at line 565 | PASS |
| onTouchEnd on person g | `grep 'onTouchEnd.*onHoverPerson(null)'` | Line 564 | PASS |
| onTouchEnd on department g | `grep 'onTouchEnd.*onHoverDept(null)'` | Line 552 | PASS |
| onTouchCancel on person g | `grep 'onTouchCancel.*onHoverPerson(null)'` | Line 564 | PASS |
| onTouchCancel on department g | `grep 'onTouchCancel.*onHoverDept(null)'` | Line 552 | PASS |

---

### Requirements Coverage

| Requirement | Source Plan | Description | Status | Evidence |
|-------------|-------------|-------------|--------|----------|
| HOSP-01 | 03-01-PLAN.md | Person figures are visually centered within their department rooms | SATISFIED | All four x-values match room center_x exactly (math verified programmatically) |
| HOSP-02 | 03-01-PLAN.md | No text overlaps in department labels, wait times, or room notes | SATISFIED | 2.65 SVG unit clearance between metricLabel baseline (dept_y+12) and figure head top (person_y-1.35) in all four rooms |
| HOSP-03 | 03-01-PLAN.md | Person figures have touch-friendly tap targets (44px+ equivalent in SVG units) | SATISFIED | 12x12 SVG unit `fill="transparent"` rect produces 48.96px at 768px minimum viewport (12 * 4.08 px/unit) |

**Orphaned requirements check:** REQUIREMENTS.md maps HOSP-01, HOSP-02, HOSP-03 to Phase 3 — all three appear in the plan's `requirements` field. No orphaned requirements.

---

### Anti-Patterns Found

| File | Line | Pattern | Severity | Impact |
|------|------|---------|----------|--------|
| — | — | — | — | — |

No TODO/FIXME/PLACEHOLDER comments found. No `return null` stubs. No `fill="none"` on touch targets (correctly uses `fill="transparent"`). No console.log statements. No hardcoded empty arrays/objects flowing to rendering. Build and lint both clean.

---

### Human Verification Required

#### 1. Visual centering on actual device

**Test:** Open the app at level 2 (hospital scene) on a tablet or desktop browser at 768px width. Visually inspect whether each of the four person figures appears to sit centered within its room box.
**Expected:** Each figure is horizontally centered in its room, with clear space above the figure's head (below the metric label text) and clear space at the figure's feet (above the room note text).
**Why human:** SVG coordinate math has been verified programmatically, but visual perception of "centered" involves rendered glyph bounds of the PersonFig component that cannot be confirmed without a browser render.

#### 2. Touch target feel on a touch device

**Test:** On a physical touch device (phone or tablet), navigate to the hospital scene and attempt to tap each of the four person figures. Especially try tapping the edges of each figure rather than the center.
**Expected:** Every tap on or near a figure (within approximately 44px of the figure center) triggers navigation to that person's story. No figure requires precise tap accuracy.
**Why human:** SVG pointer event hit testing with `fill="transparent"` rects is confirmed in code, but actual touch responsiveness on different browsers and OS versions (iOS Safari, Android Chrome) requires device testing.

#### 3. Hover tooltip stays in viewBox

**Test:** Mouse over each of the four person figures and confirm the hover tooltip (dark pill showing name and role) is fully visible and not clipped.
**Expected:** All four tooltips render fully within the SVG viewport — no clipping at any edge.
**Why human:** Tooltip bounds were verified mathematically against the viewBox (0 0 100 96) and all pass, but actual browser rendering of SVG text overflow and overflow clipping requires visual confirmation.

---

### Gaps Summary

No gaps. All three observable truths are verified at all levels (exists, substantive, wired). Both task commits are present in git history. Build and lint pass cleanly. Requirements HOSP-01, HOSP-02, and HOSP-03 are all satisfied with concrete implementation evidence.

The phase delivered exactly what was planned:
- Two x-values swapped (patient-one 60→72, patient-two 72→60) to match room centers
- All four y-values shifted down by 2 SVG units (36→38, 69→71) to create 2.65 unit text clearance
- One 12x12 `fill="transparent"` touch rect added per person figure as first child of its `<g>`
- `onTouchEnd` and `onTouchCancel` handlers added to both person and department `<g>` elements

Three human verification items are noted for visual and device confirmation but do not block goal achievement — the code is correct.

---

_Verified: 2026-03-24T22:30:00Z_
_Verifier: Claude (gsd-verifier)_
