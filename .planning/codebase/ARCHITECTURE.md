# Architecture

**Analysis Date:** 2026-03-24

## Pattern Overview

**Overall:** Single-file monolithic SPA (Single Page Application)

**Key Characteristics:**
- All application code (730 lines, 72KB) lives in one file: `src/App.jsx`
- No routing library; navigation is a state-driven zoom-level system
- Data is embedded as inline JavaScript object literals (no API, no database)
- All styling is inline (`style={{}}` objects); no CSS modules or utility classes
- SVG-based data visualization rendered entirely in React (no D3, no charting library)
- Zero external dependencies beyond React and ReactDOM

## Layers

**Data Layer:**
- Purpose: Provides all health, civic, and geographic data for the visualization
- Location: `src/App.jsx` lines 13-212 (top of file, before any components)
- Contains: Six data constants (`STATE_DATA`, `HEX_GRID`, `CD_PATHS`, `NYC_DISTRICTS`, `HOSPITAL_PEOPLE`, `DEPARTMENTS`) plus derived constants (`sevColor`, `typeColor`)
- Depends on: Nothing (pure data)
- Used by: Every component reads from these constants directly via closure

**Utility Layer:**
- Purpose: Color-mapping functions that convert data values to visual colors
- Location: `src/App.jsx` lines 197-198
- Contains: `leColor(le)` (life expectancy to 4-tier color), `engagementColor(t)` (voter turnout to 4-tier color)
- Depends on: Nothing (pure functions)
- Used by: `USMap`, `NYCMap`, `LeftPanel`

**Component Layer:**
- Purpose: All UI rendering (SVG maps, HTML panels, navigation)
- Location: `src/App.jsx` lines 229-633
- Contains: 8 function components (see Component Hierarchy below)
- Depends on: Data constants, utility functions, React hooks
- Used by: `App` root component orchestrates all of them

**Orchestration Layer:**
- Purpose: Root component managing all state and transitions
- Location: `src/App.jsx` lines 638-730 (`export default function App()`)
- Contains: All `useState` hooks, the `zoomTo` transition callback, layout logic
- Depends on: All other layers
- Used by: `src/main.jsx` (entry point)

## Component Hierarchy

```
App (root)                           [src/App.jsx:638]
|
+-- NavBar                           [src/App.jsx:558]
|   Props: level, onHome, onGoUS, onGoNYC
|   Renders: Brand button, US/NYC map tabs, breadcrumb trail
|   Hidden when: level < 0
|
+-- Title Page (inline JSX)          [src/App.jsx:683-698]
|   Renders when: level === -1
|   Contains: Title text, "BEGIN" button -> zoomTo(0)
|
+-- Map Layout (levels 0-2)          [src/App.jsx:702-716]
|   Renders when: level >= 0 && level <= 2
|   Layout: Flex row (LeftPanel 360px + visualization area flex-1)
|   |
|   +-- LeftPanel                    [src/App.jsx:248]
|   |   Props: level, hovState, hovHood, onPrev, onNext
|   |   Contains:
|   |     - CHAPTERS text box (indexed by level)
|   |     - Level-specific legends (voter turnout at L0, life expectancy gap at L1)
|   |     - Hover detail cards (state stats at L0, neighborhood stats at L1)
|   |     - Nav arrows (Back / Next)
|   |
|   +-- Visualization Area (conditional by level)
|       +-- USMap (level 0)          [src/App.jsx:402]
|       |   Props: hovered, onHover, onZoomNYC
|       |   Renders: SVG hexagonal state map, 50 colored polygons
|       |   Click NY state -> onZoomNYC()
|       |
|       +-- NYCMap (level 1)         [src/App.jsx:427]
|       |   Props: hovered, onHover, onZoomCenter
|       |   Renders: SVG community district map, ~59 path shapes
|       |   Contains: Pulse on target district (isTarget flag)
|       |   Click target district -> onZoomCenter()
|       |
|       +-- HospitalScene (level 2)  [src/App.jsx:467]
|           Props: hoveredPerson, onHoverPerson, onClickPerson, hoveredDept, onHoverDept
|           Renders: Building with 4 DEPARTMENTS rooms + 4 HOSPITAL_PEOPLE figures
|           Contains:
|             PersonFig (x4)         [src/App.jsx:446]
|             Pulse (x4, one per person)
|           Click person -> onClickPerson(id)
|
+-- PersonStory (level 3)            [src/App.jsx:512]
    Props: person (full object)
    Renders when: level === 3 && selectedPerson
    Contains: Avatar, bio, timeline with color-coded events, summary card
    Back button -> zoomTo(2, null)
```

