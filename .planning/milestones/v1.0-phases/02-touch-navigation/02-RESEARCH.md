# Phase 02: Touch Navigation - Research

**Researched:** 2026-03-24
**Domain:** Touch event handling, CSS touch-action, swipe gestures, breadcrumb navigation
**Confidence:** HIGH

## Summary

This phase adds full touch navigation to the 4-level drill-down visualization. Every requirement (NAV-01 through NAV-04) is pure front-end work confined to `src/App.jsx`, consistent with the project's single-file constraint. No new dependencies are needed. The browser's native touch event API (`onTouchStart`, `onTouchEnd`, `onTouchCancel`) is the correct tool for all four requirements.

The 300ms tap delay (NAV-03) is eliminated with one CSS property: `touch-action: manipulation`. This is applied as an inline style (consistent with the project's inline-styles-only convention). The existing `onMouseEnter`/`onMouseLeave` hover pattern on SVG elements causes sticky hover on touch screens because `onMouseLeave` does not fire reliably after a tap. The fix is adding `onTouchEnd` and `onTouchCancel` handlers that call the same hover-clear function (`onHover(null)`, `setHovState(null)`, etc.).

Swipe gesture detection (NAV-04) is implemented by storing `touchstart` X coordinates in `useRef` on the App container and comparing to `touchend` X coordinates; a horizontal delta exceeding a threshold (e.g., 50px) triggers `zoomTo(level-1)` (swipe right = back) or the appropriate forward action when allowed. No `useEffect` is needed. The back arrow button (NAV-01) is already structurally present but must have its touch target enlarged to 44px minimum. Breadcrumb segments (NAV-02) need `onClick` handlers wired to `zoomTo(targetLevel)` with a guard that only navigates backward.

**Primary recommendation:** Apply `touch-action: manipulation` globally on the root `<div>`, add `onTouchEnd`/`onTouchCancel` wherever `onMouseLeave` clears hover state, add swipe detection to the App root container using `useRef`, and upgrade NavBar breadcrumbs to be tappable buttons with 44px+ touch targets.

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| NAV-01 | User can tap a back arrow in the NavBar to return to the previous level (44px+ touch target) | Existing Back button in LeftPanel is ~40px; NavBar has no back button. Must add one to NavBar or ensure LeftPanel button meets 44px. Padding upgrade pattern documented below. |
| NAV-02 | User can tap any breadcrumb segment to jump directly to that level (backward-only navigation) | Current NavBar breadcrumbs are `<span>` elements — not interactive. Must convert to `<button>` with `onClick={() => zoomTo(targetLevel)}` guarded to `targetLevel < level`. |
| NAV-03 | All interactive elements use `touch-action: manipulation` to eliminate 300ms tap delay | Single inline style property. Apply to root container and confirmed sufficient by MDN (widely supported since 2019). |
| NAV-04 | User can swipe left/right to navigate between levels | `onTouchStart`/`onTouchEnd` on App root `<div>`. useRef for touch coordinates. Threshold ~50px. Swipe right = back, swipe left = forward (when valid). No library needed. |
</phase_requirements>

---

## Project Constraints (from CLAUDE.md)

All directives from CLAUDE.md that constrain this phase's implementation:

- **Single file:** All changes go in `src/App.jsx` only.
- **No additional dependencies:** React + Vite only. No gesture libraries (hammerjs, use-gesture, etc.).
- **Inline styles exclusively:** `touch-action`, touch target sizes, and all layout changes must be inline `style={{}}` objects — no CSS classes.
- **No test framework configured:** No automated tests to write or run.
- **No `useEffect`, `useRef`, `useMemo`, `useReducer`, `useContext` currently used:** The plan can introduce `useRef` (it is a standard hook; the project simply hasn't needed it yet). This is the correct tool for storing touch coordinates without triggering re-renders.
- **`useCallback` wraps `zoomTo` with `[]` dependency array:** Any new callback that calls `zoomTo` should also be wrapped in `useCallback` or defined inline (since `zoomTo` is already memoized, calling it from an inline handler is fine).
- **Naming conventions:** New state variables use camelCase; event props use `on` prefix. Touch coordinate refs can be `touchStartX` (camelCase).
- **44px+ touch targets:** Minimum for all interactive navigation elements.
- **No `console.log`/`console.error`:** No logging in implementation.

---

## Standard Stack

### Core (no new packages)

| Technique | Source | Purpose | Why Standard |
|-----------|--------|---------|--------------|
| `touch-action: manipulation` | CSS / MDN | Eliminates 300ms double-tap delay on all interactive elements | Baseline widely available since 2019; zero-cost CSS-only fix |
| `onTouchStart` / `onTouchEnd` / `onTouchCancel` | React synthetic events | Swipe detection; clearing sticky hover | Native browser API; no library |
| `useRef` | React built-in | Store touch coordinates between events without re-render | Correct hook for mutable values that don't affect rendering |
| `@media (hover: hover)` | CSS media query | Restrict hover effects to pointer devices | Modern standard for touch/mouse differentiation |

### Installation

No new packages needed.

---

## Architecture Patterns

### NAV-03: Eliminate 300ms Delay

Apply `touch-action: manipulation` to the outermost root `<div>` in `App`. This cascades to all children because pointer events descend. The CLAUDE.md inline-style convention means this goes on the root `<div>`:

```jsx
// src/App.jsx — root div in App()
<div style={{
  width:"100vw", height:"100vh", overflow:"hidden",
  background:CREAM, position:"relative",
  touchAction:"manipulation",   // ← add this
}}>
```

React's camelCase style property for `touch-action` is `touchAction`.

**Confidence:** HIGH — MDN confirms property name; React inline style camelCase is standard.

---

### NAV-01: Back Arrow with 44px+ Touch Target

The existing Back button in `LeftPanel` uses `padding:"10px 20px"`. With `fontSize:12`, the computed height is approximately `12 * 1.5 + 20 = 38px`. This is below the 44px minimum.

Fix: increase vertical padding to `"14px 20px"` → computed height `12 * 1.5 + 28 = 46px`. Alternatively, add an explicit `minHeight:44` inline style.

The `NavBar` currently has **no back arrow**. NAV-01 says "in the NavBar". Two options:

1. **Add back arrow to NavBar** — consistent with NAV-01 wording. A dedicated `←` button in the NavBar black bar, visible whenever `level > 0`, calls `zoomTo(level - 1, null)`. This is the most literal reading of the requirement.
2. **Keep back in LeftPanel, upgrade size** — LeftPanel is hidden at level 3 (PersonStory), so this is incomplete coverage.

**Recommendation:** Add back arrow to NavBar. It is the persistent chrome present at all levels > 0, including level 3. LeftPanel's Back button can remain as-is (it provides the same function at levels 1-2, and the requirement says NavBar specifically).

```jsx
// Inside NavBar return, before the brand button or after it
{level > 0 && (
  <button
    onClick={() => zoomTo(level - 1, null)}
    style={{
      background: "none", border: "none",
      color: "#f5f5f5", cursor: "pointer",
      fontFamily: "'Libre Franklin',sans-serif",
      fontSize: 20, fontWeight: 700,
      minWidth: 44, minHeight: 44,
      display: "flex", alignItems: "center", justifyContent: "center",
      marginRight: 8,
      touchAction: "manipulation",
    }}
    aria-label="Go back"
  >&#8592;</button>
)}
```

`NavBar` receives `level` already. It needs `onBack` prop added (or access to `zoomTo` directly — since NavBar is module-private and `zoomTo` is passed as a prop pattern in this file, add `onBack` prop).

**Confidence:** HIGH — this is direct DOM manipulation with existing patterns.

---

### NAV-02: Tappable Breadcrumbs (Backward-Only)

Current breadcrumb segments at level 2 and 3 are `<span>` elements — non-interactive. Convert them to `<button>` elements. Guard with `targetLevel < level` (backward-only).

Level map for breadcrumbs:
- "United States Map" button → always navigates to level 0 (already interactive via `onGoUS`)
- "New York City Map" button → navigates to level 1 (already interactive via `onGoNYC`)
- "Health Center" breadcrumb span → navigates to level 2 (currently non-interactive)
- "Story" breadcrumb span → current level; non-navigable (correct — can't jump to self)

So the only change is making the "Health Center" span at level 3 tappable (navigates to level 2):

```jsx
// level===3 case in NavBar
{level===3&&(
  <span style={{display:"flex",alignItems:"center",gap:6,marginLeft:4}}>
    <span style={{color:"rgba(255,255,255,0.3)",fontSize:14}}>›</span>
    <button
      onClick={()=>zoomTo(2,null)}
      style={{
        background:"none", border:"none", cursor:"pointer",
        fontFamily:"'Libre Franklin',sans-serif",
        fontSize:13, fontStyle:"italic", color:"#f5f5f5", fontWeight:600,
        padding:"0 4px", minHeight:44,
        touchAction:"manipulation",
      }}
    >Health Center</button>
    <span style={{color:"rgba(255,255,255,0.3)",fontSize:14}}>›</span>
    <span style={{fontFamily:"'Libre Franklin',sans-serif",fontSize:13,fontStyle:"italic",color:RED,fontWeight:600}}>Story</span>
  </span>
)}
```

The NavBar needs `zoomTo` (or a prop) threaded through. Currently NavBar accepts `onGoUS` and `onGoNYC`; add `onBack` and `onGoHospital` props, or pass a generic `onNavigate(level)` callback.

**Recommendation:** Add a single `onGoLevel` prop to NavBar: `onGoLevel={(n)=>zoomTo(n,null)}`. This handles both NAV-01 and NAV-02 from a single prop, and avoids threading 4 separate callbacks.

**Confidence:** HIGH — straightforward prop threading; existing pattern in codebase.

---

### NAV-04: Swipe Left/Right Navigation

The App root div wraps all level views. Add `onTouchStart` and `onTouchEnd` there for global swipe detection.

Store the start X in a `useRef` (not `useState`) so the value updates without triggering a re-render.

```jsx
// In App() — add this import and ref
import { useState, useCallback, useRef } from "react";

// Inside App():
const touchStartX = useRef(null);

const handleTouchStart = useCallback((e) => {
  touchStartX.current = e.touches[0].clientX;
}, []);

const handleTouchEnd = useCallback((e) => {
  if(touchStartX.current === null) return;
  const delta = e.changedTouches[0].clientX - touchStartX.current;
  touchStartX.current = null;
  const THRESHOLD = 50;
  if(delta > THRESHOLD) {
    // swipe right = back
    if(level > -1) zoomTo(level - 1, null);
  } else if(delta < -THRESHOLD) {
    // swipe left = forward (only when a logical next level exists)
    // level 0→1, 1→2 are covered; level 2→3 requires a person selection,
    // so swipe-left at level 2 should be a no-op (person must be explicitly tapped)
    if(level >= 0 && level < 2) zoomTo(level + 1, null);
  }
}, [level, zoomTo]);
```

Wire onto root div:
```jsx
<div
  style={{...}}
  onTouchStart={handleTouchStart}
  onTouchEnd={handleTouchEnd}
>
```

**Edge cases to handle:**
- Swipe at level -1 (title screen): swipe right → no-op (level -1 is the floor). Guard with `level > 0` for going back, or allow `level > -1`.
- Swipe at level 3 (story): swipe right → back to level 2 (valid).
- Swipe left at level 2 → no-op (can't navigate to level 3 without a person; forward is tap-only).
- Swipe left at level 3 → no-op (already deepest).
- Phase animation in progress: if `phase !== "idle"`, ignore the swipe to prevent double-triggering.

**Revised guard:**
```jsx
if(phase !== "idle") return; // inside handleTouchEnd
```

**Confidence:** HIGH — pattern is well-established in vanilla React; no library needed.

---

### Sticky Hover Fix (Blocker noted in STATE.md)

STATE.md explicitly flagged: "Sticky hover state on touch screens (onMouseEnter fires but onMouseLeave does not on touch) — needs onTouchEnd/onTouchCancel handlers."

Affected elements:
- `USMap` → `onMouseLeave={()=>onHover(null)}` on each `<g>`
- `NYCMap` → `onMouseLeave={()=>onHover(null)}` on each `<g>`
- `HospitalScene` → `onMouseLeave` on rooms and person figures (lines 467-507)

Fix: add `onTouchEnd` and `onTouchCancel` that call the same clear function:

```jsx
// USMap <g> element
<g
  key={st}
  onMouseEnter={()=>has&&onHover(st)}
  onMouseLeave={()=>onHover(null)}
  onTouchEnd={()=>onHover(null)}
  onTouchCancel={()=>onHover(null)}
  onClick={()=>isNY&&onZoomNYC()}
  style={{cursor:isNY?"pointer":"default", touchAction:"manipulation"}}
>
```

Apply the same pattern to NYCMap `<g>` elements and HospitalScene hover-tracked elements.

Note: `onTouchEnd` on the SVG `<g>` will fire before the synthesized `onClick` on touch devices (touch sequence: touchstart → touchend → mousemove → mousedown → mouseup → click). Clearing hover state in `onTouchEnd` is safe because the `onClick` fires after, and hover state for the panel card display is cosmetic.

**Confidence:** HIGH — this is the documented fix for React synthetic event sticky hover on touch.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Eliminating 300ms delay | FastClick, custom event deduplication | `touch-action: manipulation` | CSS-only, zero JS, universally supported since 2019 |
| Swipe detection | Third-party gesture libraries (Hammerjs, react-swipeable, use-gesture) | `onTouchStart` / `onTouchEnd` with a 50px threshold | Project forbids new dependencies; 15-line implementation covers all needed cases |
| Touch target sizing | Custom tap expansion overlays | `minWidth:44, minHeight:44` inline style | Inline padding/minHeight is the correct and simplest approach |

**Key insight:** This codebase's inline-style constraint and no-new-dependencies constraint eliminate library options entirely. All four requirements are achievable with browser-native touch events and one CSS property.

---

## Common Pitfalls

### Pitfall 1: `useRef` Dependency Array in `useCallback`

**What goes wrong:** `handleTouchEnd` reads `level` and calls `zoomTo`. If `level` is not in the dependency array, the callback captures a stale closure.

**Why it happens:** `level` is a state variable that changes; `useCallback` with an empty `[]` will always see the initial value.

**How to avoid:** Include `level` and `zoomTo` in the dependency array of `handleTouchEnd`. Since `zoomTo` is memoized with `[]`, it is stable — no infinite loop risk.

**Warning signs:** Swipe always navigates to the same level regardless of current level.

---

### Pitfall 2: Swipe Conflict with SVG Panning

**What goes wrong:** Touch on the SVG maps (USMap, NYCMap) may be intercepted by the swipe handler before the map gets the event.

**Why it happens:** `onTouchEnd` bubbles up. The root div will receive the touch event even if it started on a map element.

**How to avoid:** The 50px threshold naturally prevents accidental triggers from taps. However, if users attempt to "pan" the maps (they are not zoomable/pannable — they are static SVG displays), the swipe will fire navigation. Since the maps are static, this is acceptable and even desirable. If future phases add panning, add `e.stopPropagation()` at that point to the map's touch handler.

---

### Pitfall 3: `onTouchEnd` vs `onTouchMove` Abort

**What goes wrong:** A vertical scroll or drag doesn't cancel the swipe handler, causing accidental navigation when the user scrolls the LeftPanel.

**Why it happens:** LeftPanel is scrollable (`overflowY:"auto"`). Touch that starts in LeftPanel and moves will bubble a `touchend` to the root.

**How to avoid:** Track Y delta alongside X delta in `handleTouchEnd`. Only trigger swipe if `Math.abs(deltaX) > Math.abs(deltaY)` (i.e., the gesture is more horizontal than vertical). Requires storing both `touchStartX` and `touchStartY` refs.

```jsx
const touchStartX = useRef(null);
const touchStartY = useRef(null);

// in handleTouchStart:
touchStartX.current = e.touches[0].clientX;
touchStartY.current = e.touches[0].clientY;

// in handleTouchEnd:
const deltaX = e.changedTouches[0].clientX - touchStartX.current;
const deltaY = e.changedTouches[0].clientY - touchStartY.current;
if(Math.abs(deltaX) <= Math.abs(deltaY)) return; // vertical scroll — ignore
```

**Warning signs:** Scrolling the LeftPanel causes unintended level jumps.

---

### Pitfall 4: Animation Guard on Swipe

**What goes wrong:** Rapid swipes during the 400ms fade-out animation trigger multiple `zoomTo` calls, stacking transitions.

**Why it happens:** `phase` is not checked before calling `zoomTo`.

**How to avoid:** Guard at the top of `handleTouchEnd`: `if(phase !== "idle") return;`

---

### Pitfall 5: NavBar Props Mismatch

**What goes wrong:** Adding `onBack` / `onGoLevel` props to NavBar without updating the call site in App causes `undefined` prop and navigation silently fails.

**Why it happens:** JavaScript's permissive prop passing — calling `onGoLevel(2)` when prop is undefined throws `TypeError`.

**How to avoid:** Update both the `NavBar` function signature and the `<NavBar ...>` JSX in App in the same edit.

---

## Code Examples

### Root div with touch-action and swipe handlers

```jsx
// Source: MDN touch-action, React synthetic events
<div
  style={{
    width:"100vw", height:"100vh", overflow:"hidden",
    background:CREAM, position:"relative",
    touchAction:"manipulation",
  }}
  onTouchStart={handleTouchStart}
  onTouchEnd={handleTouchEnd}
>
```

### useRef-based swipe detection

```jsx
// Source: React docs — useRef for mutable values
const touchStartX = useRef(null);
const touchStartY = useRef(null);

const handleTouchStart = useCallback((e) => {
  touchStartX.current = e.touches[0].clientX;
  touchStartY.current = e.touches[0].clientY;
}, []);

const handleTouchEnd = useCallback((e) => {
  if(phase !== "idle") return;
  if(touchStartX.current === null) return;
  const deltaX = e.changedTouches[0].clientX - touchStartX.current;
  const deltaY = e.changedTouches[0].clientY - touchStartY.current;
  touchStartX.current = null;
  touchStartY.current = null;
  if(Math.abs(deltaX) <= Math.abs(deltaY)) return; // vertical gesture
  const THRESHOLD = 50;
  if(deltaX > THRESHOLD && level > 0) zoomTo(level - 1, null);
  else if(deltaX < -THRESHOLD && level >= 0 && level < 2) zoomTo(level + 1, null);
}, [level, phase, zoomTo]);
```

### Sticky hover fix on SVG elements

```jsx
// Source: React synthetic events — onTouchEnd fires; onMouseLeave does not on touch
<g
  onMouseEnter={()=>has&&onHover(st)}
  onMouseLeave={()=>onHover(null)}
  onTouchEnd={()=>onHover(null)}
  onTouchCancel={()=>onHover(null)}
  onClick={()=>isNY&&onZoomNYC()}
  style={{cursor:isNY?"pointer":"default", touchAction:"manipulation"}}
>
```

### NavBar back arrow button

```jsx
// 44px minimum touch target via minHeight + padding
{level > 0 && (
  <button
    onClick={onBack}
    style={{
      background:"none", border:"none", color:"#f5f5f5",
      cursor:"pointer", fontFamily:"'Libre Franklin',sans-serif",
      fontSize:20, fontWeight:700,
      minWidth:44, minHeight:44,
      display:"flex", alignItems:"center", justifyContent:"center",
      marginRight:8, touchAction:"manipulation",
    }}
  >&#8592;</button>
)}
```

---

## Environment Availability

Step 2.6: SKIPPED (no external dependencies identified — this phase is pure inline code changes to `src/App.jsx`).

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| FastClick library for 300ms delay | `touch-action: manipulation` CSS property | Chrome 32 (2014), widely available 2019 | No library needed |
| `touchstart`/`touchend` with `preventDefault()` | Native click with `touch-action` manipulation | 2014-2019 | No synthetic events needed |
| Separate mobile/desktop hover CSS | `@media (hover: hover)` query | CSS4 / 2017+ | CSS-only solution for sticky hover |

**Deprecated/outdated:**
- FastClick: no longer needed; `touch-action: manipulation` is the replacement
- `react-tap-event-plugin` (react-tap-event-plugin on npm): archived; not needed in React 19

---

## Open Questions

1. **Swipe at level -1 (title screen)**
   - What we know: Level -1 shows the title page; there is no "back" from this level.
   - What's unclear: Should swipe left on the title screen go to level 0, or be a no-op? The requirement says "navigate between adjacent levels" — but level -1 to 0 is the BEGIN button action.
   - Recommendation: Treat swipe left on the title screen as a no-op (same as clicking BEGIN requires explicit intent). Swipe right on any screen where `level <= 0` is a no-op.

2. **Swipe-left at level 2 to level 3**
   - What we know: Level 3 requires a `selectedPerson` to be set. `zoomTo(3, null)` explicitly sets `selectedPerson` to null, causing a blank PersonStory.
   - What's unclear: Should the planner include a "swipe left at level 2 advances to first person" behavior?
   - Recommendation: Keep swipe-left at level 2 as a no-op. Level 3 is person-specific and must be entered by tapping a person figure. This keeps swipe semantics simple and matches the app's narrative intent.

3. **LeftPanel Back button at level 3**
   - What we know: LeftPanel is not rendered at level 3. The PersonStory view has its own "← Back to Health Center" button at the bottom.
   - What's unclear: NAV-01 says "in the NavBar" — does the existing bottom button on PersonStory count for touch compliance?
   - Recommendation: The NavBar back arrow covers NAV-01 fully. The existing PersonStory bottom button is complementary; ensure it also has `minHeight:44` and `touch-action: manipulation`.

---

## Sources

### Primary (HIGH confidence)
- MDN Web Docs: `touch-action` — property definition, browser support, `manipulation` keyword behavior
  - https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action
- Chrome Developers Blog: "300ms tap delay, gone away"
  - https://developer.chrome.com/blog/300ms-tap-delay-gone-away

### Secondary (MEDIUM confidence)
- CSS-Tricks: "Solving Sticky Hover States with @media (hover: hover)"
  - https://css-tricks.com/solving-sticky-hover-states-with-media-hover-hover/
- KIRUPA: "Detecting Touch Swipe Gestures"
  - https://www.kirupa.com/html5/detecting_touch_swipe_gestures.htm
- WebAbility: "Target Size - Complete Guide"
  - https://www.webability.io/glossary/target-size (44px Apple HIG / Google Material / WCAG 2.5.8)

### Tertiary (LOW confidence)
- javascriptkit.com: "4 novel ways to deal with sticky :hover effects"
  - http://javascriptkit.com/dhtmltutors/sticky-hover-issue-solutions.shtml

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — all techniques are native browser APIs or CSS properties, documented by MDN
- Architecture: HIGH — patterns derived from reading actual App.jsx code; no assumptions
- Pitfalls: HIGH — the sticky hover issue is explicitly documented in STATE.md; the swipe/scroll conflict is a well-known pattern

**Research date:** 2026-03-24
**Valid until:** 2026-09-24 (stable — CSS properties and React synthetic events do not change frequently)
