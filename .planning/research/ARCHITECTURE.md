# Architecture Research

**Domain:** Static civic health data visualization (React + Vite, SVG-based, no backend)
**Researched:** 2026-03-10
**Confidence:** HIGH

## Current State Assessment

The entire application lives in a single 567-line `src/App.jsx` file. All data is defined as JavaScript constants at the top of the file (~248 lines of data, ~240 lines of components, ~80 lines of state/layout). This monolith was appropriate for a prototype with fake data but will not survive the transition to real, citable, updateable data from multiple public sources.

The data currently embedded inline breaks down as:
- `STATE_DATA` (51 entries, 3 metrics each) -- ~50 lines
- `HEX_GRID` (51 coordinate pairs) -- 2 lines
- `CD_PATHS` (59 SVG path definitions) -- ~65 lines
- `NYC_DISTRICTS` (59 entries, 5 fields each) -- ~60 lines
- `HOSPITAL_PEOPLE` (4 people with timelines) -- ~40 lines
- `DEPARTMENTS` (4 entries) -- 5 lines
- Color functions and constants -- ~8 lines

Real data will be larger (more metrics per state, source citations, richer timelines) and must be traceable to sources. Keeping it inline in App.jsx would push the file past 1,000 lines and make data updates a minefield.

## Recommended Architecture

### System Overview

```
src/
├── data/                    # All data, separated by zoom level
│   ├── states.js            # STATE_DATA + HEX_GRID + source citations
│   ├── nycDistricts.js      # NYC_DISTRICTS + source citations
│   ├── nycPaths.js          # CD_PATHS (SVG geometry, rarely changes)
│   ├── hospital.js          # DEPARTMENTS + HOSPITAL_PEOPLE
│   └── sources.json         # Master citation registry
├── components/              # One file per zoom-level view
│   ├── USMap.jsx            # Level 0: hex grid map
│   ├── NYCMap.jsx           # Level 1: community district map
│   ├── HospitalScene.jsx    # Level 2: health center departments + figures
│   ├── PersonStory.jsx      # Level 3: individual timeline
│   ├── InfoPanel.jsx        # Hover overlay (shared)
│   ├── PersonFig.jsx        # SVG person figure (shared)
│   └── Pulse.jsx            # SVG pulse animation (shared)
├── utils/
│   ├── colors.js            # leColor, engagementColor, sevColor, typeColor
│   └── animations.js        # Zoom transition logic (if extracted)
├── App.jsx                  # ~100 lines: state, layout, zoom orchestration
└── main.jsx                 # Entry point (unchanged)
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| `App.jsx` | Zoom state machine, breadcrumb nav, layout shell | useState + useCallback, conditional rendering by level |
| `USMap` | Render hex grid, color by metric, hover states | Imports `states.js` data, receives hover callbacks as props |
| `NYCMap` | Render community district SVG paths, color by metric | Imports `nycDistricts.js` + `nycPaths.js` |
| `HospitalScene` | Render department rooms and person figures | Imports `hospital.js`, passes click to zoom handler |
| `PersonStory` | Render selected person's timeline, narrative | Receives person object as prop |
| `InfoPanel` | Floating info panel on hover | Receives data + position as props |
| `data/*.js` | Export data constants with source metadata | Named exports, ES module format |

## Data Management Strategy

### Use ES Module Files, Not JSON

Use `.js` files (not `.json`) for data because:
1. **Vite tree-shakes JS imports** -- unused exports are eliminated from the build
2. **Comments are possible** -- inline source annotations like `// CDC WONDER 2022`
3. **Computed values** -- derived fields (e.g., gap calculations) can live next to raw data
4. **Type coercion** -- no risk of string-vs-number bugs from JSON parsing

JSON is appropriate only for the citation registry (`sources.json`) since it is pure reference data that could be consumed by other tools.

### Data File Structure

Each data file follows the same pattern:

```javascript
// src/data/states.js

/**
 * Sources:
 * - Voter turnout: MIT Election Data + Science Lab, 2020 general election
 * - Uninsured rate: US Census Bureau ACS 2022 1-year estimates (Table S2701)
 * - Life expectancy: CDC WONDER, NVSS 2020-2022
 */

