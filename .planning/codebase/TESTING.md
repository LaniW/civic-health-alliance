# Testing Patterns

**Analysis Date:** 2026-03-24

## Test Framework

**Status:** No testing framework is configured or in use.

- No test runner installed (no Jest, Vitest, Mocha, etc.)
- No test configuration files (`jest.config.*`, `vitest.config.*`)
- No test files anywhere in the repository
- No test-related npm scripts in `package.json`

**Runner:** None

**Assertion Library:** None

**Run Commands:**
```bash
# No test commands exist. These would need to be added:
npm run lint    # ESLint only -- the sole quality check available
npm run build   # Vite build -- fails on syntax errors and unresolved imports
```

## Linting Configuration

**ESLint v9** with flat config format.

**Config file:** `eslint.config.js`

**Plugins and rulesets:**
- `@eslint/js` -- core JavaScript recommended rules
- `eslint-plugin-react-hooks` -- enforces Rules of Hooks
- `eslint-plugin-react-refresh` -- validates Vite HMR compatibility

**Custom rules:**
```js
rules: {
  'no-unused-vars': ['error', { varsIgnorePattern: '^[A-Z_]' }],
}
```
This allows unused UPPER_CASE constants (like color constants and data that may only be referenced indirectly).

**Language settings:**
- ECMAScript 2020, latest parser features, JSX enabled
- Browser globals (no Node globals)
- ES modules source type

**Ignored paths:** `dist/`

**Run lint:**
```bash
npm install          # Required -- node_modules must exist
npm run lint         # Runs: eslint .
```

**Current lint status:** Cannot run without `npm install` first (node_modules not present in repo). No known lint errors based on code inspection.

## Build Validation

**Vite build** provides basic validation:
```bash
npm run build        # Produces dist/ -- catches syntax errors and broken imports
npm run preview      # Serves dist/ locally for manual verification
```

The build will fail on:
- Syntax errors in JSX
- Missing imports (unresolved modules)
- Invalid JavaScript

The build will NOT catch:
- Runtime errors (null reference, bad data lookups)
- Visual regressions
- Interaction bugs
- Logic errors in color functions or data transformations

## Code Quality Tools

**Configured:**
| Tool | Purpose | Status |
|------|---------|--------|
| ESLint v9 | Static analysis, hooks rules | Configured, runnable |
| Vite build | Syntax/import validation | Configured, runnable |

**Not configured:**
| Tool | Purpose | Recommendation |
|------|---------|----------------|
| Prettier | Code formatting | Not present -- would standardize inconsistent semicolons and quote styles |
| TypeScript | Type checking | Not used -- JSX only, no type safety |
| Vitest | Unit/component testing | Natural fit for Vite projects |
| React Testing Library | Component testing | Standard for React component assertions |
| Playwright/Cypress | E2E testing | Would cover drill-down navigation flows |
| Storybook | Component isolation | Useful for SVG components (`Pulse`, `PersonFig`) |
| Lighthouse CI | Performance/accessibility | Would catch accessibility issues in SVG maps |

## Test File Organization

**Current State:** No test files exist.

**Recommended location pattern** (co-located with source):
```
src/
├── App.jsx
├── App.test.jsx          # App integration tests
├── utils/
│   ├── colors.js         # Extracted color functions
│   └── colors.test.js    # Unit tests for color logic
├── components/
│   ├── USMap.jsx
│   ├── USMap.test.jsx
│   ├── NYCMap.jsx
│   ├── NYCMap.test.jsx
│   └── ...
└── __fixtures__/
    └── testData.js        # Shared test data factories
```

**Naming convention to follow:** `[FileName].test.jsx` for component tests, `[fileName].test.js` for pure function tests.

## Recommended Test Framework Setup

**Install Vitest + React Testing Library:**
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

**Add test config to `vite.config.js`:**
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './src/test-setup.js',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules/', 'src/__fixtures__/'],
    },
  },
})
```

**Create `src/test-setup.js`:**
```js
import '@testing-library/jest-dom';
```

**Add scripts to `package.json`:**
```json
"scripts": {
  "test": "vitest run",
  "test:watch": "vitest",
  "test:coverage": "vitest run --coverage"
}
```

## Component Testability Analysis

### Currently Testable (Pure Functions)

**`leColor(le)`** -- line 197 of `src/App.jsx`
```jsx
function leColor(le) {
  return le >= 86 ? "#15803d" : le >= 82 ? "#65a30d" : le >= 79 ? "#eab308" : "#dc2626";
}
```
Test pattern:
```js
import { describe, test, expect } from 'vitest';

