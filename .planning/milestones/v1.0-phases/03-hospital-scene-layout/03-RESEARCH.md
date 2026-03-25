# Phase 3: Hospital Scene Layout — Research

**Researched:** 2026-03-24
**Domain:** SVG layout, touch target sizing, inline-styled React components
**Confidence:** HIGH — all findings derived from direct source code analysis of `src/App.jsx`

---

## Summary

Phase 3 addresses three concrete visual and interaction problems in the `HospitalScene` SVG component. All three problems were diagnosed by measuring coordinates directly in the source code — no assumptions. The root causes are:

1. **Centering (HOSP-01):** Two of the four person figures (`patient-one` at x=60 and `patient-two` at x=72) have their x-coordinates swapped relative to their assigned department rooms. The other two figures are already correctly centered on their room's horizontal midpoint.

2. **Text overlap (HOSP-02):** All four figures are positioned 2 SVG units too high (y=36 instead of y=38 for upper rooms, y=69 instead of y=71 for lower rooms). This places the figure's head within 0.65 SVG units of the `metricLabel` text line, causing visual overlap at any render scale.

3. **Touch targets (HOSP-03):** The clickable hit area for each figure is determined by its `<g>` element's visual bounds — approximately 5.85 SVG units wide. At the minimum supported viewport (768px, yielding ~4.08px per SVG unit), this produces a ~24px touch width, well below the 44px minimum. An invisible `<rect fill="transparent">` must be added as a 12×12 SVG unit hit target.

**Primary recommendation:** Fix the three issues with minimal surgical changes to `HOSPITAL_PEOPLE` x/y values and one invisible rect per figure in `HospitalScene`. No structural refactoring required.

---

## Project Constraints (from CLAUDE.md)

- **Tech stack**: React + Vite, no additional dependencies — keep it minimal
- **Single file**: All changes go in `src/App.jsx`
- **Inline styles exclusively** — no CSS classes on components, all `style={{}}` objects
- **Color constants**: Use `RED`, `CREAM`, `CREAM2` from top of `App.jsx`
- `fill="transparent"` (not `fill="none"`) for invisible SVG elements that must receive pointer events
- No `console.log`, `console.error`, or `console.warn` in the codebase
- No Prettier or formatter; match existing code style (compact, semicolons, double quotes in JSX)

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| HOSP-01 | Person figures are visually centered within their department rooms | Coordinate analysis identifies exact x/y corrections: patient-one x: 60→72, patient-two x: 72→60, all four y values shift up 2 SVG units |
| HOSP-02 | No text overlaps in department labels, wait times, or room notes | Vertical coordinate analysis shows all figures must move y: 36→38 (upper rooms) and y: 69→71 (lower rooms) to achieve 2.65 SVG unit clearance from metricLabel text |
| HOSP-03 | Person figures have touch-friendly tap targets (44px+ equivalent in SVG units) | Touch target analysis shows 12×12 SVG unit transparent `<rect>` achieves 48.96px at 768px viewport minimum |
</phase_requirements>

---

## Standard Stack

No new libraries. This phase is entirely within the existing stack.

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| React | 19.2.4 | Component rendering | Already installed |
| Vite | 7.3.1 | Dev server / build | Already installed |

**Installation:** None required.

---

## Architecture Patterns

### Relevant Project Structure

```
src/
└── App.jsx          # Single file — all changes here
    ├── HOSPITAL_PEOPLE[]   # lines 203-264 — person x,y coordinates
    ├── DEPARTMENTS[]       # lines 266-271 — room geometry
    ├── PersonFig()         # lines 498-519 — figure renderer (no change)
    └── HospitalScene()     # lines 525-578 — scene assembler (touch rect added here)
```

### Pattern 1: SVG Coordinate System

**What:** `HospitalScene` uses `viewBox="0 0 100 96"`. All positions are in SVG user units. The SVG scales to fill the visualization area (`width:"100%", height:"100%"`).

**Scale computation:**
- LeftPanel fixed at 360px. Visualization area = viewport width − 360px.
- At 768px viewport: (768−360)/100 = **4.08 px/SVG unit** (minimum supported)
- At 1024px viewport: (1024−360)/100 = **6.64 px/SVG unit**
- At 1280px viewport: (1280−360)/100 = **9.2 px/SVG unit**

**When to use:** All size/position decisions must be verified against the 4.08 px/unit minimum scale.

### Pattern 2: PersonFig Bounding Box

`PersonFig` renders via `translate(x,y) scale(0.45)`. The local coordinate extents after scaling:

