# Architecture Research

**Domain:** Single-file React SPA — incremental refinements to touch navigation, SVG layout, and content data
**Researched:** 2026-03-24
**Confidence:** HIGH — based on direct code analysis of `src/App.jsx` (730 lines) and project constraints

---

## Current System State

Everything lives in `src/App.jsx`. The project constraint from `PROJECT.md` explicitly rules out file decomposition for this milestone. All changes described in this document are edits within that single file.

```
src/App.jsx (730 lines)
 ├── Lines 1–224     Data layer + color utilities + shared style constants
 ├── Lines 229–237   Pulse component (SVG pulse dot)
 ├── Lines 242–397   LeftPanel (narrative text, legends, hover cards, nav arrows)
 ├── Lines 402–421   USMap (hex state grid)
 ├── Lines 427–441   NYCMap (community district SVG)
 ├── Lines 446–465   PersonFig (SVG person figure primitives)
 ├── Lines 467–507   HospitalScene (rooms + person figures)
 ├── Lines 512–553   PersonStory (individual timeline)
 ├── Lines 558–633   NavBar (breadcrumb navigation)
 └── Lines 638–730   App (state machine, layout, zoom transitions)
```

The App component is the sole owner of all state. Components receive data and callbacks as props. No context, no shared module state, no event bus.

---

## Component Boundaries

### NavBar (lines 558–633)

**Owns:** Top navigation bar rendering. Nothing else.

**Receives from App:**
- `level` — integer (-1 to 3) controlling which breadcrumbs render
- `onHome` — callback: `() => zoomTo(-1)`
- `onGoUS` — callback: `() => level !== 0 && zoomTo(0)`
- `onGoNYC` — callback: `() => level < 1 && zoomTo(1)`

**Does NOT receive:** Callbacks for levels 2 (Health Center) or 3 (Story). These breadcrumbs at lines 617–630 render as display-only `<span>` labels — no `onClick` handlers. They show context ("Health Center" and "Story") but tapping them does nothing on touch screens.

**Current breadcrumb structure:**
```
Level 0:  [Civic Health Alliance]  [US Map •active]  [NYC Map]
Level 1:  [Civic Health Alliance]  [US Map]  [NYC Map •active]
Level 2:  [Civic Health Alliance]  [US Map]  [NYC Map •active]  › Health Center
Level 3:  [Civic Health Alliance]  [US Map]  [NYC Map]  › Health Center  › Story
```

At levels 2 and 3 the "Health Center" and "Story" spans have no touch target. A user on a touch screen at level 3 must know to tap "NYC Map" to go back two levels — or rely on the "Back to Health Center" button inside `PersonStory`. The back arrow in `LeftPanel` (`onPrev`) is hidden at level 3 because `PersonStory` occupies a separate layout branch.

**Touch navigation gap:** The back arrow in `LeftPanel` at line 384 fires `onPrev={() => zoomTo(level-1, null)}` and the next arrow fires `onNext`. These exist inside the levels 0–2 layout branch at lines 702–717. At level 3 (PersonStory), the layout switches to a completely different branch (lines 720–728) and neither arrow is present. The only explicit back path from level 3 is the "Back to Health Center" button at line 724.

**Explicit back arrow requirement:** The active requirement in `PROJECT.md` calls for a back arrow button *in NavBar* for touch screen navigation. Adding it to NavBar means NavBar needs a new prop — a back callback from App — and renders a visible touch-sized button at all levels > 0. The App component wires the correct `zoomTo` target.

**Tappable breadcrumbs requirement:** The level-2 and level-3 breadcrumb spans need `onClick` callbacks. At level 2 the "Health Center" span is already the active location — no navigation needed. At level 3, "Health Center" should call `zoomTo(2)` and "Story" is the active label. The US/NYC buttons already have `onClick` at all levels. The gap is only for the level 2+ breadcrumb spans.

---

### HospitalScene (lines 467–507)

**Owns:** Rendering 4 department rooms and 4 person figures inside an SVG `viewBox="0 0 100 96"`.

**Receives from App:**
- `hoveredPerson`, `onHoverPerson` — hover state for person figures
- `onClickPerson` — fires `zoomTo(3, id)` when a person is clicked
- `hoveredDept`, `onHoverDept` — hover state for department rooms

**Layout issue — person figure positioning:**

People are positioned via `x` and `y` props passed to `PersonFig`. Those coordinates in `HOSPITAL_PEOPLE` are:

| Person | x | y | Department area |
|--------|---|---|-----------------|
| Maria Santos | 60 | 69 | Pediatrics room (x:46–74, y:55–83) |
| Dr. Okafor | 29 | 36 | Primary Care room (x:14–44, y:22–50) |
| James Whitfield | 72 | 36 | Waiting Room area (x:56–88, y:22–50) |
| Sofia Chen | 30 | 71 | Dental room (x:18–42, y:55–83) |

`PersonFig` renders with `scale(0.45)` applied to a group centered at `(x, y)`. The adult figure body extends from approximately y-3 to y+12 in unscaled units, then compressed by 0.45. The center of the rendered figure is not at the geometric center of its room rectangle — figures appear to float in the upper portion of the room rather than being vertically centered.

The centering fix requires calculating each room's center point from the `DEPARTMENTS` constants:
- `cx = d.x + d.w/2`
- `cy = d.y + d.h/2`

Then placing the figure's reference point at a vertical offset from that center. The `PersonFig` component's visual center of mass is approximately at `y + 5` in unscaled space (midpoint of head-to-legs span of ~17 units, scaled by 0.45 = ~7.6 units tall, center at ~3.8 below the translate origin). Placing each person at `(cx, cy - 3)` would center them more accurately within their room.

**SVG text overlap issue:**

Department rooms display three stacked text elements:
1. Department label at `d.y + 4.5` (fontSize 2.4)
2. Wait number at `d.y + 8` (fontSize 3.5) — only when `d.wait !== null`
3. Wait unit ("day wait") at `d.y + 10.5` (fontSize 1.5)
4. Note at `d.y + d.h - 2.5` (fontSize 1.6)

Room height is 28 SVG units. At the bottom of the room (`d.y + d.h - 2.5 = d.y + 25.5`) the note sits close to the person figure that also occupies this room. The label and wait number at the top, plus the note at the bottom, leave roughly 12 SVG units of space in the middle — where person figures are placed. The overlap between person figures and room text becomes visible when figure coordinates happen to be near text anchor positions.

---

### PersonStory (lines 512–553)

**Owns:** Full-page rendering of a single person's timeline narrative.

**Receives from App:**
- `person` — a full object from `HOSPITAL_PEOPLE`

**Content replacement scope:** The four entries in `HOSPITAL_PEOPLE` (Maria, Dr. Okafor, James, Sofia) are composite fictional characters. Replacing them with real stories from Stella Saffo's transcript means editing the data constants at lines 200–205 of `src/App.jsx`. The component itself does not need changes — it renders whatever `person.bg` and `person.timeline` contain. The structural change is purely in data.

**Impact on PersonStory's summary card (lines 543–549):** The summary messages at the bottom of PersonStory are hard-coded `person.id === "maria"` conditionals. When person IDs change for new story subjects, these conditionals must be updated to match the new IDs, or the summary text must be moved into the person data object itself (a `summary` field on each person).

Moving the summary text into the data object is the cleaner approach: add a `summary` field to each `HOSPITAL_PEOPLE` entry, then render `{person.summary}` in the component instead of the switch-on-id pattern. This makes the content replacement fully self-contained in the data constants.

---

### LeftPanel (lines 242–397)

**Owns:** Chapter narrative text, legends (levels 0 and 1), hover detail cards, and Back/Next navigation arrows.

**Navigation arrows:** Lines 378–395 render Back and Next buttons conditioned on `level > 0` and `level < 2` respectively. These are present only within the levels 0–2 layout branch. They do not cover level 3.

**Relationship to NavBar back arrow:** `LeftPanel`'s Back arrow and NavBar's proposed back arrow would both call `zoomTo(level - 1)`. They serve different purposes: LeftPanel arrows are part of the editorial narrative flow ("go to the previous chapter"), while NavBar's back arrow is a universal escape hatch for touch users who do not see LeftPanel (e.g., at level 3 where LeftPanel is absent). Both can coexist without conflict.

---

### App (lines 638–730)

**Owns:** All state, the `zoomTo` transition function, layout branching, and prop wiring.

**Props passed down to NavBar:**
```
level, onHome, onGoUS, onGoNYC
```

Adding a back arrow to NavBar requires passing one more prop:
```
onBack={() => level > 0 && zoomTo(level - 1, null)}
```

At level -1, NavBar returns null (line 559), so `onBack` is never rendered. At level 0 (US map), going back would go to level -1 (title). The `onBack` handler should guard against navigating past level 0 to the title screen — whether that is desirable depends on UX intent. The PROJECT.md requirement says "back arrow button for touch screen navigation" without specifying a floor. The simplest safe implementation is `level > 0` as the condition.

