# Codebase Concerns

**Analysis Date:** 2026-03-24

## Tech Debt

**Monolithic Single-File Architecture** (CRITICAL):
- Issue: The entire application (730 lines, 72KB) lives in a single file `src/App.jsx`. This includes all data constants (~21KB of SVG paths in `CD_PATHS`, ~50 state data records, ~59 NYC district records, ~4 person narratives with full timelines), all utility functions, all 9 components, and the root App component. There is zero separation of concerns.
- Files: `src/App.jsx`
- Impact: Every change to any part of the application requires working in a single massive file. Code review is difficult; merge conflicts are inevitable with multiple contributors. Hot module replacement reloads the entire component tree for any edit. The roadmap (Phase 1) already identifies this as the first item to fix.
- Fix approach: Extract into `src/data/states.js`, `src/data/nycDistricts.js`, `src/data/nycPaths.js`, `src/data/hospital.js`, `src/data/stories.js` for data; `src/components/USMap.jsx`, `src/components/NYCMap.jsx`, `src/components/HospitalScene.jsx`, `src/components/PersonStory.jsx`, `src/components/LeftPanel.jsx`, `src/components/NavBar.jsx` for components; `src/utils/colors.js` for color mapping functions. This is already planned as Phase 1 in `.planning/ROADMAP.md`.

**Inline Styles Everywhere** (HIGH):
- Issue: There are 131 `style={{}}` declarations across `src/App.jsx`. The font-family string `"'Libre Franklin',sans-serif"` is repeated 50 times. `"'JetBrains Mono',monospace"` appears 14 times. The color `"#1a1a1a"` appears 19 times. `"#9ca3af"` appears 12 times. There is exactly one shared style object (`panelCard` at line 220); all other styles are raw inline objects, creating new objects on every render.
- Files: `src/App.jsx` (lines 220-730 especially)
- Impact: Massive duplication makes visual consistency fragile. Changing the font or a repeated color requires finding and updating dozens of locations. New objects are allocated on every render (minor perf impact at this scale). Impossible to implement responsive design, pseudo-selectors, or media queries through inline styles alone.
- Fix approach: Extract a design token system (`src/styles/tokens.js`) with constants for fonts, colors, spacing. Create reusable style objects for common patterns (text styles, card styles, button styles). Consider CSS modules or a utility-class approach for responsive breakpoints.

**Embedded Data in Source Code** (HIGH):
- Issue: All application data is hardcoded as JavaScript object literals at the top of `src/App.jsx`. The `CD_PATHS` constant alone is ~21KB of SVG path strings (lines 71-135). `STATE_DATA` is 50 state records (lines 13-65). `NYC_DISTRICTS` is 59 district records (lines 136-195). `HOSPITAL_PEOPLE` contains 4 person narratives with full timeline data (lines 200-205). `DEPARTMENTS` is at lines 207-212. None of this data is sourced from external files or APIs.
- Files: `src/App.jsx` (lines 13-212)
- Impact: The data is bundled into the main JS chunk, increasing initial load. SVG path data is not compressible by tree-shaking. Data updates require code changes and redeployment. No separation between data authoring and code development. Cannot lazy-load data per zoom level.
- Fix approach: Move to separate JSON or JS modules in `src/data/`. For the ~21KB SVG paths, consider a separate JSON file that can be lazy-loaded when the user navigates to the NYC map (Level 1). Static data files can be placed in `public/data/` and fetched on demand for even better code splitting.

**No Test Framework or Tests** (HIGH):
- Issue: Zero test files exist. No test framework is configured. `package.json` has no test script. The color mapping functions (`leColor`, `engagementColor`), data lookups, zoom state machine, and component rendering are completely untested.
- Files: `package.json` (no test dependency), entire `src/` directory (no `.test.` or `.spec.` files)
- Impact: Any refactoring (especially the Phase 1 monolith extraction) risks introducing regressions with no safety net. The color threshold logic in `leColor` and `engagementColor` (line 197-198) uses magic number boundaries that could easily break during data updates. The zoom state machine (`zoomTo` at line 647) uses nested `setTimeout` that is difficult to verify manually.
- Fix approach: Add Vitest (integrates well with Vite). Priority test targets: (1) `leColor` and `engagementColor` threshold functions, (2) `zoomTo` state transitions, (3) snapshot tests for each component at each zoom level before extraction.