| Extent | Local coord | × 0.45 | SVG units from anchor |
|--------|-------------|--------|-----------------------|
| Head top | y=−3 | | anchor − 1.35 |
| Head bottom | y=+3 | | anchor + 1.35 |
| Torso bottom | y=+14 | | anchor + 6.30 |
| Feet bottom | y=+20 | | anchor + 9.00 |
| Arm left | x=−6.5 | | anchor − 2.93 |
| Arm right | x=+6.5 | | anchor + 2.93 |

**Figure bounding box (relative to anchor point):**
- Width: 5.85 SVG units (−2.93 to +2.93)
- Height: 10.35 SVG units (−1.35 to +9.00)
- Visual center of figure body: anchor + 3.83 (midpoint of −1.35 to +9.00)

### Pattern 3: Department Room Geometry

```
Room layout (all rooms, h=28):
  y + 0       : room top edge (rect boundary)
  y + 4.5     : department label (fontSize 2.2, Libre Franklin 700)
  y + 9       : metric value (fontSize 3.2, JetBrains Mono 700)
  y + 12      : metric label (fontSize 1.5, JetBrains Mono 400)
  y + 14 ..   : <<< safe zone for person figure starts here >>>
  y + 25.5 .. : <<< safe zone for person figure ends here >>>
  y + 25.5    : note text baseline (fontSize 1.6, JetBrains Mono 400)
  y + 28      : room bottom edge (rect boundary)
```

**Safe zone for person figure:** `dept_y + 14` to `dept_y + 25.5` = 11.5 SVG units.

The figure (10.35 SVG units tall) fits in this zone with clearance. Optimal anchor y:
- Center of safe zone: `dept_y + 19.75`
- Minus figure visual offset: `dept_y + 19.75 − 3.83 = dept_y + 15.92 ≈ dept_y + 16`

This yields:
- Upper rooms (dept_y=22): person y = **38**
- Lower rooms (dept_y=55): person y = **71**

### Pattern 4: Invisible SVG Touch Target Rect

**What:** Add a transparent `<rect>` inside each person's `<g>` before the `PersonFig` and `Pulse` elements. The rect receives pointer events because `fill="transparent"` participates in hit testing, while `fill="none"` does not.

**Critical distinction:**
```jsx
// WRONG — does not receive pointer events
<rect fill="none" ... />

// CORRECT — receives pointer events, visually invisible
<rect fill="transparent" ... />
```

**Sizing:** 12×12 SVG units centered on figure body.

```jsx
// Source: src/App.jsx — add inside person <g> before PersonFig
<rect
  x={p.x - 6}
  y={p.y - 2}
  width={12}
  height={12}
  fill="transparent"
  style={{cursor:"pointer"}}
/>
```

At minimum scale (4.08 px/SVG unit): 12 × 4.08 = **48.96px** ✓ (exceeds 44px minimum)

### Anti-Patterns to Avoid

- **Changing `PersonFig` geometry:** The component renders correctly. Only x/y anchor values in `HOSPITAL_PEOPLE` need adjustment.
- **Using `fill="none"` for touch targets:** Does not receive pointer events in SVG. Always use `fill="transparent"` for invisible hit areas.
- **Adjusting room text positions to fix overlap:** The text layout is correct — only the person figure y-coordinates are wrong. Move figures, not text.
- **Increasing figure scale:** Would change the visual weight of the figures and risk new overlaps with room borders.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Touch target expansion | Custom JS hit-detection, event delegation, or coordinate math | Invisible `<rect fill="transparent">` | Native SVG hit testing, zero JS overhead, works with existing onMouseEnter/onTouchEnd handlers |
| Figure centering | Dynamic centering algorithm | Direct numeric x/y corrections in HOSPITAL_PEOPLE | Two figures have hardcoded wrong values; direct fix is safer and matches the existing pattern |

**Key insight:** SVG's built-in hit testing with `fill="transparent"` is the standard approach for enlarging click/touch targets on small SVG shapes. No JS geometry computation needed.

---

## Common Pitfalls

### Pitfall 1: fill="none" Does Not Receive Pointer Events

**What goes wrong:** Developer adds `<rect fill="none">` expecting it to catch touch events. The rect is invisible, but touches pass through it and hit nothing (or the background).

**Why it happens:** SVG hit testing is fill-based by default. `fill="none"` means no fill paint, which means no hit area. `fill="transparent"` paints with a fully transparent color, which creates a hit area.

**How to avoid:** Always use `fill="transparent"` (not `fill="none"`) for invisible SVG touch/click targets.

**Warning signs:** Tap on empty area around figure does nothing; pointer events only fire directly on visible figure strokes.