## Data Flow

**Zoom Navigation Flow:**

1. User triggers navigation (click state, click district, click person, click nav button)
2. Component calls a callback received as prop (e.g., `onZoomNYC`, `onClickPerson`)
3. Callback invokes `zoomTo(nextLevel, personId?)` in `App`
4. `zoomTo` runs a two-phase animation via `phase` state:
   - Sets `phase` to `"out"` (triggers opacity 0 + scale 1.05 over 400ms)
   - After 400ms timeout: updates `level`, resets all hover states, optionally sets `selectedPerson`, sets `phase` to `"in"` (opacity 1 + scale 1 over 450ms)
   - After 450ms timeout: sets `phase` to `"idle"` (static)

```
User Click
  |
  v
Component callback (prop)
  |
  v
zoomTo(newLevel, personId?)       [App, line 647]
  |
  +-- setPhase("out")              --- 400ms fade+scale out ---+
  |                                                            |
  |   setTimeout(400ms):                                       |
  |   +-- setLevel(newLevel)                                   |
  |   +-- setSelectedPerson(...)                               |
  |   +-- reset all hover states                               |
  |   +-- setPhase("in")           --- 450ms fade+scale in  ---+
  |                                                            |
  |       setTimeout(450ms):                                   |
  |       +-- setPhase("idle")                                 |
  |                                                            |
  v                                                            v
Level renders correct view based on `level` state value
```

**Hover Data Flow:**

1. User hovers over a map element (state, district, department, person)
2. SVG element's `onMouseEnter` calls setter prop (e.g., `onHover`)
3. Setter updates corresponding hover state in `App` (e.g., `hovState`, `hovHood`)
4. `LeftPanel` re-renders conditionally based on hover state, showing data card
5. The hovered element also re-renders with highlight styling (thicker stroke, full opacity)

```
SVG onMouseEnter -----> setState via prop -----> App re-renders
                                                    |
                                          +---------+---------+
                                          |                   |
                                    LeftPanel shows      SVG element
                                    hover data card      gets highlight
                                          |              styles
                                          v
                                    onMouseLeave -----> setState(null)
```

**State Management:**

All state lives in `App` via `useState` hooks. There is no context, no reducer, no state library.

| State Variable   | Type            | Purpose                              | Set By                |
|------------------|-----------------|--------------------------------------|-----------------------|
| `level`          | `number`        | Current zoom level (-1 to 3)         | `zoomTo()`            |
| `phase`          | `string`        | Animation phase ("idle"/"out"/"in")  | `zoomTo()`            |
| `hovState`       | `string\|null`  | Hovered US state code (e.g., "NY")   | `USMap` via prop      |
| `hovHood`        | `string\|null`  | Hovered NYC district ID              | `NYCMap` via prop     |
| `hovPerson`      | `string\|null`  | Hovered person ID in hospital        | `HospitalScene` prop  |
| `hovDept`        | `string\|null`  | Hovered department ID in hospital    | `HospitalScene` prop  |
| `selectedPerson` | `object\|null`  | Full person object for story view    | `zoomTo()` with pid   |

## Key Abstractions

**Zoom Level Hierarchy:**
- Purpose: Represents the narrative drill-down from macro to micro
- Levels: -1 (title), 0 (US map), 1 (NYC map), 2 (hospital), 3 (person story)
- Pattern: The `level` integer controls which view renders. Conditional rendering via `{level===N && (...)}` blocks in App's return JSX at `src/App.jsx:683-728`.
- Navigation entries: Title screen BEGIN button -> 0, NY hex click -> 1, target district pulse click -> 2, person figure click -> 3. Also LeftPanel Back/Next arrows and NavBar tabs.

