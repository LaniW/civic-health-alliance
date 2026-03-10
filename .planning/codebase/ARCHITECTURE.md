# Architecture

**Analysis Date:** 2026-03-10

## Pattern Overview

**Overall:** Multi-level interactive data visualization with hierarchical zoom-in pattern

**Key Characteristics:**
- Four-level progressive disclosure: US States → NYC Districts → Health Center → Individual Stories
- SVG-based interactive maps and vector graphics (no external charting libraries)
- Single-component monolithic architecture with inline state management
- Declarative animation transitions between zoom levels
- Inline styling throughout (no external CSS except animations and fonts)
- Custom SVG path data for geographic visualizations

## Layers

**Presentation Layer:**
- Purpose: Render all UI elements including maps, scenes, and overlays
- Location: `src/App.jsx` (entire component tree)
- Contains: JSX components for USMap, NYCMap, HospitalScene, PersonStory, InfoPanel, NarrativeOverlay, PersonFig, Pulse
- Depends on: React (useState, useCallback), inline data constants
- Used by: Browser rendering engine

**Data Layer:**
- Purpose: Store and organize all application data
- Location: `src/App.jsx` lines 7–248 (constant definitions)
- Contains:
  - `STATE_DATA`: US state metrics (voter turnout, uninsured rate, life expectancy)
  - `HEX_GRID`: Hexagon grid coordinates for US state positioning
  - `CD_PATHS`: SVG path definitions for NYC community district boundaries
  - `NYC_DISTRICTS`: NYC district data with life expectancy and voter turnout
  - `HOSPITAL_PEOPLE`: Four individuals with personal timelines
  - `DEPARTMENTS`: Hospital department metadata (wait times, severity)
  - Color mapping functions: `leColor()`, `engagementColor()`, `typeColor`

**State Management Layer:**
- Purpose: Track user interactions and view state
- Location: `src/App.jsx` lines 489–495 (useState hooks in App component)
- Managed state:
  - `level`: Current zoom level (−1 = title, 0 = US, 1 = NYC, 2 = Hospital, 3 = Person)
  - `phase`: Animation phase ("idle", "out", "in")
  - `hovState`: Hovered US state code
  - `hovHood`: Hovered NYC district ID
  - `hovPerson`: Hovered hospital person ID
  - `hovDept`: Hovered hospital department ID
  - `selectedPerson`: Currently selected person for story view

## Data Flow

**Zoom Navigation:**

1. User clicks on interactive element (state hex, NYC district, hospital person)
2. `zoomTo()` callback triggered with new level and optional person ID
3. State animates out: `phase="out"` → opacity 0, scale 1.6
4. 400ms later: level and person state updated, phase transitions to "in"
5. State animates in: `phase="in"` → opacity 1, scale 1 over 450ms
6. Breadcrumb navigation updated via `crumbs` array calculation

**Hover Interactions:**

1. SVG element receives `onMouseEnter` → updates specific hover state (hovState, hovHood, hovPerson, hovDept)
2. Hovered element receives visual updates: opacity, stroke width, stroke color
3. `InfoPanel` and hover tooltips render dynamically based on hover state
4. `onMouseLeave` resets hover state to null

**Data Rendering:**

- `USMap`: Iterates `HEX_GRID` + `STATE_DATA`, renders colored hexagon polygons with state codes
- `NYCMap`: Iterates `NYC_DISTRICTS`, looks up `CD_PATHS` SVG paths, colors by life expectancy
- `HospitalScene`: Renders `DEPARTMENTS` as rectangles, `HOSPITAL_PEOPLE` as scaled figure groups
- `PersonStory`: Renders single selected person's data including timeline events from `person.timeline` array

## Key Abstractions

**Pulse Animation Component:**
- Purpose: Animated circular pulse indicator on interactive elements
- Examples: `src/App.jsx` lines 252–260
- Pattern: SVG `<animate>` tags with SMIL animation (radius and opacity)
- Used for: Visual emphasis on state hexes, person icons

**Person Figure Component:**
- Purpose: Render stylized human figures with variant types
- Examples: `src/App.jsx` lines 339–358
- Pattern: Conditional SVG shapes (circles for head, rectangles for body, lines for limbs)
- Variants: "adult" (default), "child", "doctor"
- Scaling: All figures scale by 0.45 via transform

**Color Mapping Functions:**
- `leColor(le)`: Maps life expectancy to color (red < 79 → yellow → green 86+)
- `engagementColor(t)`: Maps voter turnout percentage to color gradient
- `typeColor`: Object literal mapping event types ("health", "civic") to colors

**Timeline Event Structure:**
- Purpose: Represent personal history events with date, type, title, detail
- Pattern: Array of objects with `{date, type, title, detail}` shape
- Used in: PersonStory component to render animated timeline

## Entry Points

**Application Root:**
- Location: `src/main.jsx`
- Triggers: Application startup via Vite
- Responsibilities: Mounts React app to DOM element with ID "root"

**Title Screen:**
- Location: `src/App.jsx` lines 526–533
- Triggers: When `level === -1`
- Responsibilities: Displays title, tagline, "Begin" button; initiates zoom to level 0

**Navigation Header:**
- Location: `src/App.jsx` lines 535–544
- Triggers: When `level >= 0`
- Responsibilities: Renders breadcrumb trail; allows navigation back to title or previous zoom levels

**Main Zoom State Logic:**
- Location: `src/App.jsx` lines 497–506 (zoomTo callback)
- Triggers: User clicks interactive element
- Responsibilities: Orchestrates zoom animation sequence and state updates

## Error Handling

**Strategy:** No explicit error handling implemented

**Current State:**
- SVG element lookups (e.g., `CD_PATHS[n.id]`) return null without error catching
- Missing person or district data silently returns null from components
- No try-catch blocks or error boundaries
- No fallback UI for data fetch failures (not applicable—all data is hardcoded)

## Cross-Cutting Concerns

**Logging:** None. No console logging, debugging, or telemetry.

**Validation:** Minimal. Data shape validation only via ternary checks:
- `if(!person) return null`
- `if(!cd) return null`
- No schema validation or type checking

**Authentication:** Not applicable. No user authentication or authorization.

**Animation:** SMIL animations for Pulse component; CSS transitions via inline style objects; React state transition delays via setTimeout.

**Styling:** All via inline `style={{}}` objects and `<style>` tag in root. No CSS-in-JS library. Fonts imported via `<style>` tag with Google Fonts URL.

---

*Architecture analysis: 2026-03-10*