**zoomTo function (lines 647–656):**

```javascript
const zoomTo = useCallback((nl, pid) => {
  setPhase("out");
  setTimeout(() => {
    setLevel(nl);
    if (pid !== undefined) setSelectedPerson(pid ? HOSPITAL_PEOPLE.find(p => p.id === pid) : null);
    setHovState(null); setHovHood(null); setHovPerson(null); setHovDept(null);
    setPhase("in");
    setTimeout(() => setPhase("idle"), 450);
  }, 400);
}, []);
```

This function is used unmodified for all four milestone changes. Touch navigation (NavBar back arrow, tappable breadcrumbs) calls `zoomTo` exactly as hover/click navigation already does. No changes needed to `zoomTo` itself.

**Known fragility:** `zoomTo` has no guard for concurrent calls. Rapid tapping could trigger overlapping `setTimeout` chains. Adding a `phase !== "idle"` guard at the top of `zoomTo` prevents this without breaking existing behavior. This is a separate concern from the milestone changes but is worth noting because touch screens make rapid tapping more likely than mouse clicking.

---

## Data Flow

### Zoom Navigation (all 4 milestone changes use this path)

```
Touch event on NavBar button / breadcrumb / LeftPanel arrow
  |
  v
onClick callback (prop from App)
  |
  v
zoomTo(newLevel, personId?) in App
  |
  +-- setPhase("out")       [400ms fade out]
  |
  +-- setTimeout 400ms:
      setLevel(newLevel)
      setSelectedPerson(...)
      reset all hover states
      setPhase("in")        [450ms fade in]
      setTimeout 450ms: setPhase("idle")
  |
  v
App re-renders with new level
NavBar re-renders with new level (active states, breadcrumbs)
Correct view branch renders (title / map layout / person story)
```

### Hover Flow (SVG layout changes affect this path)

```
onMouseEnter on SVG <g> element
  |
  v
onHoverPerson(id) or onHoverDept(id) prop
  |
  v
setHovPerson(id) or setHovDept(id) in App
  |
  v
HospitalScene re-renders:
  - tooltip <rect>/<text> appears near person at (p.x - 16, p.y - 15)
  - department room highlight (stroke/fill change)
```

When person `x`/`y` coordinates change for the centering fix, the tooltip position `(p.x - 16, p.y - 15)` also moves with them. No separate tooltip positioning logic is needed — it is relative to the figure's coordinates.

### Content Replacement Flow (data-only change)

```
HOSPITAL_PEOPLE constant (lines 200–205)
  |
  v
App reads HOSPITAL_PEOPLE in zoomTo to find selectedPerson
  |
  v
PersonStory receives person as prop
  |
  v
PersonStory renders person.name, person.bg, person.timeline[]
PersonStory renders person.summary (after field is added)
```

The only components that read `HOSPITAL_PEOPLE` directly are:
1. `HospitalScene` — iterates `.map()` to render person figures
2. `App` — uses `.find()` inside `zoomTo` to resolve `selectedPerson`

`PersonStory` receives the resolved person object as a prop and does not import the array. Replacing content in `HOSPITAL_PEOPLE` is fully self-contained in the data constants. The `PersonFig` component uses only `x`, `y`, `color`, and `variant` — it does not read name, bg, or timeline. No layout component needs changes for content replacement.

---

## Suggested Build Order

The four changes in this milestone have dependency relationships:

### 1. Add `summary` field to `HOSPITAL_PEOPLE` data objects

**Why first:** This is a pure data addition with no component side effects. It can be done while reading the transcript. It also unlocks removing the hard-coded `person.id === "maria"` conditionals before the actual story content is ready, reducing the coupling between data and component code.

**What it touches:** `HOSPITAL_PEOPLE` constant only.

### 2. Update `PersonStory` to use `person.summary`

**Why second:** Remove the `person.id` switch-on-id pattern in the summary card (lines 543–549) and replace with `{person.summary}`. This decouples the component from specific person IDs before the IDs change. After this change, any data content swap in step 4 will render correctly without component edits.

**What it touches:** `PersonStory` component only (6 lines removed, 1 line added).

### 3. Fix `NavBar` touch navigation

**Why third:** NavBar changes require adding a new prop in App and modifying NavBar. This is low risk and has no visual dependency on the SVG layout or content changes. It should be done and visually verified independently.