**Chapter System:**
- Purpose: Maps zoom levels 0-2 to narrative text shown in the left panel
- Data: `CHAPTERS` array at `src/App.jsx:242-246`
- Shape: `{ch: string, title: string, body: string, cta: string}`
- Indexed by: `level` (only levels 0, 1, 2 have chapters)

**Color Mapping:**
- Purpose: Converts numeric health/civic data to a 4-tier color scale
- Functions at `src/App.jsx:197-198`:
  - `leColor(le)`: life expectancy thresholds at 79, 82, 86
  - `engagementColor(t)`: voter turnout thresholds at 58, 64, 70
- Palette: `#dc2626` (red) < `#eab308` (yellow) < `#65a30d` (light green) < `#15803d` (dark green)
- Both functions use the same 4 colors with different breakpoints

**Panel Card Style:**
- Purpose: Shared styling constant for LeftPanel info boxes
- Location: `src/App.jsx:220-224` (`panelCard` const)
- Pattern: Cream background, red-tinted border, consistent padding
- Used by: Every card in `LeftPanel` via spread: `style={{...panelCard, ...overrides}}`

**Animation Phase Machine:**
- Purpose: Manages fade-out/fade-in transitions between zoom levels
- States: `"idle"` -> `"out"` -> `"in"` -> `"idle"`
- Implementation: `anim` computed object at `src/App.jsx:658-662` derives CSS from `phase`
  - `"out"`: `{opacity:0, transform:"scale(1.05)", transition:"all 0.4s cubic-bezier(0.4,0,1,1)"}`
  - `"in"`: `{opacity:1, transform:"scale(1)", transition:"all 0.45s cubic-bezier(0,0,0.2,1)"}`
  - `"idle"`: `{opacity:1, transform:"scale(1)"}` (no transition)
- Applied via: `style={{...anim}}` spread on each level's container div

## Entry Points

**Browser Entry:**
- Location: `index.html`
- Triggers: Browser loads the page
- Responsibilities: Provides `<div id="root">` mount point, loads `src/main.jsx` as ES module

**React Entry:**
- Location: `src/main.jsx`
- Triggers: Vite bundles and serves it
- Responsibilities: Creates React root via `createRoot()`, renders `<App />` inside `<StrictMode>`

**Application Entry:**
- Location: `src/App.jsx` (default export `App` at line 638)
- Triggers: Rendered by `main.jsx`
- Responsibilities: All state management, all rendering, all navigation

## Error Handling

**Strategy:** Defensive null checks only. No error boundaries, no try/catch, no fallback UI.

**Patterns:**
- `PersonStory` returns `null` if `person` is falsy: `if(!person) return null` at line 513
- `NavBar` returns `null` on the title screen: `if(level<0) return null` at line 559
- `NYCMap` guards missing SVG paths: `if(!cd) return null` at line 432
- No global error boundary; uncaught errors crash the entire app with a white screen
- No loading states (all data is inline, nothing async)

## Cross-Cutting Concerns

**Logging:** None. No console.log, no error reporting, no analytics.

**Validation:** None. Data is hardcoded and trusted. No user input exists beyond hover/click.

**Authentication:** Not applicable. Static client-side-only visualization with no backend.

**Fonts:** Loaded via inline `<style>` tag containing a Google Fonts `@import` at `src/App.jsx:667`. Three font families imported:
- Libre Franklin (body text, headings, UI labels)
- JetBrains Mono (data values, monospace labels)
- Playfair Display (imported but unused in current code)

**Global CSS Reset:** Injected via inline `<style>` tag in `App`'s return JSX at lines 666-672:
- `*{margin:0;padding:0;box-sizing:border-box;}`
- `@keyframes fadeIn` for staggered element appearance
- Custom scrollbar styles
- Link and button hover styles

**Scaffold CSS Files:** `src/index.css` and `src/App.css` are Vite scaffold leftovers. `index.css` sets dark theme base styles that are largely overridden by App's inline styles. `App.css` contains unused logo animation styles.

**Animation Mechanisms (two coexist):**
1. CSS transitions on inline `style` objects: hover effects on SVG elements (`transition:"all 0.2s"`), zoom animation (`anim` object)
2. SVG SMIL `<animate>` elements: pulsing dots in `Pulse` component (radius + opacity animation), NY state highlight border

---

*Architecture analysis: 2026-03-24*
