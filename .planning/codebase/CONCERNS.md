# Codebase Concerns

**Analysis Date:** 2026-03-10

## Tech Debt

**Monolithic Single-File Architecture:**
- Issue: Entire application logic (567 lines) is contained in `src/App.jsx`, mixing data, UI components, and state management
- Files: `src/App.jsx`
- Impact:
  - Impossible to test individual components in isolation
  - State management scattered across multiple `useState` calls without clear organization
  - Makes code difficult to reuse, maintain, and reason about
  - Performance degradation as component grows
- Fix approach: Extract components into separate files (`USMap.jsx`, `NYCMap.jsx`, `HospitalScene.jsx`, `PersonStory.jsx`, `InfoPanel.jsx`), move data constants to separate modules (`src/data/stateData.js`, `src/data/nycDistricts.js`, `src/data/hospitalPeople.js`), consider state management pattern (Context API or Zustand)

**Hardcoded Data Embedded in Component:**
- Issue: All application data (STATE_DATA, HEX_GRID, CD_PATHS, NYC_DISTRICTS, HOSPITAL_PEOPLE, DEPARTMENTS) is declared as JavaScript objects inside the component file
- Files: `src/App.jsx` (lines 7-245)
- Impact:
  - No separation between data layer and presentation
  - Impossible to update data without modifying component code
  - No data validation or schema definition
  - CD_PATHS contains complex SVG path strings that are fragile and unmaintainable
- Fix approach: Move all data to JSON files in `src/data/` or database, implement data access layer, add JSON schema validation, document data structure

**No React Component File Structure:**
- Issue: Application lacks proper directory organization; only `src/App.jsx`, `src/main.jsx`, one CSS file, and one SVG asset exist
- Files: `src/App.jsx`, `src/main.jsx`
- Impact:
  - New developers cannot find related code
  - No clear patterns for where to add new features
  - Difficulty understanding component relationships
- Fix approach: Create structure: `src/components/`, `src/data/`, `src/hooks/`, `src/styles/`, `src/utils/`, organize each major section into its own module

**Missing Type Safety:**
- Issue: Project uses JSX without TypeScript; no prop validation, return type hints, or type definitions
- Files: `src/App.jsx`, `src/main.jsx`, `eslint.config.js`
- Impact:
  - Silent bugs from prop mismatches
  - No IDE autocomplete for custom props
  - Refactoring is risky and manual
  - README explicitly recommends TypeScript for production apps
- Fix approach: Migrate to TypeScript, add prop-types as interim solution, add JSDoc type annotations

**Inline Styling Throughout:**
- Issue: Every component uses inline `style={{...}}` objects with hardcoded colors, fonts, sizes
- Files: `src/App.jsx` (virtually all JSX elements)
- Impact:
  - No design system or reusable style constants
  - Changes to spacing, colors, or typography require editing dozens of locations
  - Difficult to maintain consistent visual language
  - No dark mode or theme switching capability
- Fix approach: Extract styles to CSS modules or Tailwind, create design tokens (`src/styles/tokens.js`), create style utility functions

## Missing Critical Features

**No Testing Infrastructure:**
- Problem: Zero test files, no test runner configuration (Jest, Vitest not configured)
- Files: None (no test suite)
- Risk: Cannot verify behavior changes, refactoring is dangerous, data visualization logic has no validation
- Priority: High - Complex visualization logic (hex map coordinates, color mappings, zoom animations) lacks coverage

**No Error Handling:**
- Problem: No error boundaries, try-catch blocks, or error states for data validation
- Files: `src/App.jsx`
- Risk: If data becomes malformed, app crashes silently; user sees blank screen with no feedback
- Example: `NYC_DISTRICTS.find()` will return undefined if ID doesn't exist (line 461), causing "Cannot read property" errors

**No Loading States:**
- Problem: Application assumes all data is available immediately; no loading indicators for initial render
- Files: `src/App.jsx` (level state starts at -1, jumps to 0)
- Risk: If this becomes dynamic data fetching in future, user sees blank screen during load

**No Data Validation:**
- Problem: No schema validation for CD_PATHS, STATE_DATA, or NYC_DISTRICTS
- Files: `src/App.jsx` (lines 7-192)
- Risk: Single missing or malformed data entry breaks entire visualization

## Fragile Areas

**Complex SVG Path Data (CD_PATHS):**
- Files: `src/App.jsx` (lines 65-129)
- Why fragile:
  - 80+ KB of minified SVG path strings with no documentation of format
  - Each district is defined as `{p:"...",cx:...,cy:...}` with no schema validation
  - Centers (cx, cy) must be manually calculated and kept in sync with paths
  - Missing or corrupt path data silently fails - district doesn't render
- Safe modification:
  - Never manually edit path strings - regenerate from SVG source
  - Write validation to check all districts have paths and centers
  - Add unit tests for path parsing: `describe('CD_PATHS', () => { test('all districts have valid paths') })`
