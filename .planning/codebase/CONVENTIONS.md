# Coding Conventions

**Analysis Date:** 2026-03-24

## Naming Patterns

**Files:**
- Use PascalCase for component files: `App.jsx`
- Use camelCase for entry points: `main.jsx`
- Use `.jsx` extension (not `.tsx`) for all component files
- Config files at project root use camelCase: `vite.config.js`, `eslint.config.js`
- CSS files use kebab-case matching their component: `App.css`, `index.css`

**Functions/Components:**
- PascalCase for React components: `USMap`, `NYCMap`, `PersonFig`, `HospitalScene`, `PersonStory`, `NavBar`, `LeftPanel`, `Pulse`
- camelCase for utility functions: `leColor()`, `engagementColor()`
- camelCase for callback props with `on` prefix: `onHover`, `onZoomNYC`, `onClickPerson`, `onHoverDept`, `onPrev`, `onNext`

**Variables/Constants:**
- UPPER_SNAKE_CASE for top-level data constants: `STATE_DATA`, `HEX_GRID`, `CD_PATHS`, `NYC_DISTRICTS`, `HOSPITAL_PEOPLE`, `DEPARTMENTS`, `CHAPTERS`
- UPPER_SNAKE_CASE for color palette constants: `RED`, `CREAM`, `CREAM2`
- camelCase for derived/mapping objects: `sevColor`, `typeColor`, `panelCard`
- camelCase for state variables with abbreviated hover prefix: `hovState`, `hovHood`, `hovPerson`, `hovDept`
- Props use longer descriptive names: `hoveredPerson`, `hoveredDept`, `selectedPerson`
- Single-letter or very short variable names in tight computation scope: `R`, `H`, `pX`, `pY`, `oX`, `oY`, `cx`, `cy`, `d`, `st`, `isH`, `isNY`

**Data Fields:**
- Short abbreviated keys in data objects: `le` (life expectancy), `vt` (voter turnout), `p` (SVG path), `cx`/`cy` (center coordinates), `w`/`h` (width/height)
- Full words for string/descriptive fields: `name`, `borough`, `voterTurnout`, `uninsured`, `lifeExp`, `label`, `note`
- Boolean fields use `is` prefix: `isTarget`

## Code Style

**Formatting:**
- No Prettier or formatter configured
- Double quotes for JSX/component file strings: `import { useState } from "react"`
- Single quotes in config files: `import js from '@eslint/js'`
- Semicolons present in `src/App.jsx` but omitted in config files and `main.jsx`
- Compact code style — multiple statements on one line separated by semicolons:
  ```jsx
  setHovState(null);setHovHood(null);setHovPerson(null);setHovDept(null);
  ```
- Minimal whitespace in JSX attributes:
  ```jsx
  <circle cx={cx} cy={cy} r={r} fill={color} opacity={0.15}>
  ```

**Linting:**
- ESLint v9 with flat config at `eslint.config.js`
- Extends: `js.configs.recommended`, `reactHooks.configs.flat.recommended`, `reactRefresh.configs.vite`
- Custom rule: `no-unused-vars` with `varsIgnorePattern: '^[A-Z_]'` -- allows unused constants starting with uppercase or underscore
- Targets `**/*.{js,jsx}` files
- Ignores `dist/` directory
- Browser globals enabled via `globals.browser`
- ECMAScript 2020+ with JSX features enabled

**Note:** Run `npm install` before `npm run lint` -- `node_modules` must be present.

## Inline Styling Approach

**All styling is inline.** The codebase uses zero CSS classes on custom components. Every style is passed as a `style={{}}` object.

**Shared style objects** defined at module scope in `src/App.jsx`:

`panelCard` (line 220) -- reusable card base:
```jsx
const panelCard = {
  background: CREAM2,
  border: `1px solid rgba(184,18,14,0.80)`,
  padding: "20px 22px",
};
```

`mapBtnBase` (line 564, inside `NavBar`) -- navigation button base:
```jsx
const mapBtnBase = {
  display: "flex", width: 208, height: 45, flexDirection: "column",
  justifyContent: "center", alignItems: "center",
  border: "none", cursor: "pointer",
  fontFamily: "'Libre Franklin',sans-serif",
  fontSize: 16, fontStyle: "italic", fontWeight: 600,
  lineHeight: "normal", textAlign: "center",
  transition: "background 0.18s,color 0.18s",
  flexShrink: 0,
};
```