### Pitfall 2: Hover Tooltip Overflow

**What goes wrong:** The hover tooltip rendered when `hoveredPerson===p.id` is positioned at `p.x-18, p.y-16`. After moving person y from 36→38, the tooltip top edge moves from y=20 to y=22 — still inside the SVG viewBox, no issue. But if x positions change (patient-one x: 60→72), the tooltip rect `x=p.x-18=54` to `p.x+18=90` — still inside viewBox (0-100). Safe.

**How to avoid:** Verify tooltip bounding box is within `0 0 100 96` viewBox after any coordinate changes.

### Pitfall 3: Touch Target Overlap Between Adjacent Figures

**What goes wrong:** 12×12 touch rects for adjacent figures overlap, causing the wrong person to be tapped.

**Why it happens:** If two figures share a room and their x-coordinates are close, their 12-unit-wide touch rects (x±6) can intersect.

**How to verify:** Check minimum x-distance between figures. In this codebase all four figures are in separate rooms, so closest approach is patient-one at x=72 (insurance) vs. dr-saffo at x=29 (hivid) — no overlap possible.

### Pitfall 4: SVG Text Baseline Behavior

**What goes wrong:** SVG `<text>` y attribute is the text baseline (bottom of most capital letters), not the top. A font of size 1.5 has approximately 1.5 SVG units of ascender height above baseline. When checking clearance between text and figure, the figure top must be compared against `text_y + 0` (baseline), not `text_y + fontSize`.

**How to avoid:** Add 1-2 SVG units of clearance above the figure's top edge beyond the text baseline. The recommended y=38/71 provides 2.65 SVG units between metricLabel baseline (34/67) and figure top (36.65/69.65) — sufficient.

---

## Code Examples

### HOSP-01 + HOSP-02: Corrected HOSPITAL_PEOPLE Coordinates

```jsx
// Source: src/App.jsx lines 203-264 — HOSPITAL_PEOPLE data constant
// Changes: patient-one x: 60→72, patient-two x: 72→60
//          all y values: upper rooms 36→38, lower rooms 69→71

const HOSPITAL_PEOPLE=[
  {id:"dr-saffo",    x:29, y:38, ...},  // hivid center_x=29 ✓, y adjusted 36→38
  {id:"patient-one", x:72, y:38, ...},  // insurance center_x=72, x fixed 60→72, y adjusted
  {id:"patient-two", x:60, y:71, ...},  // maternal center_x=60, x fixed 72→60, y adjusted
  {id:"patient-three",x:30,y:71, ...},  // casemanagement center_x=30 ✓, y adjusted 69→71
];
```

**Verification table:**

| Person | Room | Room center_x | Old x | New x | Room dept_y | Old y | New y |
|--------|------|--------------|-------|-------|-------------|-------|-------|
| dr-saffo | hivid | 29 | 29 | 29 | 22 | 36 | 38 |
| patient-one | insurance | 72 | 60 | 72 | 22 | 36 | 38 |
| patient-two | maternal | 60 | 72 | 60 | 55 | 69 | 71 |
| patient-three | casemanagement | 30 | 30 | 30 | 55 | 69 | 71 |

### HOSP-03: Invisible Touch Target Rect

```jsx
// Source: src/App.jsx lines 563-573 — person rendering loop in HospitalScene
// Add <rect fill="transparent"> inside each person <g>, before PersonFig and Pulse

{HOSPITAL_PEOPLE.map(p=>(
  <g key={p.id}
     onMouseEnter={()=>onHoverPerson(p.id)}
     onMouseLeave={()=>onHoverPerson(null)}
     onTouchEnd={()=>onHoverPerson(null)}
     onTouchCancel={()=>onHoverPerson(null)}
     onClick={()=>onClickPerson(p.id)}
     style={{cursor:"pointer"}}>
    <rect x={p.x-6} y={p.y-2} width={12} height={12} fill="transparent" style={{cursor:"pointer"}}/>
    <PersonFig x={p.x} y={p.y} color={p.color} variant={p.id==="dr-saffo"?"doctor":"adult"}/>
    <Pulse cx={p.x} cy={p.y-3} r={0.8} color={p.color}/>
    {hoveredPerson===p.id&&<g>
      <rect x={p.x-18} y={p.y-16} width={36} height={8} rx={1} fill="rgba(26,26,26,0.93)" stroke={p.color} strokeWidth={0.25}/>
      <text x={p.x} y={p.y-12} textAnchor="middle" style={{fontSize:2.2,fontFamily:"'Libre Franklin',sans-serif",fill:"#fff",fontWeight:700}}>{p.name}</text>
      <text x={p.x} y={p.y-9.5} textAnchor="middle" style={{fontSize:1.6,fontFamily:"'JetBrains Mono',monospace",fill:"#d1d5db"}}>{p.role}</text>
    </g>}
  </g>
))}
```