- Test coverage: Zero tests for SVG coordinate systems or path rendering

**Hex Grid Coordinate System (HEX_GRID):**
- Files: `src/App.jsx` (lines 61-63, used in USMap line 265-267)
- Why fragile:
  - Column-row mapping has no visual reference
  - Coordinate calculation involves magic numbers: `R*Math.sqrt(3)`, `R*2.05`, `pY*0.88`, `row%2===1?pX/2:0`
  - Adding or moving a state requires updating both HEX_GRID and understanding hex math
  - No validation that all 50 states are present
- Safe modification:
  - Store hex grid as visual diagram in comments
  - Add `console.warn()` if state count doesn't equal 50
  - Validate all US states are present: `Object.keys(HEX_GRID).length === 50`
- Test coverage: No tests for hex coordinate calculations or wraparound behavior

**Timeline Data Structure (HOSPITAL_PEOPLE[].timeline):**
- Files: `src/App.jsx` (lines 198-238)
- Why fragile:
  - Timeline animations use magic value `i*0.05` for stagger timing (line 430)
  - Date strings are unstructured strings, no parsing
  - Type field values ("health", "civic") must match typeColor object exactly (line 248)
  - Missing timeline entry breaks person story rendering
- Safe modification:
  - Parse dates to Date objects and validate ranges
  - Add enum validation: `if(!["health","civic"].includes(t.type)) throw new Error()`
  - Document stagger timing calculation
- Test coverage: Zero tests for timeline rendering or date parsing

**Animation Timing Constants:**
- Files: `src/App.jsx` (lines 504-505, 430, 522)
- Why fragile:
  - Multiple hardcoded setTimeout delays (400ms, 450ms) with no explanation
  - CSS animation durations (2s, 0.35s, 0.4s, etc.) must be manually coordinated
  - If one timing changes, others may become out of sync
  - No buffer between animation phases - could cause flicker
- Safe modification:
  - Extract all timing to `src/constants/animations.js`: `ZOOM_OUT_DURATION: 400, LEVEL_CHANGE_DURATION: 450, PHASE_COMPLETE_DURATION: 450`
  - Document why each duration exists
  - Use these constants throughout the file instead of magic numbers
- Test coverage: No tests for animation sequencing or timing

**Color Mapping Functions:**
- Files: `src/App.jsx` (lines 194-196, 250)
- Why fragile:
  - `leColor()` uses magic number thresholds (86, 82, 79) with no explanation (why these numbers?)
  - `engagementColor()` uses different thresholds (70, 64, 58) - inconsistent logic
  - Color values hardcoded in multiple places (typeColor, sevColor objects)
  - No color blindness accessibility validation
- Safe modification:
  - Create `src/utils/colorMap.js` with documented thresholds
  - Add function parameters for threshold customization
  - Add comments: `// 86+ years in top quartile`
  - Test with simulation: https://www.color-blindness.com/coblis-color-blindness-simulator/
- Test coverage: No tests for color threshold logic

## Performance Bottlenecks

**Inline SVG Rendering Without Optimization:**
- Problem: USMap renders all 50 states on every hover, without memoization
- Files: `src/App.jsx` (lines 271-278)
- Cause: No React.memo() wrapper, no useMemo() for hex calculations
- Current impact: Minimal (only 50 polygons), but noticeable on slower devices
- Improvement path:
  - Wrap USMap in React.memo()
  - Extract hex polygon generation to useMemo()
  - Use `shouldComponentUpdate` pattern or React 19's `use` directive
  - Consider SVG optimization tools (SVGO) on CD_PATHS

**Full Page Re-renders on State Changes:**
- Problem: Every hover event triggers full app re-render
- Files: `src/App.jsx` (lines 489-495)
- Cause: Six separate useState calls at root level, all trigger parent re-render
- Current impact: Fine for desktop, noticeable on mobile/tablets
- Improvement path:
  - Use useCallback() for all event handlers (partially done, line 497)
  - Implement Context or useReducer to batch state updates
  - Move hover state to individual components instead of root
  - Profile with React DevTools Profiler

**Large Embedded Data in Component**
- Problem: 567-line file with ~300 lines of data definitions means all must be parsed on mount
- Files: `src/App.jsx`
- Cause: Data embedded instead of lazy-loaded or stored in database
- Current impact: Acceptable (~100KB JS), but no caching strategy
- Improvement path:
  - Move data to separate `.json` files
  - Lazy load levels (only load NYC data when needed)
  - Consider static site generation (SSG) if data is static

## Security Considerations

**External Font Loading from Google Fonts:**
- Risk: Fonts loaded from googleapis.com in CSS @import (line 520)
- Files: `src/App.jsx` (line 520)
- Current mitigation: Font API is public and widely used
- Recommendations:
  - Consider self-hosting fonts for offline capability
  - Add font-display: swap to prevent FOUT (Flash of Unstyled Text)
  - Document font loading in accessibility notes