**Style object spread pattern:**
```jsx
<div style={{...panelCard, margin:"14px 18px 0", flexShrink:0}}>
```

**Font family is repeated on every text element.** There is no CSS reset that sets a global font. Every `<p>`, `<h3>`, `<span>`, `<text>`, `<button>` explicitly sets its own `fontFamily`:
```jsx
fontFamily: "'Libre Franklin',sans-serif"
// or
fontFamily: "'JetBrains Mono',monospace"
```

**Numeric vs. string dimension values:** Pixel values use numbers (not strings) for properties that accept unitless values: `fontSize: 13`, `fontWeight: 700`, `width: 360`. Padding/margin with multiple values use strings: `padding: "20px 22px"`.

**When adding new styled elements:**
- Use inline `style={{}}` objects -- not CSS classes
- Repeat `fontFamily` on every text element
- Use existing color constants (`RED`, `CREAM`, `CREAM2`) from top of `src/App.jsx`
- Use `panelCard` spread for panel/card containers in the left panel
- Follow the existing numeric-value convention for single-dimension properties

## Color System

**Primary palette constants** (top of `src/App.jsx`, lines 6-8):
```jsx
const RED    = "#B8120E";   // Brand red -- borders, accents, alerts, CTAs
const CREAM  = "#FAF8F4";   // Primary page background
const CREAM2 = "#FBF9F4";   // Panel/card background (slightly different)
```

**Data visualization color scales** -- 4-tier traffic-light system (lines 197-198):
```jsx
function leColor(le) {
  return le >= 86 ? "#15803d" : le >= 82 ? "#65a30d" : le >= 79 ? "#eab308" : "#dc2626";
}
function engagementColor(t) {
  return t >= 70 ? "#15803d" : t >= 64 ? "#65a30d" : t >= 58 ? "#eab308" : "#dc2626";
}
```
Both use the same 4 colors: `#dc2626` (red), `#eab308` (yellow), `#65a30d` (light green), `#15803d` (dark green).

**Severity colors** (line 214):
```jsx
const sevColor = { low: "#a3a3a3", medium: "#f59e0b", high: "#ea580c", critical: RED };
```

**Type colors** (line 215):
```jsx
const typeColor = { health: RED, civic: "#2563eb" };
```

**Text/UI grays used inline (not constants):**
| Hex | Usage |
|-----|-------|
| `#1a1a1a` | Primary heading text, dark backgrounds (navbar, buttons) |
| `#374151` | Body text |
| `#4b5563` | Secondary body text |
| `#6b7280` | Label text |
| `#9ca3af` | Muted text, metadata, source citations |
| `#b0a999` | Very muted decorative text |
| `#d1d5db` | Light UI elements |
| `#e5e1d8` | Border/divider lines |
| `#f0ede6` | Progress bar background |
| `#f5f3ef` | Subtle highlight background |

**When adding new colors:** Use existing constants and the inline gray palette above. Do not introduce new named constants unless establishing a genuinely new semantic category.

## Font System

**Fonts loaded via `<style>` tag inside the App component** (Google Fonts CDN, line 667 of `src/App.jsx`):
```
Libre Franklin: weights 400, 500, 700, 900 (normal), 400, 600 (italic)
JetBrains Mono: weights 400, 600, 700
```

**Usage rules:**
- **Libre Franklin** -- all headings, body text, buttons, labels, narrative content
- **JetBrains Mono** -- numeric data values, dates, borough/district codes, source citations, technical metadata

**When to use which font:**
| Content Type | Font | Example |
|-------------|------|---------|
| Headings | Libre Franklin 900 | Chapter titles, person names |
| Body text | Libre Franklin 400 | Narrative paragraphs |
| Buttons | Libre Franklin 700 | Navigation buttons |
| Labels | Libre Franklin 500-700 | Legend labels, stat labels |
| Data values | JetBrains Mono 700 | `28 day wait`, `84.4 yrs` |
| Metadata | JetBrains Mono 400 | Borough codes, dates |
| Source links | Libre Franklin 400 (9px) | Citation links |

**Note:** `App.css` references Playfair Display but this font is not loaded by the Google Fonts import. The active codebase only uses Libre Franklin and JetBrains Mono.

## Component Patterns

**All function components with hooks.** No class components, no higher-order components, no render props.

