# Codebase Structure

**Analysis Date:** 2026-03-24

## Directory Layout

```
civic-health-alliance/
├── src/                         # All application source code
│   ├── App.jsx                  # Monolithic app (730 lines, 72KB — ALL logic)
│   ├── main.jsx                 # React entry point (10 lines)
│   ├── App.css                  # Vite scaffold CSS (unused by app)
│   ├── index.css                # Base document styles (mostly overridden)
│   ├── assets/                  # Static assets directory
│   │   └── react.svg            # Vite scaffold asset (unused)
│   └── civic-health-alliance.code-workspace  # VS Code workspace file
├── public/                      # Static files served at root
│   └── vite.svg                 # Favicon
├── .planning/                   # GSD planning and documentation
│   ├── PROJECT.md               # Project goals and requirements
│   ├── ROADMAP.md               # 5-phase execution plan
│   ├── REQUIREMENTS.md          # Detailed requirements
│   ├── STATE.md                 # Current project state
│   ├── config.json              # GSD config
│   ├── codebase/                # Codebase analysis docs (this directory)
│   │   ├── ARCHITECTURE.md
│   │   ├── STRUCTURE.md
│   │   ├── CONVENTIONS.md
│   │   ├── TESTING.md
│   │   ├── STACK.md
│   │   ├── INTEGRATIONS.md
│   │   └── CONCERNS.md
│   └── research/                # Pre-development research notes
│       ├── ARCHITECTURE.md
│       ├── FEATURES.md
│       ├── PITFALLS.md
│       ├── STACK.md
│       └── SUMMARY.md
├── index.html                   # HTML shell (Vite entry)
├── package.json                 # Dependencies (React 19, Vite 7)
├── package-lock.json            # Locked dependency tree
├── vite.config.js               # Vite config (React plugin only)
├── eslint.config.js             # ESLint flat config (v9)
├── CLAUDE.md                    # Claude Code project instructions
├── README.md                    # Project readme
└── .gitignore                   # Ignores node_modules, dist, .vercel
```

## Directory Purposes

**`src/`:**
- Purpose: All application JavaScript/JSX source code
- Contains: One monolithic component file, entry point, scaffold CSS
- Key files: `App.jsx` is the only file that matters for application logic
- Note: `App.css` and `index.css` are Vite scaffold leftovers; the app uses inline styles exclusively

**`public/`:**
- Purpose: Static files served verbatim by Vite (no processing)
- Contains: Only `vite.svg` (used as favicon in `index.html`)
- Build behavior: Contents copied to `dist/` root

**`.planning/`:**
- Purpose: GSD workflow documentation (project planning, codebase analysis, research)
- Contains: Markdown docs consumed by planning and execution commands
- Key files: `ROADMAP.md` (5-phase plan), `PROJECT.md` (goals/scope)

**`.planning/codebase/`:**
- Purpose: Machine-generated codebase analysis documents
- Contains: Architecture, structure, conventions, testing, stack, integrations, concerns docs
- Consumed by: `/gsd:plan-phase` and `/gsd:execute-phase` commands

**`.planning/research/`:**
- Purpose: Pre-development research notes about architecture choices, features, pitfalls
- Contains: Research documents informing the roadmap

## Key File Locations

**Entry Points:**
- `index.html`: HTML shell with `<div id="root">` mount point and `<script>` tag loading `src/main.jsx`
- `src/main.jsx`: React root creation, mounts `<App />` inside `<StrictMode>`
- `src/App.jsx`: Default export `App` component (line 638) — the entire application

**Configuration:**
- `vite.config.js`: Minimal config enabling `@vitejs/plugin-react` (no aliases, no custom settings)
- `eslint.config.js`: Flat config (ESLint v9) with react-hooks and react-refresh plugins; allows unused vars matching `^[A-Z_]`
- `package.json`: Scripts (`dev`, `build`, `lint`, `preview`), React 19.2.0 + Vite 7.3.1

**Core Logic (all in `src/App.jsx`):**

| Section | Lines | Content | Approx Size |
|---------|-------|---------|-------------|
| Constants & Palette | 1-9 | `RED`, `CREAM`, `CREAM2` color constants | ~9 lines |
| STATE_DATA | 13-65 | 50 US states: name, voterTurnout, uninsured, lifeExp | ~53 lines |
| HEX_GRID | 67-69 | State hex grid positions `{stateCode: [col, row]}` | ~3 lines |
| CD_PATHS | 71-133 | ~59 NYC district SVG path strings | ~63 lines (~80KB of SVG data) |
| NYC_DISTRICTS | 135-195 | 59 districts: name, borough, le, vt | ~61 lines |
| Color functions | 197-198 | `leColor()`, `engagementColor()` | 2 lines |
| HOSPITAL_PEOPLE | 200-205 | 4 person objects with timelines | ~6 lines (dense) |
| DEPARTMENTS | 207-212 | 4 hospital departments with metadata | ~6 lines |
| Style constants | 214-224 | `sevColor`, `typeColor`, `panelCard` | ~11 lines |
| Pulse component | 229-237 | Animated SVG pulse dot | ~9 lines |
| LeftPanel component | 242-397 | Narrative panel with legends and hover cards | ~156 lines |
| USMap component | 402-421 | Hexagonal US state map | ~20 lines |
| NYCMap component | 427-441 | NYC community district map | ~15 lines |
| PersonFig component | 446-465 | SVG person figure (adult/child/doctor) | ~20 lines |
| HospitalScene component | 467-507 | Hospital interior with rooms and people | ~41 lines |
| PersonStory component | 512-553 | Individual narrative timeline | ~42 lines |
| NavBar component | 558-633 | Top navigation bar with tabs | ~76 lines |
| App component | 638-730 | Root state management and layout | ~92 lines |