**No Content Security Policy (CSP):**
- Risk: No CSP headers defined; allows inline styles and scripts
- Files: None (deployment config missing)
- Current mitigation: Single-file app, no user input
- Recommendations:
  - Add CSP header: `default-src 'self'; font-src 'self' fonts.googleapis.com; style-src 'self' 'unsafe-inline'`
  - Note: Inline styles make CSP stricter; another reason to extract styles

**Hardcoded External Links Without Validation:**
- Risk: Links to external sources (LGB MAP, KFF, CDC) in source code (lines 292-294)
- Files: `src/App.jsx`
- Current mitigation: Links are static and to reputable sources
- Recommendations:
  - Move links to configuration file so they can be updated without code release
  - Add link validation tests to ensure URLs are accessible
  - Add `rel="noopener noreferrer"` to external links

**No Input Sanitization (if user-facing):**
- Risk: Person names, timeline dates, and descriptions are not sanitized before rendering
- Files: `src/App.jsx` (PersonStory component, lines 407-448)
- Current mitigation: All data is hardcoded, not user-generated
- Recommendations:
  - If this ever becomes dynamic, add sanitization using DOMPurify
  - Use React's built-in escaping (already done with JSX)
  - Add server-side validation for any dynamic data

## Known Bugs

**Undefined Data Access (Potential Crash):**
- Symptoms: If NYC_DISTRICTS data is updated and a district ID doesn't match a CD_PATH, clicking that district crashes
- Files: `src/App.jsx` (lines 310-311: `if(!cd) return null`)
- Trigger: Add new district to NYC_DISTRICTS without corresponding CD_PATHS entry
- Workaround: Always add matching paths when adding districts
- Impact: Entire NYC map view becomes unusable

**Missing Person in HOSPITAL_PEOPLE Lookup:**
- Symptoms: Clicking person ID that doesn't exist in HOSPITAL_PEOPLE returns null person story
- Files: `src/App.jsx` (line 501: `HOSPITAL_PEOPLE.find(p=>p.id===pid)`)
- Trigger: URL manipulation or data update removing a person
- Workaround: Validate ID before calling zoomTo(3, id)
- Impact: Silent failure - user clicks, nothing happens

**Race Condition in zoomTo Animation:**
- Symptoms: Rapid clicking between zoom levels can cause animation glitches
- Files: `src/App.jsx` (lines 497-506)
- Trigger: Click multiple times before 450ms animation completes
- Workaround: Disable interaction during animation
- Impact: Janky transitions, may show wrong level briefly

## Scaling Limits

**Maximum Data Size:**
- Current capacity: ~1,500 data points (51 states + 32 districts + 60 timeline events)
- Limit: At ~300 lines of data, adding more than 2-3x current data makes file unmaintainable
- If scaling beyond 50 states: Move to database or GraphQL
- If adding more districts: Use GeoJSON instead of manual path strings

**Browser Memory for SVG:**
- Current capacity: CD_PATHS alone is ~80KB; full app is ~150KB
- At 5x scale (~400KB), rendering performance begins to degrade on mobile devices
- Rendering 500+ districts simultaneously causes noticeable lag on older devices
- Scaling approach: Implement viewport-based rendering (render only visible districts)

**Animation Performance:**
- Current capacity: 6 simultaneous animations (pulse, hex glow, stagger effects)
- At 10+ animations: GPU memory exhaustion on low-end devices
- Improvement: Use CSS transforms instead of SVG animate elements (better GPU support)

## Test Coverage Gaps

**No Unit Tests for Color Functions:**
- What's not tested: `leColor()`, `engagementColor()` logic
- Files: `src/App.jsx` (lines 194-196, 250)
- Risk: Color threshold changes break silently without visual indicator
- Priority: Medium - wrong colors don't break functionality, but mislead users

**No Tests for Hex Grid Calculations:**
- What's not tested: Hex coordinate transformations, wraparound behavior
- Files: `src/App.jsx` (lines 265-267)
- Risk: Off-by-one errors in state positioning, coordinate math errors
- Priority: High - visual layout breaks completely if math is wrong

**No Tests for Timeline Rendering:**
- What's not tested: Timeline event filtering, stagger timing, animation sequencing
- Files: `src/App.jsx` (lines 407-448)
- Risk: Malformed timeline data crashes component
- Priority: Medium - affects person story view

**No Integration Tests:**
- What's not tested: Zoom transitions, state persistence across level changes, hover interactions
- Files: `src/App.jsx` (entire app)
- Risk: Cross-level interactions break silently (e.g., selecting person, then going back to map level)
- Priority: High - navigation is critical to UX

**No Accessibility Tests:**
- What's not tested: Color contrast, keyboard navigation, screen reader compatibility
- Files: Entire app
- Risk: Visualization is completely inaccessible to colorblind and screen reader users
- Priority: High - violates WCAG 2.1 standards

---

*Concerns audit: 2026-03-10*