describe('leColor', () => {
  test.each([
    [90, '#15803d'],   // >= 86 -> dark green
    [86, '#15803d'],   // boundary: exactly 86
    [85, '#65a30d'],   // >= 82 -> light green
    [82, '#65a30d'],   // boundary: exactly 82
    [80, '#eab308'],   // >= 79 -> yellow
    [79, '#eab308'],   // boundary: exactly 79
    [78, '#dc2626'],   // < 79 -> red
    [70, '#dc2626'],   // well below
  ])('leColor(%d) returns %s', (input, expected) => {
    expect(leColor(input)).toBe(expected);
  });
});
```

**`engagementColor(t)`** -- line 198 of `src/App.jsx`
Same pattern, thresholds at 70/64/58.

**Blocker:** These functions are not exported. To test them, either:
1. Extract to `src/utils/colors.js` and export (preferred)
2. Use Vitest's `vi.importActual` with module rewiring (fragile)

### Component Test Targets

**`Pulse` (lines 229-237):**
- Simple SVG component, ideal for snapshot testing
- Props: `cx`, `cy`, `r` (default 6), `color` (default RED)
- Verify: renders 2 circles, animate elements present, default props applied

**`PersonFig` (lines 446-465):**
- SVG person figure with 3 variants: `"adult"` (default), `"child"`, `"doctor"`
- Props: `x`, `y`, `color`, `variant`
- Test: correct SVG elements for each variant, transform applied, color propagated

**`PersonStory` (lines 512-553):**
- Null guard: `if (!person) return null`
- Renders person profile + timeline from `person.timeline` array
- Test: null person returns nothing, person data rendered, timeline events mapped correctly, type colors applied

**`NavBar` (lines 558-633):**
- Null guard: `if (level < 0) return null`
- Conditional breadcrumbs for levels 2 and 3
- Button active states based on level
- Test: not rendered at level -1, breadcrumbs appear at levels 2/3, button callbacks fire

**`USMap` (lines 402-422):**
- Renders 50+ hex polygons from `HEX_GRID` + `STATE_DATA`
- Hover highlights and NY state animation
- Test: correct number of polygons, hover callback fired, NY click triggers zoom

**`NYCMap` (lines 427-441):**
- Renders ~59 district paths from `CD_PATHS` + `NYC_DISTRICTS`
- Target district has pulsing indicator
- Test: paths rendered, hover callback, target district click triggers zoom

**`HospitalScene` (lines 467-507):**
- Renders departments as rooms + person figures
- Hover tooltips for people
- Test: 4 departments rendered, 4 people rendered, hover shows tooltip, click fires callback

**`LeftPanel` (lines 248-397):**
- Most complex component -- conditional rendering based on level (0, 1, 2)
- Shows different legends and hover info per level
- Calculates best/worst life expectancy from `NYC_DISTRICTS`
- Test: chapter text matches level, legend varies by level, hover data displays

**`App` (lines 638-730):**
- Integration test target -- orchestrates all state and transitions
- `zoomTo` callback with setTimeout-based animation phases
- Test: starts at level -1, BEGIN button transitions to level 0, navigation between levels works

### Test Challenges

**Monolithic file:** All components are unexported private functions inside `src/App.jsx`. Testing individual components requires either:
1. **Extraction** -- move components to separate files and export them (the `.planning/ROADMAP.md` plan)
2. **Integration-only testing** -- test through the App component, which is the only export

**setTimeout animations:** The `zoomTo` function uses nested `setTimeout(cb, 400)` then `setTimeout(cb, 450)`. Tests must use `vi.useFakeTimers()` or `waitFor()` with appropriate timeouts.

**SVG interactivity:** SVG elements use `onMouseEnter`/`onMouseLeave` which are harder to simulate than standard DOM events. Use `fireEvent.mouseEnter()` from Testing Library.

**Large embedded data:** `CD_PATHS` is ~80KB of SVG path strings. Snapshot tests of components using this data will produce enormous snapshots. Consider testing with a subset or mocking the path data.

## Mocking Guidance

**Do NOT mock:**
- `STATE_DATA`, `NYC_DISTRICTS`, `HOSPITAL_PEOPLE`, `DEPARTMENTS` -- use real data for accuracy tests
- Color functions -- test actual output values
- React hooks -- test through component behavior

**Mock when needed:**
- `setTimeout` -- use `vi.useFakeTimers()` to test zoom transitions
- Browser APIs if added (fetch, localStorage, geolocation)
- `CD_PATHS` data in snapshot tests (replace with minimal stub paths)

**Mock pattern for callbacks:**
```js
const onHover = vi.fn();
const onZoomNYC = vi.fn();
render(<USMap hovered={null} onHover={onHover} onZoomNYC={onZoomNYC} />);
fireEvent.mouseEnter(screen.getByText('NY').closest('g'));
expect(onHover).toHaveBeenCalledWith('NY');
```

## Test Data Factories

**Recommended `src/__fixtures__/testData.js`:**
```js
export function makePerson(overrides = {}) {
  return {
    id: 'test-person',
    name: 'Test Person',
    role: 'Test Role',
    x: 50, y: 50,
    color: '#e74c3c',
    age: 30,
    bg: 'Test background.',
    timeline: [
      { date: 'Jan 2023', type: 'health', title: 'Test event', detail: 'Test detail.' },
    ],
    ...overrides,
  };
}