Note: `onTouchEnd` and `onTouchCancel` are already required by Phase 2 (UI-SPEC.md line 229) for the sticky hover fix. If Phase 2 has been executed before Phase 3, these handlers are already present.

### Clearance Verification After Coordinate Changes

```
After y: 36→38 (upper rooms, dept_y=22):
  metricLabel baseline: dept_y + 12 = 34
  figure head top:      person_y - 1.35 = 36.65
  clearance: 36.65 - 34 = 2.65 SVG units ✓

  figure feet bottom:   person_y + 9 = 47
  note baseline:        dept_y + h - 2.5 = 47.5
  clearance: 47.5 - 47 = 0.5 SVG units (tight, ok — note renders below figure)

After y: 69→71 (lower rooms, dept_y=55):
  metricLabel baseline: dept_y + 12 = 67
  figure head top:      person_y - 1.35 = 69.65
  clearance: 69.65 - 67 = 2.65 SVG units ✓

  figure feet bottom:   person_y + 9 = 80
  note baseline:        dept_y + h - 2.5 = 80.5
  clearance: 80.5 - 80 = 0.5 SVG units (tight, ok)
```

---

## Environment Availability

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| Node.js | `npm run dev`, `npm run build` | ✓ | v25.8.1 | — |
| npm | Package management | ✓ | 11.12.0 | — |
| Vite | Dev server | ✓ | 7.3.1 (installed) | — |
| React | Component rendering | ✓ | 19.2.4 (installed) | — |

No missing dependencies. This phase requires no new installs.

---

## State of the Art

| Old Approach | Current Approach | Impact |
|--------------|------------------|--------|
| Hard-coded x/y in HOSPITAL_PEOPLE | Same — intentional for this project | Corrections are single-constant edits |
| SVG pointer-events on visible elements only | Add invisible `<rect fill="transparent">` for touch expansion | Standard SVG technique, no dependencies |

---

## Open Questions

1. **Phase 2 execution status**
   - What we know: STATE.md says "Phase 2 UI-SPEC approved, Plan: Not started"
   - What's unclear: Whether Phase 2 will be executed before Phase 3 (the roadmap says they can run in parallel)
   - Recommendation: Phase 3 plan must not assume `onTouchEnd`/`onTouchCancel` handlers are already present on the person `<g>` elements. Include them explicitly in the Phase 3 task. If Phase 2 has already added them, the executor will see the duplication and skip.

2. **Hover tooltip position with updated y=38/71**
   - What we know: Tooltip rect is at `p.y-16` to `p.y-8`. After y: 36→38, tooltip top = 38-16=22 (inside viewBox). After patient-two x: 72→60, tooltip range = 42..78 (inside viewBox 0-100).
   - What's unclear: Nothing — verified safe, no issue.
   - Recommendation: No action needed.

---

## Sources

### Primary (HIGH confidence)
- `src/App.jsx` lines 203-271 — `HOSPITAL_PEOPLE` and `DEPARTMENTS` data constants, directly measured
- `src/App.jsx` lines 495-578 — `PersonFig` and `HospitalScene` component implementations, directly measured
- `src/App.jsx` lines 525-527 — viewBox `"0 0 100 96"` confirmed
- MDN SVG `fill` property — `fill="transparent"` vs `fill="none"` pointer event behavior (HIGH confidence from SVG specification; `fill="none"` means no fill paint, `fill="transparent"` paints transparent, both visually invisible but only the latter creates a hit area)
- `.planning/phases/02-touch-navigation/02-UI-SPEC.md` — touch target standard (44px minimum), onTouchEnd requirement

### Secondary (MEDIUM confidence)
- Apple HIG, Google Material Design, WCAG 2.5.8 — all specify 44px as minimum touch target size (referenced via Phase 2 UI-SPEC)

---

## Metadata

**Confidence breakdown:**
- Coordinate analysis (HOSP-01, HOSP-02): HIGH — directly measured from source code, no external dependencies
- Touch target sizing (HOSP-03): HIGH — SVG `fill="transparent"` is a well-established pattern; pixel math derived from measured viewBox and known LeftPanel width
- SVG hit-testing behavior: HIGH — defined by SVG specification

**Research date:** 2026-03-24
**Valid until:** Stable — findings are pure geometry derived from source code. Only invalidated if `DEPARTMENTS` room geometry or `PersonFig` scale changes.
