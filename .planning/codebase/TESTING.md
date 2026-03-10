# Testing Patterns

**Analysis Date:** 2026-03-10

## Test Framework

**Status:** No testing framework currently configured or in use.

**Available Infrastructure (Not Currently Used):**
- No Jest, Vitest, or other test runner installed
- No test configuration files present (`jest.config.js`, `vitest.config.ts`, etc.)
- No test files found in repository (`.test.js`, `.spec.js` patterns absent)

**Testing Infrastructure Recommendation:**
The project uses modern tools that support testing setup:
- **Runtime:** Node.js with ES modules (`"type": "module"` in `package.json`)
- **Build tool:** Vite (`vite`, `@vitejs/plugin-react`)
- **React version:** 19.2.0

Suggested test setup would use:
- **Vitest** (recommended for Vite projects)
- **React Testing Library** (for component testing)
- **@testing-library/user-event** (for user interaction testing)

## Test File Organization

**Current State:** No test files exist.

**Recommended Structure:**
When tests are added, follow these patterns:

**Location:**
- Co-locate tests with source files in `src/` directory
- Alternative: Create parallel `src/__tests__/` subdirectory

**Naming:**
- Pattern: `[ComponentName].test.jsx` for unit/component tests
- Pattern: `[ComponentName].spec.jsx` for integration/behavior tests
- Examples would be:
  - `src/App.test.jsx` - Main App component tests
  - `src/components/USMap.test.jsx` - Individual view component tests
  - `src/utils/colors.test.js` - Utility function tests

## Component Testability Analysis

### Current Component Structure

**File:** `src/App.jsx` (567 lines, single file with all logic)

**Components Ready for Testing:**

1. **`App` (Main Component)**
   - Location: `src/App.jsx` (lines 488-568)
   - What to test:
     - Initial state (level should be -1)
     - Navigation between levels (zoomTo callback)
     - State management (hovState, hovHood, hovPerson, hovDept, selectedPerson)
     - Conditional rendering based on level
   - Dependencies: useState, useCallback
   - Challenge: Large component with mixed concerns

2. **`USMap` (Level 0 View)**
   - Location: `src/App.jsx` (lines 264-298)
   - What to test:
     - SVG rendering with correct hex grid
     - Hover state handling (onHover callback)
     - Click handlers for NY state
     - Hover state effects (opacity, stroke changes)
   - Props: `hovered`, `onHover`, `onZoomNYC`
   - Pure rendering component (can test snapshot + interactions)

3. **`NYCMap` (Level 1 View)**
   - Location: `src/App.jsx` (lines 302-335)
   - What to test:
     - NYC districts rendering
     - Best/worst life expectancy calculation
     - Hover state effects
     - Click handlers for target districts
   - Props: `hovered`, `onHover`, `onZoomCenter`

4. **`HospitalScene` (Level 2 View)**
   - Location: `src/App.jsx` (lines 360-403)
   - What to test:
     - Department rendering with correct severity colors
     - Person figure positioning and color
     - Hover state for persons and departments
     - Click handlers for person selection
   - Props: `hoveredPerson`, `onHoverPerson`, `onClickPerson`, `hoveredDept`, `onHoverDept`

5. **`PersonStory` (Level 3 Detail View)**
   - Location: `src/App.jsx` (lines 407-448)
   - What to test:
     - Null guard (returns null if no person)
     - Person data display (name, role, age, background)
     - Timeline rendering with correct event types
     - Event type color mapping (health vs civic)
   - Props: `person` (can be null)
   - Challenge: Complex nested JSX with timeline data

6. **`PersonFig` (SVG Illustration Component)**
   - Location: `src/App.jsx` (lines 339-358)
   - What to test:
     - Correct SVG elements rendered for variant
     - Color applied correctly
     - Transform/scale applied
   - Props: `x`, `y`, `color`, `variant` (child|adult|doctor)
   - Ideal for snapshot testing

7. **Utility Functions:**
   - `leColor(le)` (line 194): Returns color based on life expectancy
   - `engagementColor(t)` (line 250): Returns color based on threshold
   - `Pulse` component (lines 252-260): Animated pulse indicator

### Suggested Test Cases

**Example: `leColor` function testing**
```javascript
describe('leColor', () => {
  test('returns green for 86+', () => {
    expect(leColor(86)).toBe("#15803d");
  });
  test('returns yellow-green for 82-85', () => {
    expect(leColor(82)).toBe("#65a30d");
  });
  test('returns yellow for 79-81', () => {
    expect(leColor(79)).toBe("#eab308");
  });
  test('returns red for < 79', () => {
    expect(leColor(78)).toBe("#dc2626");
  });
});
```

**Example: `USMap` component testing**
```javascript
describe('USMap', () => {
  test('renders all state hexagons', () => {
    const { container } = render(
      <USMap hovered={null} onHover={() => {}} onZoomNYC={() => {}} />
    );
    // Check for polygon elements
  });

  test('calls onHover when state is hovered', () => {
    const onHover = vi.fn();
    render(
      <USMap hovered={null} onHover={onHover} onZoomNYC={() => {}} />
    );
    // Simulate hover on state hexagon
    // Verify onHover was called with state code
  });

  test('calls onZoomNYC when NY is clicked', () => {
    const onZoomNYC = vi.fn();
    render(
      <USMap hovered={null} onHover={() => {}} onZoomNYC={onZoomNYC} />
    );
    // Simulate click on NY hexagon
    // Verify onZoomNYC was called
  });
});
```