**Missing Type Safety** (MEDIUM):
- Issue: Project uses JSX without TypeScript. No PropTypes, no JSDoc type annotations. Component props are untyped: `LeftPanel({level,hovState,hovHood,onPrev,onNext})`, `USMap({hovered,onHover,onZoomNYC})`, etc.
- Files: `src/App.jsx` (all component definitions), `package.json` (has `@types/react` and `@types/react-dom` as devDependencies but no TypeScript compiler)
- Impact: Silent bugs from prop mismatches. No IDE autocomplete for custom props. Refactoring is risky. The presence of `@types/react` devDependencies without TypeScript suggests an incomplete migration or template artifact.
- Fix approach: Either add PropTypes as a quick fix, or migrate to TypeScript (rename `.jsx` to `.tsx`, add `tsconfig.json`). TypeScript is recommended since the type packages are already installed.

**Stale CSS Files from Vite Template** (LOW):
- Issue: `src/App.css` (41 lines) and `src/index.css` (67 lines) contain Vite template boilerplate styles (`.logo`, `.logo-spin`, `.read-the-docs`, `.card` classes) that are never used by the application. The app uses exclusively inline styles. The `index.css` dark mode `color-scheme: light dark` conflicts with the app's fixed cream/red palette.
- Files: `src/App.css`, `src/index.css`
- Impact: Dead code that confuses future developers. The `index.css` sets `background-color: #242424` on `:root` which briefly flashes before React mounts the cream background. The `body { display: flex; place-items: center }` in `index.css` (lines 25-29) may interfere with the full-viewport layout.
- Fix approach: Delete `src/App.css` entirely (it is not imported in `App.jsx`). Replace `src/index.css` with a minimal reset that matches the application's actual design system (cream background, no dark mode).

**Workspace File Committed to Source** (LOW):
- Issue: `src/civic-health-alliance.code-workspace` (7 lines) is a VS Code workspace configuration file committed inside the `src/` directory.
- Files: `src/civic-health-alliance.code-workspace`
- Impact: Should not be in `src/`. May confuse build tools or be included in production bundles.
- Fix approach: Move to project root and add to `.gitignore`, or delete.

## Known Bugs