export const STATE_DATA = {
  AL: {
    name: "Alabama",
    voterTurnout: 58.9,      // MIT Election Lab 2020
    uninsured: 8.2,           // ACS 2022 S2701
    lifeExp: 73.8,            // CDC WONDER 2020-2022
  },
  // ...
};

export const HEX_GRID = {
  AK: [0, 0], HI: [1, 5], ME: [11, 0],
  // ... (unchanged -- this is layout, not data)
};

export const STATE_SOURCES = {
  voterTurnout: { name: "MIT Election Data + Science Lab", year: 2020, url: "https://electionlab.mit.edu/data" },
  uninsured: { name: "US Census Bureau ACS", table: "S2701", year: 2022, url: "https://data.census.gov" },
  lifeExp: { name: "CDC WONDER NVSS", years: "2020-2022", url: "https://wonder.cdc.gov" },
};
```

### SVG Paths Stay Separate

`CD_PATHS` is pure geometry (~65 lines of SVG path strings). It changes only if the district boundaries change (they do not). Keep it in `nycPaths.js` to avoid cluttering the data files that will be updated regularly.

### Citation Registry

```json
// src/data/sources.json
{
  "cdc-wonder": {
    "name": "CDC WONDER",
    "url": "https://wonder.cdc.gov",
    "accessed": "2026-03-01",
    "datasets": ["Life expectancy by state", "Mortality by cause"]
  },
  "acs-2022": {
    "name": "American Community Survey 2022",
    "url": "https://data.census.gov",
    "accessed": "2026-03-01",
    "datasets": ["Uninsured rate (S2701)", "Poverty rate"]
  }
}
```

This registry serves two purposes: (1) a footer or "Sources" section in the UI can render it, and (2) future data updates can be traced to when each source was last pulled.

## Architectural Patterns

### Pattern 1: Data-as-Module (Not Fetched)

**What:** All data is imported at build time via ES module `import` statements. No `fetch()`, no loading states, no error handling for network failures.
**When to use:** Always for this project. The constraint is "fully static, no backend."
**Trade-offs:** Bundle size increases with data, but this project's data is small (estimated <50KB total for all real data). Vite code-splits by route if needed, but with only 4 zoom levels and no routing, eager loading is fine.

```javascript
// In USMap.jsx
import { STATE_DATA, HEX_GRID } from '../data/states';
```

This is the simplest possible data architecture. It compiles to static JS in the Vite build, works with any static host, needs zero runtime data fetching.

### Pattern 2: Source Metadata Colocation

**What:** Keep source citations directly next to the data they describe, not in a separate file.
**When to use:** For every data constant. The project's credibility depends on citing real sources.
**Trade-offs:** Slightly larger source files, but dramatically easier to verify and update data.

The `STATE_SOURCES` export alongside `STATE_DATA` means a developer updating voter turnout data can immediately see which source to pull from, and can update the year/URL at the same time.

### Pattern 3: Props-Down for View Components

**What:** View components receive data as props, do not import data directly. Exception: top-level map components (USMap, NYCMap) import their own data because they are the sole consumers.
**When to use:** For shared components (InfoPanel, PersonFig, Pulse) and for PersonStory (which receives a specific person object).
**Trade-offs:** Slightly more prop threading, but components stay reusable and testable.

```javascript
// App.jsx -- PersonStory gets a specific person, not the whole array
{level === 3 && selectedPerson && (
  <PersonStory person={selectedPerson} onBack={() => zoomTo(2)} />
)}
```

## Data Flow

### Current Flow (Unchanged Conceptually)

```
[User Click/Hover]
    |
[App.jsx state machine]
    |
    +-- level 0 --> USMap (reads states.js)
    +-- level 1 --> NYCMap (reads nycDistricts.js + nycPaths.js)
    +-- level 2 --> HospitalScene (reads hospital.js)
    +-- level 3 --> PersonStory (receives person via prop)
    |