**Specific changes:**
- Add `onBack` prop to `NavBar` function signature
- Render a back arrow button (`← ` or `‹`) conditionally when `level > 0`
- Add `onClick` to the "Health Center" breadcrumb span at level 3, wiring `zoomTo(2)`
- Wire `onBack={() => level > 0 && zoomTo(level - 1, null)}` in App's NavBar render

**Touch target size:** The back arrow button needs a minimum 44x44 CSS pixel touch target (standard iOS/Android guideline). At the current NavBar height of 61px, the height is sufficient. Ensure the button has `minWidth: 44` or generous padding to meet the width requirement. The existing `mapBtnBase` style object sets `width: 208` — the back arrow should be a smaller independent button, not based on `mapBtnBase`.

**What it touches:** `NavBar` component and one line in `App`'s JSX (NavBar props).

### 4. Center person figures and fix text overlap in `HospitalScene`

**Why fourth:** SVG coordinate changes require visual verification to confirm figures sit inside rooms. This is best done after the non-visual changes are committed, so any visual diff is clearly attributable to this step.

**Specific changes:**
- Update `x` and `y` in each `HOSPITAL_PEOPLE` entry to center figures within their department room
- Room centers (from `DEPARTMENTS` constants):
  - Primary Care: cx=29, cy=36 (matches current Dr. Okafor position — already centered)
  - Waiting Room: cx=72, cy=36 (matches current James position — already centered)
  - Pediatrics: cx=60, cy=69 (matches current Maria position — already centered)
  - Dental: cx=30, cy=69 (current Sofia is at x:30, y:71 — close but slightly off)
- The apparent issue is likely text overlap rather than figure position: the label at `d.y+4.5`, wait value at `d.y+8`, and note at `d.y+d.h-2.5` occupy the top and bottom of each room, with figures in the middle. If rooms are 28 units tall and figures are ~7.6 units tall (scaled), figures placed at `cy` of the room are actually competing with the bottom note text.
- Resolution: Move each person's `y` coordinate up by approximately 3–5 SVG units from the room center, placing them in the vertical middle of the empty space between the wait value text and the bottom note text.
- Text overlap fix: Verify by inspection whether `d.y + 10.5` (wait unit text) and figure positions overlap. If they do, increase vertical spacing between the wait number and wait unit, or move figure origin down within `PersonFig`'s internal coordinate system.

**What it touches:** `HOSPITAL_PEOPLE` x/y coordinates (data constants, not component logic).

### 5. Replace story content in `HOSPITAL_PEOPLE`

**Why last:** Requires reading the Stella Saffo transcript and drafting new timeline entries. This is the highest-effort change and has no dependencies on steps 3 or 4. It can proceed in parallel with steps 3 and 4, but is listed last because the data layer work (steps 1 and 2) must be complete before this is done to avoid re-editing PersonStory.

**What it touches:** `HOSPITAL_PEOPLE` constant — `id`, `name`, `role`, `age`, `bg`, `summary`, `timeline[]`, `color`. Possibly `variant` if the new characters require child or doctor figure variants.

**Side effect on NavBar breadcrumbs:** None — NavBar does not reference person names or IDs.

**Side effect on HospitalScene:** None — HospitalScene renders `PersonFig` using only `x`, `y`, `color`, and `variant` from person objects. As long as those fields remain, figure rendering is unchanged.

**Side effect on PersonStory:** None after step 2 decouples the component from person IDs.

---

## Component Responsibility Table

| Component | Responsibility | Communicates With | Change Type for Milestone |
|-----------|---------------|-------------------|--------------------------|
| `App` | State owner, layout, zoom transitions | Passes props to all components | Add `onBack` prop to NavBar wire |
| `NavBar` | Top nav, breadcrumbs | Receives callbacks from App | Add back arrow + clickable breadcrumbs |
| `LeftPanel` | Chapter narrative, hover info, nav arrows | Reads hover state via props | No change |
| `USMap` | US hex state map | Reports hover via prop | No change |
| `NYCMap` | NYC district map | Reports hover via prop | No change |
| `HospitalScene` | Hospital rooms + person figures | Reports hover/click via prop | Person x/y coordinate update only |
| `PersonFig` | SVG person figure primitive | Receives x, y, color, variant | No change |
| `PersonStory` | Individual timeline narrative | Receives person object via prop | Remove id-switch, use person.summary |
| `HOSPITAL_PEOPLE` | Person story data | Read by App, HospitalScene | Full content replacement |