**Component declaration style** -- use `function` keyword (not arrow functions):
```jsx
function ComponentName({ prop1, prop2, prop3 }) {
  // logic
  return (
    <div>...</div>
  );
}
```

**Hooks usage in the codebase:**
- `useState` -- 7 state variables in `App` component
- `useCallback` -- wraps the `zoomTo` transition function (with `[]` dependency array)
- No `useEffect`, `useRef`, `useMemo`, `useReducer`, or `useContext` anywhere

**State management -- all centralized in `App`** (lines 639-645 of `src/App.jsx`):
```jsx
const [level, setLevel] = useState(-1);          // Current zoom level (-1 to 3)
const [phase, setPhase] = useState("idle");       // Animation phase: "idle"|"out"|"in"
const [hovState, setHovState] = useState(null);   // Hovered US state code or null
const [hovHood, setHovHood] = useState(null);     // Hovered NYC district id or null
const [hovPerson, setHovPerson] = useState(null); // Hovered person id or null
const [hovDept, setHovDept] = useState(null);     // Hovered department id or null
const [selectedPerson, setSelectedPerson] = useState(null); // Full person object or null
```
State setters are passed directly as props. No context, no Redux, no state management library.

**Conditional rendering patterns:**
```jsx
{level === 0 && (<USMap ... />)}                    // Equality check with &&
{level >= 0 && level <= 2 && (<div>...</div>)}      // Range check
{level === 0 && hovStateData && (<div>...</div>)}   // Level + data presence
{level > 0 && (<button>Back</button>)}              // Comparison with &&
```

**Export pattern:**
- Only `App` is exported: `export default function App()`
- All other components, data, and utilities are module-private (no export keyword)

## Import Organization

**`src/App.jsx` has a single import:**
```jsx
import { useState, useCallback } from "react";
```

**`src/main.jsx` import order:**
```jsx
import { StrictMode } from 'react'          // 1. React core
import { createRoot } from 'react-dom/client' // 2. React DOM
import './index.css'                          // 3. CSS files
import App from './App.jsx'                   // 4. Local components
```

**Path aliases:** None configured. All imports use relative paths.

**No barrel files.** No `index.js` re-exports.

## Error Handling

**Defensive null checks -- no error boundaries or try/catch:**

Guard clauses at component top:
```jsx
function PersonStory({ person }) {
  if (!person) return null;
  // ...
}

function NavBar({ level, onHome, onGoUS, onGoNYC }) {
  if (level < 0) return null;
  // ...
}
```

Inline null guards in `.map()` callbacks:
```jsx
const cd = CD_PATHS[n.id]; if (!cd) return null;
```

Conditional data lookups:
```jsx
const hovStateData = hovState ? STATE_DATA[hovState] : null;
const hovHoodData  = hovHood ? NYC_DISTRICTS.find(n => n.id === hovHood) : null;
```

**Pattern to follow:** Use `if (!x) return null` at the top of components or inside map callbacks. Do not use try/catch for rendering logic. Do not add error boundaries unless handling async or external data failures.

## Animation Patterns

**Two-phase zoom transition** via nested `setTimeout` (line 647 of `src/App.jsx`):
```jsx
const zoomTo = useCallback((nl, pid) => {
  setPhase("out");                              // Start fade-out
  setTimeout(() => {
    setLevel(nl);                               // Switch level during opacity:0
    if (pid !== undefined) setSelectedPerson(pid ? HOSPITAL_PEOPLE.find(p => p.id === pid) : null);
    setHovState(null); setHovHood(null); setHovPerson(null); setHovDept(null);
    setPhase("in");                             // Start fade-in
    setTimeout(() => setPhase("idle"), 450);    // Animation complete
  }, 400);
}, []);
```

**Phase-driven animation style object** (line 658):
```jsx
const anim = phase === "out"
  ? { opacity: 0, transform: "scale(1.05)", transition: "all 0.4s cubic-bezier(0.4,0,1,1)" }
  : phase === "in"
  ? { opacity: 1, transform: "scale(1)", transition: "all 0.45s cubic-bezier(0,0,0.2,1)" }
  : { opacity: 1, transform: "scale(1)" };
```

**SVG SMIL `<animate>` elements** for continuous pulsing (used in `Pulse` component):
```jsx
<animate attributeName="r" from={r} to={r*3.5} dur="2s" repeatCount="indefinite"/>
<animate attributeName="opacity" from={0.5} to={0} dur="2s" repeatCount="indefinite"/>
```