[Hover] --> InfoPanel (receives data via prop from parent)
```

### State Management

No state management library needed. The app has exactly 7 pieces of state (level, phase, hovState, hovHood, hovPerson, hovDept, selectedPerson). React useState is sufficient. Adding Context, Redux, or Zustand would be over-engineering for a visualization with no user input, no forms, no authentication.

### Key Data Flows

1. **Zoom navigation:** User click -> `zoomTo()` in App.jsx -> animation phase state -> level change -> conditional render of zoom-level component
2. **Hover info:** Mouse enter on SVG element -> set hover state in App.jsx -> pass hovered item data to InfoPanel
3. **Person selection:** Click person figure in HospitalScene -> `zoomTo(3, personId)` -> App looks up person from hospital.js -> passes to PersonStory

## Component Splitting Strategy

### Why Split Now

The monolithic App.jsx must be split **before** replacing data, not after. Reasons:
1. **Merge conflicts:** Multiple researchers updating data in the same file will collide constantly
2. **Cognitive load:** 600+ lines means scrolling past SVG paths to find component logic
3. **Incremental updates:** One zoom level's data can be updated and tested independently

### Split Order (Build Implications)

Split the file in this order to minimize risk at each step:

1. **Extract `src/utils/colors.js`** -- pure functions, zero risk. Import in App.jsx.
2. **Extract `src/data/nycPaths.js`** -- pure SVG data, no logic. Import in App.jsx.
3. **Extract `src/data/states.js`** -- STATE_DATA + HEX_GRID. Import in App.jsx.
4. **Extract `src/data/nycDistricts.js`** -- NYC_DISTRICTS. Import in App.jsx.
5. **Extract `src/data/hospital.js`** -- HOSPITAL_PEOPLE + DEPARTMENTS. Import in App.jsx.
6. **Extract `src/components/Pulse.jsx`** -- tiny leaf component, no dependencies.
7. **Extract `src/components/PersonFig.jsx`** -- tiny leaf component.
8. **Extract `src/components/InfoPanel.jsx`** -- receives all data via props.
9. **Extract `src/components/USMap.jsx`** -- imports its own data.
10. **Extract `src/components/NYCMap.jsx`** -- imports its own data.
11. **Extract `src/components/HospitalScene.jsx`** -- imports hospital.js.
12. **Extract `src/components/PersonStory.jsx`** -- receives person via props.

After step 12, `App.jsx` should be ~80-120 lines: imports, state declarations, zoomTo logic, breadcrumb nav, and conditional rendering of the four zoom-level components.

**Each step should be a separate commit that changes nothing visually.** The app should look identical after every extraction.

### Data Replacement Comes After Split

Once the file structure is established:
- Replace `src/data/states.js` with real CDC/Census/MIT Election Lab data
- Replace `src/data/nycDistricts.js` with real NYC DOH/Board of Elections data
- Replace `src/data/hospital.js` with real composite stories built from documented cases
- Each replacement is an isolated change to one file

## Future Data Updates

### Update Process

When new data is published (e.g., ACS 2023 releases in late 2024):

1. Update the relevant `src/data/*.js` file with new values
2. Update the source metadata (year, URL, accessed date)
3. Run `npm run build` -- Vite bundles the new data
4. Deploy the static build

No database migrations. No API changes. No backend deployment. Just edit a JS file and rebuild.

### Adding New Metrics

To add a new metric (e.g., Medicaid enrollment rate per state):

1. Add the field to each entry in `STATE_DATA`
2. Add a color mapping function to `src/utils/colors.js`
3. Add source metadata to `STATE_SOURCES`
4. Update `USMap.jsx` to use the new metric (or add a toggle)

The modular structure means step 1 and step 4 are in different files, reviewed by different people if needed.

### Adding New Stories

The `HOSPITAL_PEOPLE` array in `hospital.js` is designed to grow. Each person is a self-contained object with `id`, `name`, `role`, `timeline[]`. Adding a 5th person:

1. Add the object to the array in `hospital.js`
2. Position them in the hospital scene (adjust `x`, `y` coordinates)
3. No changes to PersonStory.jsx needed -- it renders any person object with the standard shape

## Anti-Patterns

### Anti-Pattern 1: Fetching Data at Runtime

**What people do:** Move data to JSON files in `public/` and `fetch()` them at runtime
**Why it's wrong:** Introduces loading states, error handling, CORS issues, and caching concerns -- all for data that is known at build time. The project requirement is "fully static."
**Do this instead:** Import data as ES modules. Vite bundles them into the JS output. Zero runtime overhead.

### Anti-Pattern 2: Single Data File

**What people do:** Create one `data.js` file with everything (states, districts, paths, people)
**Why it's wrong:** Same problem as the current monolith. Different data comes from different sources, updates at different times, and is consumed by different components.
**Do this instead:** One file per data domain (states, districts, paths, hospital). Keep the mapping between "where does this data come from" and "which file do I edit" obvious.

### Anti-Pattern 3: Over-Engineering State Management

**What people do:** Add Redux/Zustand/Context for 7 pieces of UI state in a read-only visualization
**Why it's wrong:** The app has no forms, no user-generated data, no server state to sync. Adding a state library increases bundle size and cognitive overhead for zero benefit.
**Do this instead:** Keep useState in App.jsx. If prop drilling becomes painful (it won't with 4 zoom levels), add a single React Context for zoom state.

### Anti-Pattern 4: Premature Component Library

**What people do:** Extract every SVG element into its own component (HexCell, DistrictPath, TimelineEvent, etc.)
**Why it's wrong:** These are not reusable across projects. Over-componentization makes data flow harder to trace in an SVG-heavy app where coordinates and transforms are tightly coupled.
**Do this instead:** Split by zoom level (USMap, NYCMap, HospitalScene, PersonStory). Within each component, inline SVG rendering is fine. Only extract truly shared primitives (Pulse, PersonFig).

### Anti-Pattern 5: TypeScript Conversion During Data Migration

**What people do:** Convert to TypeScript at the same time as replacing data and splitting files
**Why it's wrong:** Three risky changes at once. Each is individually safe; combined they create a merge nightmare.
**Do this instead:** Complete the split and data replacement in JavaScript first. TypeScript conversion (if desired) is a separate future phase.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| Current (4 zoom levels, ~60 districts, 51 states) | No changes needed. Everything fits in a single bundle under 200KB. |
| Adding 5-10 more metrics per state | Still fine as inline data. Estimated <100KB of data total. |
| Adding all US counties (~3,200) | Would need code-splitting: lazy-load county data only when a state is clicked. Use `React.lazy()` + dynamic `import()`. |
| Interactive filtering/comparison tools | Would need state management upgrade (Context or Zustand) for filter state. Still no backend. |

### First Bottleneck

**SVG rendering performance** -- not data size. If the NYC map adds more interactive elements (hover effects on 59 districts with animations), the SVG DOM gets heavy. Mitigation: debounce hover handlers, use CSS transitions instead of React re-renders for hover effects.

### Second Bottleneck

**Bundle size** if substantially more data is added. Mitigation: Vite's built-in code splitting via dynamic imports. Not needed now, but the modular file structure makes it trivial to add later.

## Build Order Implications for Roadmap

The architecture recommends this phase ordering:

**Phase 1: File structure split (no data changes)**
- Extract data files and components from monolithic App.jsx
- Every commit preserves visual parity -- app looks identical
- This is low-risk, high-value scaffolding

**Phase 2: Replace data per zoom level (independent tracks)**
- US state data (CDC, Census, MIT Election Lab)
- NYC district data (NYC DOH, Board of Elections)
- Hospital/person stories (composite from documented cases)
- These can proceed in parallel once the file structure is in place

**Phase 3: Source citations and UI polish**
- Add source footer/overlay to the UI
- Adjust color scales if real data distributions differ from fake data
- Update any visualizations that need to accommodate new data shapes

**Why this order:** The split must come first because replacing data in a monolithic file is error-prone and untestable. Once files are separated, each data domain can be researched and updated independently without merge conflicts.

## Sources

- Vite documentation on static asset handling and code splitting (vite.dev)
- React documentation on component composition and state management (react.dev)
- Direct analysis of existing `src/App.jsx` (567 lines, commit 8a30d88)

---
*Architecture research for: Civic Health Alliance data evolution*
*Researched: 2026-03-10*