---

## Integration Boundaries

### What changes are isolated within one component

- **NavBar back arrow:** Contained to `NavBar` (new button element) + one line in App (new prop). Zero effect on HospitalScene, LeftPanel, or PersonStory.
- **Tappable breadcrumbs:** Contained to `NavBar` (add onClick to span elements) + zero App changes (reuse existing `onGoNYC`/new `onGoHealthCenter` prop if needed, or inline `zoomTo(2)` call via new prop).
- **Person figure centering:** Contained to `HOSPITAL_PEOPLE` x/y values. Does not affect PersonStory, NavBar, or LeftPanel.
- **SVG text overlap fix:** Contained to text y-coordinate values inside `HospitalScene` render loop. No data changes.
- **Content replacement:** Contained to `HOSPITAL_PEOPLE` constant + minor PersonStory component edit (step 2 above).

### What shares a change surface

- `HOSPITAL_PEOPLE` is read by both `HospitalScene` (for x/y/color/variant/id) and `App.zoomTo` (for name lookup by id). Changing person IDs requires updating any switch-on-id logic in `PersonStory` — hence step 2 above eliminates that coupling first.
- `NavBar`'s breadcrumb active states are driven by `level`. Adding tappable breadcrumbs for levels 2 and 3 requires `NavBar` to receive callbacks for those levels — currently `onGoNYC` covers level 1 but there is no `onGoHealthCenter` prop. App must provide it.

### No circular dependencies

The data flows entirely from App (owner) downward to components (consumers). No component calls another component directly. No shared mutable state outside of App's useState variables.

---

## Anti-Patterns to Avoid for This Milestone

### Introducing imperative DOM mutation for touch targets

**What people do:** Add `onTouchStart`/`onTouchEnd` handlers that manually call `style.background = ...` (like the existing BEGIN button hover at lines 694–695) to simulate touch feedback.

**Why it's wrong:** Imperative style mutation bypasses React's reconciliation and can leave elements in wrong visual states after re-renders. The existing BEGIN button pattern is already fragile (noted in `CONCERNS.md`). For NavBar, touch feedback should use CSS `active` pseudo-class via a `<style>` tag or the existing `button:hover` rule in the inline styles block.

**Do this instead:** Let `button:hover` cover pointer devices. For touch feedback, add `button:active { opacity: 0.7; }` to the inline `<style>` tag at line 666. React's synthetic event system translates touch events to click events for `onClick` handlers — no `onTouchStart` needed for navigation.

### Adding new state variables for back navigation

**What people do:** Add `const [canGoBack, setCanGoBack] = useState(false)` to track navigation availability.

**Why it's wrong:** This is derivable from `level` — `canGoBack = level > 0`. Adding redundant state creates synchronization risk.

**Do this instead:** Derive navigation availability inline: `{level > 0 && <button onClick={onBack}>...</button>}`.

### Editing `PersonFig` to fix centering

**What people do:** Change the internal coordinate offsets inside the `PersonFig` component to shift where the figure "looks" centered.

**Why it's wrong:** `PersonFig` is a shared primitive used by HospitalScene for all 4 figures. Changing its internal geometry affects all instances simultaneously and may break the figure proportions. The fix belongs in the data coordinates, not the component.

**Do this instead:** Adjust `x`/`y` values in `HOSPITAL_PEOPLE`. The `PersonFig` component should remain a pure rendering primitive.

### Changing story IDs without first decoupling PersonStory

**What people do:** Replace `HOSPITAL_PEOPLE` entries with new IDs, then discover the `{person.id === "maria" && ...}` conditionals in PersonStory break.

**Why it's wrong:** The component has hard-coded dependencies on specific person IDs. This creates silent rendering failures (empty summary card) rather than crashes.

**Do this instead:** Add a `summary` field to the data objects and update PersonStory to use it (steps 1 and 2) before replacing story content.

---

## Sources

- Direct analysis of `src/App.jsx` (730 lines, 2026-03-24)
- `.planning/codebase/ARCHITECTURE.md` — existing component hierarchy and state flow documentation
- `.planning/codebase/CONCERNS.md` — touch interaction gaps, fragile areas, known bugs
- `.planning/codebase/CONVENTIONS.md` — inline style patterns, naming conventions, animation patterns
- `.planning/PROJECT.md` — active requirements and constraints

---

*Architecture research for: Civic Health Alliance — touch navigation, SVG layout, and content integration patterns*
*Researched: 2026-03-24*