**CSS `@keyframes` defined in `<style>` tag** (line 669):
```css
@keyframes fadeIn { from { opacity:0; transform:translateY(6px); } to { opacity:1; transform:translateY(0); } }
```
Referenced inline: `animation: "fadeIn 0.15s ease-out"` or `animation: \`fadeIn 0.35s ease-out ${i*0.05}s both\``

**Hover effects via `onMouseEnter`/`onMouseLeave` on elements:**
```jsx
onMouseEnter={e => { e.currentTarget.style.background = RED; }}
onMouseLeave={e => { e.currentTarget.style.background = "#1a1a1a"; }}
```

## SVG Patterns

**All visualization rendering uses inline SVG elements in JSX:**

**SVG viewBox conventions:**
| Component | viewBox | Content |
|-----------|---------|---------|
| `USMap` | `"0 0 760 480"` | Hexagonal state grid with `<polygon>` |
| `NYCMap` | `"0 0 110 95"` | Community district `<path>` elements |
| `HospitalScene` | `"0 0 100 96"` | Building layout with `<rect>`, person figures |

**SVG attribute style:** SVG-specific attributes (`fill`, `stroke`, `opacity`, `strokeWidth`) go as JSX props. CSS properties (`fontSize`, `fontFamily`, `cursor`, `transition`) go in `style={{}}`:
```jsx
<polygon points={hex(cx,cy)}
  fill={has ? engagementColor(d.voterTurnout) : "#e8e4dc"}
  stroke={isH ? "#1a1a1a" : "#faf8f3"}
  strokeWidth={isH ? 2.5 : 1.5}
  opacity={has ? (isH ? 1 : 0.82) : 0.3}
  style={{ transition: "all 0.2s" }}
/>
```

## Comments

**Section dividers** use decorative box-drawing blocks:
```jsx
/* ═══════════════════════════════════════════════════════════════════════════
   SECTION NAME
   ═══════════════════════════════════════════════════════════════════════════ */
```

**JSX inline comments** are brief layout indicators:
```jsx
{/* Chapter text box */}
{/* Level 0 -- Voter Turnout Legend */}
{/* Spacer */}
{/* Nav arrows -- flush to bottom */}
{/* ── TITLE PAGE ── */}
```

**No JSDoc, no TSDoc, no function parameter docs.** Keep comments limited to section headers and brief inline layout notes.

## Logging

- No logging framework
- No `console.log`, `console.error`, or `console.warn` statements in the codebase
- Debug via React DevTools and browser inspector

## Module Design

**Monolithic single-file architecture.** `src/App.jsx` (730 lines) contains everything:

| Lines | Content |
|-------|---------|
| 1 | React imports |
| 6-8 | Color constants |
| 13-65 | `STATE_DATA` (50 states) |
| 67-69 | `HEX_GRID` (hex positions) |
| 71-133 | `CD_PATHS` (~80KB SVG path data) |
| 135-195 | `NYC_DISTRICTS` (59 districts) |
| 197-198 | Color utility functions |
| 200-205 | `HOSPITAL_PEOPLE` (4 people with timelines) |
| 207-215 | `DEPARTMENTS`, `sevColor`, `typeColor` |
| 220-224 | Shared style objects |
| 229-237 | `Pulse` component |
| 242-397 | `LeftPanel` component (chapters, legends, hover info, nav) |
| 402-422 | `USMap` component |
| 427-441 | `NYCMap` component |
| 446-465 | `PersonFig` component |
| 467-507 | `HospitalScene` component |
| 512-553 | `PersonStory` component |
| 558-633 | `NavBar` component |
| 638-730 | `App` component (state, transitions, layout) |

**When adding to the monolith:** Place data before components, utility functions near related data, components in dependency order (leaf components first, App last).

## CSS Files (Vestigial)

Two CSS files exist from Vite's scaffolding but are largely unused by the actual application:

- `src/index.css` -- Global resets and base styles. Imported by `main.jsx`. Sets body margin/padding and base colors. The dark theme defaults here conflict with the app's cream theme (overridden by inline styles).
- `src/App.css` -- Contains `.logo`, `.card`, `.read-the-docs` classes from the Vite template. **Not imported by `App.jsx`.** Effectively dead code.

---

*Convention analysis: 2026-03-24*
