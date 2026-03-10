# Coding Conventions

**Analysis Date:** 2026-03-10

## Naming Patterns

**Files:**
- PascalCase for components (React components): `App.jsx`, `USMap`, `NYCMap`
- camelCase for utility/helper functions: `leColor`, `engagementColor`
- All caps for constants: `STATE_DATA`, `HEX_GRID`, `CD_PATHS`, `NYC_DISTRICTS`, `HOSPITAL_PEOPLE`, `DEPARTMENTS`
- CSS files use kebab-case: `index.css`, `App.css`

**Components:**
- React components are arrow functions with PascalCase names: `export default function App()`
- Functional components with destructured props
- Helper components created as named arrow functions: `const Pulse = ({ cx, cy, r = 6, color = "#dc2626" }) => {}`

**Functions:**
- Utility functions use camelCase: `leColor`, `engagementColor`, `zoomTo`, `onHover`
- Event handlers use camelCase with semantic prefixes: `onHover`, `onZoomNYC`, `onClickPerson`, `onHoverPerson`, `onHoverDept`
- Callback functions created with `useCallback`: `const zoomTo = useCallback((nl,pid)=>{...}, [])`

**Variables:**
- State variables use camelCase: `level`, `phase`, `hovState`, `hovHood`, `hovPerson`, `hovDept`, `selectedPerson`
- Abbreviated variable names for local computation: `cx`, `cy`, `d`, `isH`, `isNY`, `st`, `h`, `R`, `pX`, `pY`, `oX`, `oY`
- Configuration objects use camelCase keys: `id`, `name`, `borough`, `le`, `vt`, `bg`, `color`, `variant`

**Constants/Objects:**
- Global data structures in ALL_CAPS: `STATE_DATA`, `NYC_DISTRICTS`, `DEPARTMENTS`
- Color palettes as constant objects: `sevColor`, `typeColor`
- Semantic prefixes for booleans: `has`, `isH`, `isNY`, `bad`

## Code Style

**Formatting:**
- ESLint configured via `eslint.config.js` with flat config format (ESLint v9)
- Target: JavaScript/JSX with React components
- Browser environment configured in `languageOptions`
- ECMAScript 2020 target with support for latest features

**Linting:**
- ESLint with recommended configs:
  - `@eslint/js` (core JS linting)
  - `eslint-plugin-react-hooks` (React Hooks rules)
  - `eslint-plugin-react-refresh` (Vite React Refresh validation)
- Custom rule: Allow unused variables if they start with uppercase (for unused imports/type definitions)
  - `'no-unused-vars': ['error', { varsIgnorePattern: '^[A-Z_]' }]`
- Global ignore: `dist` directory

**Inline Styles:**
- Extensive use of inline styles via object literals passed to `style` prop
- Common style patterns:
  ```jsx
  style={{fontFamily:"'Playfair Display',Georgia,serif",fontSize:14,fontWeight:700,color:"#1a1a1a"}}
  style={{position:"absolute",inset:0,display:"flex",flexDirection:"column"}}
  style={{background:"#dc2626",color:"#fff",padding:"16px 20px"}}
  ```
- Transitions and animations via `transition` property:
  ```jsx
  style={{transition:"all 0.2s"}}
  style={{animation:`fadeIn 0.35s ease-out ${i*0.05}s both`}}
  ```

## Import Organization

**Order:**
1. React imports: `import { useState, useCallback } from "react"`
2. No external dependency imports (minimal dependencies)
3. Local CSS: `import './index.css'`
4. Local components: `import App from './App.jsx'`

**Path Aliases:**
- Not configured; all imports are relative paths

**No Barrel Files:**
- Direct file imports, no index.js re-exports for components

## Error Handling

**Patterns:**
- Defensive null checks on component data: `if(!person) return null;`
- Safe property access: `if(!cd) return null;`
- Fallback rendering for missing data: `{HOSPITAL_PEOPLE.find(p=>p.id===pid)??null}`
- No explicit error boundaries or error handling
- No try-catch blocks in application code
- Silent failures on missing data gracefully return null components

## Logging

**Framework:**
- No logging framework configured
- No console statements used in production code
- No debugging output visible

**Patterns:**
- Implicit logging via browser DevTools required for debugging
- State changes managed via React's state setters without logging
- No debug mode

## Comments

**When to Comment:**
- Section headers using box-drawing characters:
  ```jsx
  /* ═══════════════════════════════════════════════════════════════════════════
     DATA
     ═══════════════════════════════════════════════════════════════════════════ */

  /* ═══════════ LEVEL 0: US HEX MAP ═══════════ */
  /* ═══════════ LEVEL 1: NYC MAP ═══════════ */
  /* ═══════════ LEVEL 2: HOSPITAL ═══════════ */
  /* ═══════════ LEVEL 3: PERSON STORY ═══════════ */
  /* ═══════════ INFO PANEL ═══════════ */
  /* ═══════════ NARRATIVE OVERLAY ═══════════ */
  /* ═══════════ APP ═══════════ */
  ```
- Comments mark logical sections and component levels
- No JSDoc/TSDoc used
- No inline explanatory comments for complex logic

## Function Design

**Size:**
- Small, focused components and functions
- Largest component is `App` (~80 lines of JSX)
- Most helper components 20-40 lines
- Inline utility functions for complex calculations

**Parameters:**
- Destructured props in component signatures: `function USMap({ hovered, onHover, onZoomNYC })`
- Inline default parameters: `function Pulse({ cx, cy, r = 6, color = "#dc2626" })`
- Callback functions use simple parameter names: `(nl, pid)` for next level, person id

**Return Values:**
- Components return JSX directly
- Functions return derived values or null for guards
- Arrow functions for callbacks: `const zoomTo = useCallback((nl,pid)=>{ ... }, [])`

## Module Design

**Exports:**
- Single default export: `export default function App()`
- All other components and functions defined in same file (not exported)
- No module exports besides the App component

**Single File Structure:**
- `App.jsx` contains:
  - Data constants (STATE_DATA, HEX_GRID, CD_PATHS, NYC_DISTRICTS, HOSPITAL_PEOPLE, DEPARTMENTS)
  - Color/styling constants (sevColor, typeColor, engagementColor, leColor)
  - Helper component: `Pulse`
  - 6 view/scene components: `USMap`, `NYCMap`, `PersonFig`, `HospitalScene`, `PersonStory`, `InfoPanel`
  - 1 overlay component: `NarrativeOverlay`
  - 1 main app component: `App`

**File Organization in `src/`:**
```
src/
├── App.jsx           # All React components and logic
├── App.css           # Styling (imported in App)
├── main.jsx          # Entry point (creates root and renders <App />)
├── index.css         # Global styles
└── assets/
    └── react.svg     # Static asset
```

## React Patterns

**State Management:**
- Shallow state with `useState` hooks
- Multiple independent state variables for UI state
- `useCallback` for memoized callbacks (e.g., `zoomTo` function)
- No context API or prop drilling issues (single file structure)

**Component Composition:**
- Functional components only
- Props passed directly (no context)
- Conditional rendering with ternary operators and logical AND
- Render props pattern not used

**SVG Components:**
- Extensive use of SVG for interactive visualizations
- Inline SVG with event handlers
- Dynamic attributes calculated from state/props
- Animations via SVG `<animate>` elements and CSS animations

---

*Convention analysis: 2026-03-10*