export function makeDistrict(overrides = {}) {
  return {
    id: 'BK01',
    name: 'Test District',
    borough: 'Brooklyn',
    le: 84.4,
    vt: 42.6,
    ...overrides,
  };
}

export function makeDepartment(overrides = {}) {
  return {
    id: 'test-dept',
    label: 'Test Dept',
    x: 14, y: 22, w: 30, h: 28,
    wait: 28,
    severity: 'high',
    note: 'Test note',
    ...overrides,
  };
}
```

## Coverage

**Current:** Zero test coverage. No coverage tool configured.

**Suggested targets (when tests are added):**
| Category | Target | Rationale |
|----------|--------|-----------|
| Color utility functions | 100% | Pure functions, easy to test, critical for data viz correctness |
| Data integrity checks | 100% | Verify all 50 states, 59 districts, 4 people have valid fields |
| Component null guards | 100% | Every `if (!x) return null` path should be tested |
| Component rendering | 80%+ | SVG complexity makes full coverage impractical |
| Navigation/transitions | 90% | Core user flow, high-value tests |
| Hover interactions | 70%+ | SVG events are harder to simulate |

## Test Types

### Unit Tests (Priority: High)

**Scope:** Pure functions and data validation

**What to test:**
- `leColor()` and `engagementColor()` return correct colors at all boundaries
- `sevColor` and `typeColor` maps contain expected values
- All entries in `STATE_DATA` have `name`, `voterTurnout`, `uninsured`, `lifeExp`
- All entries in `NYC_DISTRICTS` have `id`, `name`, `borough`, `le`, `vt`
- All entries in `HOSPITAL_PEOPLE` have `id`, `name`, `role`, `timeline` (non-empty array)
- Every `NYC_DISTRICTS` entry with an `id` has a corresponding `CD_PATHS` entry

### Component Tests (Priority: Medium)

**Scope:** Individual component rendering and interactions

**What to test:**
- `PersonStory` renders null for null person, renders timeline for valid person
- `NavBar` renders null at level -1, shows correct breadcrumbs per level
- `PersonFig` renders correct variant SVG (child vs adult vs doctor)
- `Pulse` renders animated circles

### Integration Tests (Priority: Medium)

**Scope:** Full App component user flows

**What to test:**
- App renders title screen at startup (level -1)
- Clicking BEGIN transitions to US map (level 0)
- Clicking New York transitions to NYC map (level 1)
- Clicking pulsing target transitions to hospital (level 2)
- Clicking a person transitions to story (level 3)
- Back buttons navigate in reverse
- NavBar buttons allow direct navigation

**Async pattern for animation tests:**
```js
import { render, screen, act } from '@testing-library/react';
import { vi } from 'vitest';

test('BEGIN button navigates to US map', async () => {
  vi.useFakeTimers();
  render(<App />);

  // Level -1: title page
  fireEvent.click(screen.getByText('BEGIN'));

  // Fast-forward through animation phases
  act(() => { vi.advanceTimersByTime(400); });  // fade-out completes
  act(() => { vi.advanceTimersByTime(450); });  // fade-in completes

  // Level 0: US map should be visible
  expect(screen.getByText('United States Map')).toBeInTheDocument();

  vi.useRealTimers();
});
```

### E2E Tests (Priority: Low)

**Not configured.** Consider Playwright for full drill-down flow testing if the app grows more complex or gains API integrations.

## Gaps in Quality Tooling

**Critical gaps:**
1. **No automated tests** -- any code change can silently break rendering, interactions, or data display
2. **No type checking** -- JavaScript-only means no compile-time safety for prop shapes or data structures
3. **No formatting enforcement** -- inconsistent semicolons, quote styles, spacing across files

**Moderate gaps:**
4. **No accessibility testing** -- SVG maps and interactive elements have no ARIA labels, no keyboard navigation
5. **No visual regression testing** -- data visualization changes could go unnoticed
6. **No CI pipeline** -- no automated checks on push or PR

**Low-priority gaps:**
7. **No bundle size monitoring** -- the ~80KB of SVG path data in `CD_PATHS` dominates bundle size
8. **No performance monitoring** -- 59 district paths re-rendering on hover could cause jank

## Recommended Testing Roadmap

**Phase 1 -- Foundation (before any refactoring):**
1. Install Vitest + React Testing Library
2. Write unit tests for color functions (extract to `src/utils/colors.js`)
3. Write data integrity tests (validate all datasets)
4. Write integration test for full navigation flow

**Phase 2 -- Component extraction testing (during ROADMAP decomposition):**
1. As components are extracted to separate files, add co-located `.test.jsx` files
2. Test each component's props, rendering, and interactions
3. Add snapshot tests for SVG components (with small test data)

**Phase 3 -- Quality hardening:**
1. Add Prettier for formatting consistency
2. Configure TypeScript (or JSDoc types) for prop validation
3. Add accessibility testing with axe-core
4. Set up CI (GitHub Actions) running lint + test + build

---

*Testing analysis: 2026-03-24*