**Flash of Unstyled Content on Initial Load**:
- Symptoms: Google Fonts are loaded via a runtime `@import url(...)` inside a `<style>` tag rendered by React (line 667). Until fonts download, text renders in system fallback fonts, causing a visible layout shift. The CSS in `index.css` also sets a dark background (#242424) that flashes before React mounts.
- Files: `src/App.jsx` (line 667), `src/index.css` (line 8)
- Trigger: Every first page load, especially on slow connections
- Workaround: None currently in place
- Fix: Move `@import url(...)` to `index.html` `<head>` with `<link rel="preconnect">` and `<link rel="stylesheet">`. Use `font-display: swap`. Fix `index.css` background to match cream palette.

**Zoom State Machine Can Be Interrupted**:
- Symptoms: Rapid clicking through navigation levels can trigger overlapping `setTimeout` chains since there is no guard on the `phase` state during transitions.
- Files: `src/App.jsx` (lines 647-656)
- Trigger: Click "Next" or navigation buttons rapidly during an ongoing transition
- Workaround: The visual effect is minor (some jank), but state could theoretically land on an unexpected level
- Fix: Guard `zoomTo` with a `phase !== "idle"` early return, or use `useRef` to track and cancel pending timeouts.

**Hover State Race Condition During Navigation**:
- Symptoms: The `zoomTo` function clears all hover states (line 652), but because hover events (`onMouseEnter`/`onMouseLeave`) are bound to SVG elements that get unmounted during the fade transition, there is a window where a hover event could fire after the state reset.
- Files: `src/App.jsx` (lines 647-656, 410, 434)
- Trigger: Hovering a state/district and clicking to navigate in the same motion
- Workaround: Defensive null checks (e.g., line 253 `hovHood ? NYC_DISTRICTS.find(...)`, line 432 `if(!cd)return null`) prevent crashes but stale hover data may briefly display in the panel

## Security Considerations

**No External APIs or User Input** (LOW risk):
- Risk: Minimal. The application has no backend, no user input, no authentication, no forms, no fetch calls, no localStorage usage. It is a purely static, read-only data visualization.
- Files: Entire codebase
- Current mitigation: None needed at current scope
- Recommendations: If data is externalized to JSON files in the future, ensure they are served from the same origin. If any user input is added, implement proper sanitization.

**External Font Loading** (LOW):
- Risk: Google Fonts CDN is loaded at runtime via `@import url(...)` at `src/App.jsx` line 667. This creates a dependency on an external service and transmits user IP addresses to Google.
- Files: `src/App.jsx` (line 667)
- Current mitigation: None
- Recommendations: Self-host fonts using `@fontsource/libre-franklin` and `@fontsource/jetbrains-mono` npm packages. This eliminates the external dependency and improves privacy.

**External Links Open Without Noopener** (LOW):
- Risk: Source citation links (`src/App.jsx` lines 284-286, 339-340) use `target="_blank"` without `rel="noopener noreferrer"`. Modern browsers handle this safely, but older browsers could allow the opened page to access `window.opener`.
- Files: `src/App.jsx` (lines 284-286, 339-340)
- Current mitigation: React 19 and modern browsers mitigate this by default
- Recommendations: Add `rel="noopener noreferrer"` to all `target="_blank"` links for defense-in-depth.

## Performance Bottlenecks

**NYC Map Re-sorts 59 Districts on Every Hover** (MEDIUM):
- Problem: The `NYCMap` component (line 431) spreads and sorts the entire `NYC_DISTRICTS` array on every render: `[...NYC_DISTRICTS].sort((a,b)=>(a.id===hovered?1:0)-(b.id===hovered?1:0))`. This creates a new array, copies 59 elements, sorts them, then maps over them to produce SVG paths -- triggered on every mouse move across any district.
- Files: `src/App.jsx` (line 431)
- Cause: The sort ensures the hovered district renders last (on top) in SVG z-order.
- Improvement path: Use `useMemo` to memoize the sorted list based on `hovered`. Or render all non-hovered districts first, then render the hovered one separately outside the loop to guarantee z-order without sorting.

**LeftPanel Recalculates Best/Worst District on Every Render** (LOW):
- Problem: Lines 250-251 run `.reduce()` over the entire 59-district array twice on every render of `LeftPanel`, even when the level is not 1 (NYC map) and the results are not displayed.
- Files: `src/App.jsx` (lines 250-251)
- Cause: No memoization and no conditional execution.
- Improvement path: Move `best` and `worst` to module-level constants (the source data is static and never changes). Alternatively, wrap in `useMemo` with empty deps.

**USMap Recalculates Hex Polygon Points on Every Render** (LOW):
- Problem: The `hex(cx,cy)` function (line 404) computes 6 trigonometric operations per state for each of the ~50 state hexagons on every render. The hex function is re-created inside the component body on every call.
- Files: `src/App.jsx` (lines 403-404)
- Cause: `hex` is defined inside `USMap` and recalculates `Math.cos` and `Math.sin` on every render for static geometry.
- Improvement path: Precompute the 6 unit hex vertex offsets as a module-level constant. Move `hex` outside the component.

**No Component Memoization** (MEDIUM):
- Problem: No component uses `React.memo`, `useMemo`, or `useCallback` (except the single `useCallback` on `zoomTo` at line 647). When any hover state changes in `App`, all child components re-render unnecessarily.
- Files: `src/App.jsx` (lines 638-728, entire component tree)
- Cause: All hover state (`hovState`, `hovHood`, `hovPerson`, `hovDept`) is managed in `App` and flows down as props. Hovering a state on the US map triggers re-render of `LeftPanel` and vice versa.
- Improvement path: Wrap `USMap`, `NYCMap`, `HospitalScene`, `PersonStory`, `LeftPanel` in `React.memo`. Stabilize callback functions passed as props using `useCallback`.

**Google Fonts Loaded at Runtime via React-Rendered Style Tag** (MEDIUM):
- Problem: Two font families (Libre Franklin with 6 weight/style variants, JetBrains Mono with 3 weights) are loaded via `@import url(...)` inside a `<style>` tag rendered by React at line 667. This means font loading begins only after React mounts, adding latency.
- Files: `src/App.jsx` (line 667)
- Cause: Quick setup pattern; not optimized for production.
- Improvement path: Move to `<link rel="preload">` in `index.html` `<head>` for critical fonts. Use `font-display: swap`. Self-host via npm packages (`@fontsource`) and bundle with Vite for zero external dependency.

## Fragile Areas

**Zoom State Machine** (HIGH):
- Files: `src/App.jsx` (lines 638-662)
- Why fragile: The zoom transition relies on nested `setTimeout` calls (400ms out, then 450ms in) with no cleanup mechanism. The `phase` state ("idle", "out", "in") acts as a simple state machine but has no protection against concurrent transitions. The `zoomTo` callback has empty dependency array `useCallback(fn, [])` which is correct for current code but will silently break if it ever needs to reference changing state (like `level`).
- Safe modification: Do not change timing values without testing all level transitions. If adding new levels, verify the animation sequence completes before the next level's content mounts. Consider replacing the `level` + `phase` + `selectedPerson` state group with `useReducer` for atomic updates.
- Test coverage: None. This is the most critical logic in the app and has zero tests.

**Color Threshold Functions** (MEDIUM):
- Files: `src/App.jsx` (lines 197-198)
- Why fragile: `leColor` and `engagementColor` use hardcoded numeric thresholds (`86, 82, 79` for life expectancy; `70, 64, 58` for voter turnout) that determine the 4-tier color scale. These magic numbers are not documented, have no comments explaining why these boundaries were chosen, and will produce misleading visualizations if data ranges change. For example, `engagementColor(58)` returns yellow, but `engagementColor(57.9)` returns red.
- Safe modification: When updating data, recalculate the min/max ranges and verify thresholds still distribute data reasonably. The planned bivariate encoding (Phase 2, requirement STATE-05) will replace these.
- Test coverage: None.

**SVG Path Data Integrity** (MEDIUM):
- Files: `src/App.jsx` (lines 71-135, `CD_PATHS` constant, ~21KB)
- Why fragile: The `CD_PATHS` object maps district IDs to SVG path strings and centroid coordinates (`cx`, `cy`). If any path string is corrupted, truncated, or has its ID mismatched, the NYC map silently renders incorrectly. The guard at line 432 (`if(!cd)return null`) means missing paths render as nothing with no console warning. There is no validation that all 59 `NYC_DISTRICTS` IDs have matching entries in `CD_PATHS`.
- Safe modification: When regenerating SVG paths from GeoJSON, verify district count matches. Add a development-only assertion: `if(process.env.NODE_ENV === 'development') { NYC_DISTRICTS.forEach(d => { if(!CD_PATHS[d.id]) console.error('Missing path:', d.id) }) }`.
- Test coverage: None.

**Inline Style Hover on BEGIN Button** (LOW):
- Files: `src/App.jsx` (lines 694-695)
- Why fragile: The "BEGIN" button uses imperative DOM mutation for hover: `onMouseEnter={e=>{e.currentTarget.style.background=RED;}}` and `onMouseLeave={e=>{e.currentTarget.style.background="#1a1a1a";}}`. This bypasses React's declarative model and can leave the button in a wrong visual state if the mouse leaves during a re-render or if React reconciles the element.
- Safe modification: Replace with a `useState`-based hover state or extract to CSS with `:hover` pseudo-class.
- Test coverage: None.

## Scaling Limits

**Data Volume** (MEDIUM):
- Current capacity: 50 states + 59 NYC districts + 4 person stories + 4 departments. All data is ~30KB of JavaScript.
- Limit: If expanded to include all US counties (~3,100), or all NYC census tracts (~2,168), the inline data approach collapses. SVG path data for tracts alone would be several MB, causing significant parse time and memory usage.
- Scaling path: Move to lazy-loaded JSON data files. Use TopoJSON instead of raw SVG paths (typically 80% smaller through arc sharing). Consider canvas rendering for >500 geographic shapes.

**Component Architecture** (MEDIUM):
- Current capacity: 9 components in one file, manageable for one developer.
- Limit: Adding new zoom levels, new data visualizations, or collaborative features requires major refactoring first. Cannot work on the hospital scene without risk of breaking the US map code, since they share one file.
- Scaling path: Complete Phase 1 (architecture split) before adding any new features. This is the critical path.

## Accessibility Gaps

**Zero ARIA Attributes or Semantic HTML** (CRITICAL):
- Problem: The entire application uses generic `<div>`, `<span>`, `<button>`, `<g>`, `<svg>`, and `<text>` elements with zero `aria-label`, `aria-describedby`, `role`, `aria-live`, or any accessibility attributes. The only `role` attribute in the entire codebase is in the unused React SVG asset (`src/assets/react.svg`). Interactive SVG elements (state hexagons, district paths, person figures) have no accessible names.
- Files: `src/App.jsx` (entire file -- all 730 lines lack accessibility attributes)
- Risk: The visualization is completely inaccessible to screen readers. Hover-dependent information (state stats, district details, person tooltips) cannot be discovered by assistive technology. There is no landmark structure for the page.
- Recommendations:
  - Add `role="img"` and `aria-label` to each `<svg>` element (e.g., `aria-label="United States hexagonal map showing voter turnout by state"`)
  - Add `<title>` and `<desc>` elements inside SVGs for screen readers
  - Add `aria-label` to all interactive elements (state hexagons, district paths, person figures)
  - Add `aria-live="polite"` to the hover info panel in `LeftPanel` so screen readers announce data changes
  - Use semantic landmarks: `<nav>` for NavBar, `<main>` for content area, `<aside>` for LeftPanel

**No Keyboard Navigation** (CRITICAL):
- Problem: All data exploration interactivity is mouse-only. SVG elements use `onMouseEnter`/`onMouseLeave`/`onClick` exclusively. There are 16 mouse event handler bindings and zero keyboard event handlers across the entire application. The `<button>` elements (NavBar, BEGIN, Back/Next) are keyboard accessible by default, but the core data exploration (hovering states, districts, departments, people) is completely mouse-exclusive.
- Files: `src/App.jsx` (lines 410, 434, 486, 494 -- all interactive SVG `<g>` elements)
- Risk: Users who navigate with keyboards, switch devices, or assistive technology cannot explore any of the map or hospital data.
- Recommendations: Add `tabIndex={0}`, `onFocus`/`onBlur` (mirroring `onMouseEnter`/`onMouseLeave`), and `onKeyDown` (Enter/Space for click) to all interactive SVG `<g>` elements. Consider arrow-key navigation between adjacent hexagons or districts.

**No Focus Management on Level Transitions** (HIGH):
- Problem: Level transitions (via `zoomTo`) unmount and remount entirely different content with no focus management. After clicking a state to zoom into NYC, keyboard focus is lost to the document body. There is no skip-to-content link. The LeftPanel and map area have no landmark roles.
- Files: `src/App.jsx` (lines 664-728 -- all level rendering logic)
- Risk: Keyboard users completely lose their place after every navigation action and must Tab through the entire NavBar again.
- Recommendations: Use `useRef` + `useEffect` to programmatically move focus to the new content area after level transitions complete. Add `<a href="#main" class="sr-only">Skip to content</a>` at the top of the page.

**Color-Only Information Encoding** (HIGH):
- Problem: The hex map and NYC district map communicate data exclusively through color. The 4-tier color scales (`leColor`, `engagementColor` at lines 197-198) use red, yellow, light green, dark green -- a palette that is indistinguishable for the ~8% of men with red-green color vision deficiency (deuteranopia/protanopia). Without hovering to reveal numeric values, the maps are unreadable for colorblind users.
- Files: `src/App.jsx` (lines 197-198, 411, 435)
- Risk: A significant portion of the audience cannot distinguish between the best and worst data categories on the map without hovering each element individually.
- Recommendations: Add a secondary visual channel (patterns, hatching, or value labels). Use a colorblind-safe sequential palette (e.g., viridis, cividis) that varies in lightness. Phase 5 in `.planning/ROADMAP.md` already identifies colorblind-accessible palettes as a requirement.

**No `prefers-reduced-motion` Respect** (MEDIUM):
- Problem: SVG SMIL `<animate>` elements (pulse dots at lines 231-234, NY state glow at lines 417-418) run indefinitely with `repeatCount="indefinite"` and no way to disable them. The CSS `@keyframes fadeIn` (line 669) and zoom transitions (lines 658-662, 400ms + 450ms) always animate regardless of user preference.
- Files: `src/App.jsx` (lines 231-234, 417-418, 658-662, 669)
- Risk: Users with vestibular disorders or motion sensitivity may experience discomfort. Indefinitely repeating pulse animations are particularly problematic. Violates WCAG 2.1 SC 2.3.3 (Animation from Interactions).
- Recommendations: Check `window.matchMedia('(prefers-reduced-motion: reduce)')` and conditionally skip SMIL animations (remove `<animate>` elements) and CSS transitions. Provide a visible toggle control. Reduce or eliminate the zoom transition animation when reduced motion is preferred.

## Mobile Responsiveness

**Fixed-Width Layout with No Breakpoints** (HIGH):
- Problem: The root layout uses `width:"100vw",height:"100vh"` (line 665) with a fixed `width:360` pixel sidebar (`LeftPanel` at line 257) beside the map area in a flex row. There are zero responsive breakpoints or `@media` queries in the application code. The title page uses `fontSize:74` (lines 687-688) which overflows on viewports narrower than ~500px. The `maxWidth:"90vw"` on the title container (line 685) helps but does not address font sizing.
- Files: `src/App.jsx` (lines 257, 665, 685-695)
- Risk: The application is unusable on mobile devices. The 360px panel plus any map content requires at minimum ~760px viewport width. On a standard phone (~375px), the panel alone nearly fills the viewport width.
- Recommendations: Stack the panel below the map on viewports under ~768px. Use relative/responsive sizing instead of fixed pixel widths. Scale title font size with `clamp()` or viewport units. Consider a drawer/overlay pattern for the info panel on mobile.

**Touch Interaction Not Supported** (HIGH):
- Problem: All data exploration relies on `onMouseEnter`/`onMouseLeave` hover events, which have no equivalent on touch devices. There is no `onTouchStart`/`onTouchEnd` handling anywhere. The SVG `viewBox` coordinates are small (e.g., NYC map is `0 0 110 95`, hospital scene is `0 0 100 96`) making individual tap targets extremely small on mobile -- a single NYC district path might be 15-20 CSS pixels wide on a phone.
- Files: `src/App.jsx` (lines 406, 429, 469, 493 -- all SVG viewBox declarations and interaction handlers)
- Risk: Mobile users cannot access hover-dependent data (state statistics, district details, department wait times, person tooltips). This effectively makes the application non-functional on touch devices.
- Recommendations: Implement a tap-to-select pattern (tap once to show info, tap elsewhere to dismiss). Increase touch target sizes on small viewports. Consider a list/card-based alternative view for mobile that does not depend on SVG map interaction.

## Dependencies at Risk

**None Critical**: The dependency tree is minimal and healthy -- React 19.2, Vite 7.3, ESLint 9.39. All are actively maintained mainstream packages with no known vulnerabilities.

**CLAUDE.md Documentation Error on Playfair Display Font**:
- Risk: The CLAUDE.md file states "Google Fonts loaded via `<style>` tag: Libre Franklin, JetBrains Mono, Playfair Display" but the actual `@import url` at line 667 only loads Libre Franklin and JetBrains Mono. There are zero references to `"Playfair Display"` anywhere in the source code. This is a documentation inaccuracy, not a wasted resource.
- Impact: Misleading for developers relying on CLAUDE.md. Low severity.
- Fix: Remove the Playfair Display reference from CLAUDE.md.

## Test Coverage Gaps

**Entire Application Is Untested** (CRITICAL):
- What's not tested: Everything. No unit tests, no integration tests, no end-to-end tests, no visual regression tests, no accessibility tests. There is no test runner, no test configuration, no test scripts in `package.json`.
- Files: All of `src/` -- `src/App.jsx` (730 lines), `src/main.jsx` (9 lines)
- Risk: The planned Phase 1 refactoring (monolith extraction into ~12 separate files) has no safety net. Any of the following could break silently: color mapping thresholds, data integrity between `NYC_DISTRICTS` and `CD_PATHS`, zoom state transitions, component rendering at each level, navigation flows.
- Priority: CRITICAL -- must be addressed before or during Phase 1
- Recommended test targets in priority order:
  1. **`leColor()` and `engagementColor()`** -- pure functions with clear inputs/outputs, encode critical visual logic, ~5 tests each
  2. **Data integrity** -- every `NYC_DISTRICTS[].id` has a matching `CD_PATHS` entry; every `STATE_DATA` key appears in `HEX_GRID`; every `HOSPITAL_PEOPLE[].id` is referenced correctly
  3. **Zoom state machine** -- `zoomTo` produces correct `level`/`phase`/`selectedPerson` state sequences; concurrent calls are handled
  4. **Component smoke tests** -- each component (`USMap`, `NYCMap`, `HospitalScene`, `PersonStory`, `LeftPanel`, `NavBar`) renders without errors at each applicable level
  5. **Visual regression snapshots** -- capture after Phase 1 split to catch extraction bugs

---

*Concerns audit: 2026-03-24*