## Mocking Requirements

**Not Currently Used:** No mocking is set up.

**When to Mock:**
- SVG interactions are difficult to test; consider mocking viewport calculations
- Avoid mocking data constants (STATE_DATA, NYC_DISTRICTS, etc.) — use real data in tests
- Mock event handlers passed as props to verify they're called correctly

**Data to Not Mock:**
- `STATE_DATA`: Real census/health data; keep in tests for accuracy verification
- `NYC_DISTRICTS`: Geographic/demographic data; test with real values
- Color mappings: Test actual color output

**What to Mock:**
- Browser APIs if added (localStorage, window.location, etc.)
- External API calls (if added in future)
- React's useState/useCallback behaviors (use vi.spyOn if needed)

## Fixtures and Factories

**Not Currently Used:** No test utilities or factories.

**Recommended Structure (When Implemented):**

Create `src/__tests__/fixtures/data.js`:
```javascript
export const mockPerson = (overrides = {}) => ({
  id: "maria",
  name: "Maria Santos",
  role: "Patient & Parent Advocate",
  age: 34,
  bg: "...",
  timeline: [],
  ...overrides
});

export const mockDistrict = (overrides = {}) => ({
  id: "BK01",
  name: "Williamsburg, Greenpoint",
  borough: "Brooklyn",
  le: 84.4,
  vt: 42.6,
  ...overrides
});
```

Create `src/__tests__/helpers/render.jsx`:
```javascript
import { render } from '@testing-library/react';

export function renderWithProviders(component, options = {}) {
  return render(component, { ...options });
}
```

**Location:** `src/__tests__/fixtures/` and `src/__tests__/helpers/`

## Coverage

**Current:** No coverage configured or measured.

**Recommended Setup (When Tests Added):**

Add to `package.json`:
```json
"scripts": {
  "test": "vitest",
  "test:watch": "vitest --watch",
  "test:coverage": "vitest --coverage"
}
```

Vitest config in `vite.config.js`:
```javascript
export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules/', 'src/__tests__/']
    }
  }
});
```

**Suggested Coverage Targets:**
- Utility functions (leColor, engagementColor): 100%
- Component rendering: 80%+ (SVG complexity makes full coverage challenging)
- Event handlers: 100% (critical for interactivity)
- Data transformations (best/worst calculations): 100%

## Test Types

### Unit Tests

**Scope:** Test individual functions and small components in isolation

**Approach:**
- Test utility functions directly: `leColor()`, `engagementColor()`
- Test simple component rendering: `PersonFig` with different variants
- Verify color mappings and calculations
- Test component prop variations

**Example Areas:**
- Data constants are valid (no undefined values, all required fields present)
- Color functions return expected hex values
- Numeric thresholds work correctly

### Component/Integration Tests

**Scope:** Test components with their interactions and state management

**Approach:**
- Render full view components (USMap, NYCMap, HospitalScene)
- Simulate user interactions (hover, click)
- Verify state updates and callbacks
- Test navigation between levels
- Verify conditional rendering based on level/selection

**Key Interactions to Test:**
- Hovering over states/districts/people triggers visual changes
- Clicking NYC or target district calls zoom callbacks
- Selecting a person displays PersonStory
- Navigation back from story returns to hospital view

### E2E / Full Flow Tests

**Status:** Not applicable for current setup (no E2E framework)

**Consider Adding If:**
- Multi-page navigation grows
- API integration is added
- Complex user workflows need verification

**Tools (Future):**
- Playwright or Cypress could test full user journeys
- Example: "User can navigate from US map → NYC → Hospital → Person story and back"

## Async Testing

**Not Currently Used:** No async operations in components

**When to Test Async:**
- `zoomTo` callback uses `setTimeout` — should test timing
- If API calls are added later

**Pattern (When Needed):**
```javascript
test('navigation completes with correct animations', async () => {
  const { rerender } = render(<App />);
  fireEvent.click(screen.getByText('Begin'));

  // Wait for animation phase to change
  await waitFor(() => {
    expect(screen.getByText(/U.S. Map/)).toBeInTheDocument();
  }, { timeout: 600 });
});
```

## Error Testing

**Current Approach:** Defensive null checks prevent errors gracefully

**Error Cases in Current Code:**
```javascript
// PersonStory.jsx
if(!person) return null;  // Guard clause

// NYCMap.jsx
if(!cd) return null;      // Missing path data guard

// App.jsx - data lookups with optional chaining
HOSPITAL_PEOPLE.find(p=>p.id===pid) ?? null
```

**Error Tests to Add:**
```javascript
test('PersonStory returns null when person is null', () => {
  const { container } = render(<PersonStory person={null} />);
  expect(container.firstChild).toBeNull();
});

test('App handles missing district data gracefully', () => {
  // Test with hovered district that has no CD_PATH
  // Should not crash rendering
});
```

## Data-Driven Testing

**Opportunity:** Test color functions and data mappings with parameterized tests

**Example (Vitest):**
```javascript
describe.each([
  [86, "#15803d"],
  [84, "#65a30d"],
  [80, "#eab308"],
  [75, "#dc2626"]
])('leColor(%i)', (value, expected) => {
  test(`returns ${expected}`, () => {
    expect(leColor(value)).toBe(expected);
  });
});
```

---

*Testing analysis: 2026-03-10*