**Styling:**
- `src/index.css`: Vite scaffold base styles (68 lines). Sets system font, dark color scheme, button/link styles. Partially overridden by App's inline styles and CSS reset.
- `src/App.css`: Vite scaffold component styles (40 lines). Contains `.logo` animation and `.card` padding. Not used by any current component.
- Inline styles: All actual application styling is via `style={{}}` JSX props throughout `src/App.jsx`

## Naming Conventions

**Files:**
- PascalCase for components: `App.jsx` (only component file)
- camelCase for entry: `main.jsx`
- kebab-case for config: `vite.config.js`, `eslint.config.js`
- lowercase for CSS: `index.css`, `App.css`

**Directories:**
- lowercase singular: `src/`, `public/`
- lowercase plural for collections: `assets/`
- dot-prefixed for tooling/hidden: `.planning/`, `.git/`

**In-code naming (within `src/App.jsx`):**
- SCREAMING_SNAKE_CASE for data constants: `STATE_DATA`, `HEX_GRID`, `CD_PATHS`, `NYC_DISTRICTS`, `HOSPITAL_PEOPLE`, `DEPARTMENTS`, `CHAPTERS`
- PascalCase for React components: `Pulse`, `LeftPanel`, `USMap`, `NYCMap`, `PersonFig`, `HospitalScene`, `PersonStory`, `NavBar`, `App`
- camelCase for functions: `leColor`, `engagementColor`, `zoomTo`
- camelCase for state variables: `hovState`, `hovHood`, `hovPerson`, `hovDept`, `selectedPerson`
- camelCase for style objects: `panelCard`, `mapBtnBase`, `sevColor`, `typeColor`

## Where to Add New Code

**IMPORTANT: Phase 1 of the roadmap (`ROADMAP.md`) plans to decompose the monolith. The guidance below describes the CURRENT state. After Phase 1, new files go in `src/components/` and `src/data/`.**

**New Data Source (e.g., new dataset for a state metric):**
- Location: Top of `src/App.jsx`, after existing data constants (before line 197)
- Pattern: `const NEW_DATA = { ... };` using SCREAMING_SNAKE_CASE
- Format: JavaScript object literal keyed by ID (state code, district ID, etc.)

**New Visualization Component (e.g., new map type):**
- Location: `src/App.jsx`, insert between existing components (before `App` at line 638)
- Pattern: `function NewComponent({props}){ return(<svg viewBox="...">...</svg>); }`
- Add conditional render in `App`'s return JSX: `{level===N && <NewComponent ... />}`
- Wire hover/click via new state variables in `App`

**New Color Mapping Function:**
- Location: `src/App.jsx` line 198 (after existing color functions)
- Pattern: `function colorName(value){ return value>=X?"#color1":value>=Y?"#color2":"#color3"; }`

**New Chapter/Narrative Content:**
- Location: `src/App.jsx` CHAPTERS array at line 242
- Add new entry: `{ch:"Chapter N", title:"...", body:"...", cta:"..."}`
- Index corresponds to zoom level

**New Zoom Level:**
1. Add data constant at top of `src/App.jsx`
2. Add rendering component before `App`
3. Add `useState` for hover state in `App` (line 639-645)
4. Add conditional render block in App JSX (around line 702-728)
5. Update `NavBar` breadcrumb logic
6. Update `LeftPanel` navigation arrows range check (currently `level<2` for Next)

**New Hospital Person:**
- Location: `src/App.jsx` HOSPITAL_PEOPLE array at line 200
- Shape: `{id, name, role, x, y, color, age, bg, timeline: [{date, type, title, detail}]}`
- Also add summary text in `PersonStory` component (line 544-547 switch)

**Static Assets (images, icons):**
- Location: `public/` for files served at root URL
- Location: `src/assets/` for files processed by Vite (import in JS)
- Currently no images are used; all visuals are SVG rendered in JSX

## Special Directories

**`node_modules/`:**
- Purpose: npm dependencies (React, Vite, ESLint, plugins)
- Generated: Yes (`npm install`)
- Committed: No (in `.gitignore`)

**`dist/`:**
- Purpose: Production build output
- Generated: Yes (`npm run build`)
- Committed: No (in `.gitignore`)

**`.vercel/`:**
- Purpose: Vercel deployment metadata
- Generated: Yes (Vercel CLI)
- Committed: No (in `.gitignore`)
- Note: Suggests this project deploys to Vercel

**`.planning/`:**
- Purpose: GSD workflow planning documents
- Generated: Partially (codebase analysis is auto-generated; project docs are human-authored)
- Committed: Yes

---

*Structure analysis: 2026-03-24*
