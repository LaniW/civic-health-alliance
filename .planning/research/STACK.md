# Stack Research

**Domain:** Interactive SVG data visualization — civic/health kiosk SPA (touch navigation + storytelling refinement milestone)
**Researched:** 2026-03-24
**Confidence:** HIGH (existing stack verified against current releases; new patterns are native CSS/HTML with no new dependencies)

---

## Context: Refinement Milestone — No New Dependencies

The app already runs on React 19.2 + Vite 7. PROJECT.md is explicit:

> "Tech stack: React + Vite, no additional dependencies — keep it minimal"
> "Single file: All changes go in `src/App.jsx` for now"

This research answers two questions: (1) what are the current versions and what do they give us, and (2) which specific CSS/HTML/React API patterns should be used for touch navigation, accessible breadcrumbs, and SVG interactivity.

---

## Recommended Stack

### Core Technologies (Already Installed)

| Technology | Version | Purpose | Why It's Right for This Work |
|------------|---------|---------|------------------------------|
| React | ^19.2.0 | UI rendering, state, events | Concurrent renderer is on by default; the existing two-phase fade animation (`zoomTo` 400ms out → state update → 450ms in) is correctly structured for it. `useState`/`useCallback` already used throughout App.jsx |
| Vite | ^7.3.1 | Dev server, HMR, build | Fast HMR is critical when iterating on a 730-line single file. Vite 7's default target is `baseline-widely-available` (features supported for 30+ months), giving broad touch device coverage with no config change |
| ESLint | ^9.39.1 + flat config | Lint rules | `react-hooks/exhaustive-deps` catches stale closures in event handlers — directly relevant when adding breadcrumb callbacks |

React 19.2 (released October 2025, per react.dev) adds `<Activity />` and `useEffectEvent`. Neither is needed for this milestone, but the version is stable and production-ready.

Vite 7 (released 2025) requires Node.js 20.19+ or 22.12+. Node 18 reached EOL in April 2025. Verify the deployment environment before building for production.

**Confidence: HIGH** — versions cross-checked against react.dev and vite.dev official release blogs.

---

### Touch Navigation Patterns (Native CSS/JS — No New Dependencies)

Touch is the core technical need for this milestone. All patterns below are native browser APIs.

#### Pattern 1: `touch-action: manipulation` on all interactive elements

**What it does:** Eliminates the 300ms tap delay that browsers add to detect double-tap-to-zoom. Browsers have supported this since Safari 9.3 / Chrome 36. No library, no JavaScript — one CSS property.

**In React's inline-style system:**
```jsx
// On any tappable element — buttons, SVG hit targets, breadcrumb items
style={{ touchAction: "manipulation", cursor: "pointer" }}
```

Apply globally to `<body>` in `index.css` as well, so the delay is removed everywhere:
```css
/* index.css */
body {
  touch-action: manipulation;
}
```

The viewport meta tag in `index.html` currently reads `initial-scale=1.0` with no `user-scalable` attribute. For a kiosk or presentation context, add `user-scalable=no, maximum-scale=1.0` to prevent accidental pinch-zoom. Omit this for public web deployment (it breaks WCAG SC 1.4.4).

**Confidence: HIGH** — MDN Web Docs, Chrome for Developers "300ms tap delay" post.

#### Pattern 2: `onPointerDown` for SVG hit targets; `onClick` for button elements

React's Pointer Events API (`onPointerDown`) fires immediately on first contact with no 300ms wait and handles mouse, touch, and stylus identically. This is the correct choice for SVG map hit targets where immediacy matters.

Keep `onClick` on `<button>` elements. Screen readers, keyboard users, and Enter/Space key activation all depend on the click event for buttons. Do not replace `onClick` with `onPointerDown` on buttons.

```jsx
// SVG hit targets — use onPointerDown for instant tap response
<polygon
  onPointerDown={() => isNY && onZoomNYC()}
  style={{ cursor: isNY ? "pointer" : "default", touchAction: "manipulation" }}
  ...
/>

// <button> elements — keep onClick (keyboard + accessibility requires it)
<button onClick={onHome} style={{ touchAction: "manipulation" }}>Back</button>
```

**Confidence: HIGH** — MDN Pointer Events API documentation.

#### Pattern 3: `onMouseEnter`/`onMouseLeave` hover → graceful on touch

The existing `onMouseEnter`/`onMouseLeave` handlers on SVG polygons and paths are mouse-only. On touch, hover state never fires. This means the InfoPanel never populates on a touch tap. The fix is to show the relevant info on `onPointerDown` before navigating, or to accept that the drill-down itself is the primary interaction on touch (the InfoPanel shows context about the current level, not the destination level).

Recommendation: accept current behavior for this milestone. Touch users tap to navigate; hover is a mouse enhancement. Do not add `onTouchStart` as a duplicate — use `onPointerDown` for the tap-to-navigate action and leave hover as mouse-only.

**Confidence: HIGH** — common pattern in hybrid touch/mouse interfaces.

---

### Accessible Breadcrumb Navigation (Semantic HTML — No New Dependencies)

The milestone requires tappable breadcrumbs to navigate to any previous level. Use semantic HTML with ARIA. No breadcrumb library is warranted for a 4-level hierarchy.

```jsx
<nav aria-label="Level navigation">
  <ol style={{ display: "flex", listStyle: "none", padding: 0, margin: 0, gap: 4 }}>
    <li>
      <button
        onClick={() => zoomTo(0)}
        aria-current={level === 0 ? "step" : undefined}
        style={{ minHeight: 44, minWidth: 44, touchAction: "manipulation" }}
      >
        US Map
      </button>
    </li>
    {level >= 1 && (
      <li>
        {/* separator — aria-hidden keeps screen readers from reading "/" */}
        <span aria-hidden="true" style={{ padding: "0 4px" }}>/</span>
        <button
          onClick={() => zoomTo(1)}
          aria-current={level === 1 ? "step" : undefined}
          style={{ minHeight: 44, minWidth: 44, touchAction: "manipulation" }}
        >
          NYC
        </button>
      </li>
    )}
    {/* etc. */}
  </ol>
</nav>
```

**Touch target minimum:** 44×44 CSS pixels (WCAG 2.1 SC 2.5.5 Level AAA). WCAG 2.2 SC 2.5.8 (Level AA) requires a minimum of 24×24px with adequate spacing. For a kiosk/presentation display where finger-sized targets are critical, use 44px or larger. The existing NavBar buttons at `padding: "12px 28px"` are close — bump to `padding: "16px 32px"` if the rendered height is under 44px.

**Confidence: HIGH** — W3C WAI ARIA Authoring Practices Guide (breadcrumb pattern), WCAG 2.1 SC 2.5.5, WCAG 2.2 SC 2.5.8.

---

### Animation — Continue Existing Approach

The current animation stack is correct for this use case. No changes warranted.

| Mechanism | Used For | Assessment |
|-----------|----------|------------|
| CSS `@keyframes fadeIn` in injected `<style>` tag | Panel card entrance | Keep — GPU-composited opacity + transform |
| Inline `transition: "all 0.2s"` on SVG polygons | Hover fill/stroke change | Keep — acceptable at ~60 paths; `fill`/`stroke` cause CPU repaints but not noticeable at this scale |
| `opacity` + `transform` two-phase logic in `zoomTo` | Level-to-level transitions | Keep — compositor-thread properties, correct React concurrent model usage |

The existing `transition: "all"` on SVG shapes technically includes non-composited properties (fill, stroke). This causes CPU-bound repaints. For ~60 community district paths it is imperceptible. Narrowing to `transition: "opacity 0.15s"` would be a correct optimization but is out of scope.

**Do not add Framer Motion or React Spring.** The no-new-dependencies constraint is firm. CSS handles the transitions correctly.

**Confidence: HIGH** — MDN animation performance docs, motion.dev Web Animation Performance Tier List.

---

## Supporting Libraries

None required. The constraint prohibits new dependencies and the required patterns are all native.

| Need | Library Option | Reason Not Used |
|------|---------------|-----------------|
| Touch gesture recognition | `@use-gesture/react` | No swipe navigation in spec; only tap/click needed |
| Accessible breadcrumb hook | `react-aria` `useBreadcrumbs` | Adds ~8KB; semantic `<nav>/<ol>/<button>` pattern covers a 4-level hierarchy without it |
| Declarative animations | `framer-motion` | ~34KB; existing CSS transitions are sufficient; no-new-deps constraint |
| SVG touch pan/zoom | `react-zoom-pan-pinch` | Not needed — SVG maps are not zoomable by design |

---

## Development Tools (Unchanged)

| Tool | Version | Purpose | Notes |
|------|---------|---------|-------|
| `@vitejs/plugin-react` | ^5.1.1 | Babel transform, Fast Refresh | Already configured in `vite.config.js`; no changes needed |
| `eslint-plugin-react-hooks` | ^7.0.1 | Hook dependency rules | `exhaustive-deps: warn` catches stale closures in event handlers added for breadcrumb navigation |
| `eslint-plugin-react-refresh` | ^0.4.24 | HMR stability | Warns when non-component exports break Fast Refresh in the large single-file App |

---

## Installation

No new packages. All patterns are native CSS/HTML/React APIs.

```bash
# Verify existing dependencies are current
npm install

# No additions for this milestone
```

---

## Alternatives Considered

| Recommended | Alternative | When Alternative Is Better |
|-------------|-------------|---------------------------|
| `touch-action: manipulation` CSS | FastClick npm library | Never — FastClick is deprecated; the CSS property has universal support since 2015 |
| `onPointerDown` on SVG hit targets | `onTouchStart` + `onClick` dual handler | Never for this project — Pointer Events API unifies both without duplication or ghost-click risk |
| Semantic `<nav>/<ol>/<button>` breadcrumb | React Aria `useBreadcrumbs` | When building a complex multi-level nav system with 10+ levels, dynamic routing, or complex keyboard patterns |
| CSS `@keyframes` + inline `transition` | Framer Motion `<motion.div>` | When you need physics-based spring animations, layout animations (`AnimatePresence`), or drag gestures |
| Custom SVG (existing) | D3.js | When you need complex data-driven layout calculations (force graphs, geographic projections, chord diagrams) — not needed here |

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| FastClick | Deprecated; modern browsers eliminated the 300ms delay via `touch-action`; adds JS bundle weight for zero benefit | `touch-action: manipulation` CSS property |
| `onTouchStart`/`onTouchEnd` for navigation | Fires on scroll initiation; creates ghost-click on some devices when paired with `onClick`; more complex to debounce correctly | `onPointerDown` on SVG targets; `onClick` on `<button>` elements |
| `event.preventDefault()` on all touch events | Breaks browser scroll behavior, accessibility, and default gestures | `touch-action` CSS property handles delay without blocking other gestures |
| Adding D3, Recharts, or Observable Plot | The existing SVG visualizations are fully built; adding a charting library would require rewriting working code; D3 conflicts with React's DOM ownership model | Continue with direct SVG in JSX |
| `framer-motion` / `react-spring` | No-new-dependencies constraint; existing CSS transitions handle the fade animations correctly | CSS `transition:` and `@keyframes` |
| `user-scalable=no` in viewport meta for public deployment | Violates WCAG 2.1 SC 1.4.4 (Resize Text); breaks accessibility for users who need zoom | Use only for dedicated kiosk/presentation deployments; rely on `touch-action: manipulation` for web |

---

## Stack Patterns by Variant

**If deployed as a kiosk on a physical touchscreen:**
- Add `user-scalable=no, maximum-scale=1.0` to the viewport meta tag in `index.html`
- Set `body { touch-action: manipulation; }` globally in `index.css`
- Target 44px+ touch areas on all breadcrumb items and the back arrow button
- Consider adding `-webkit-tap-highlight-color: transparent` to remove the default blue tap flash on iOS/Android WebViews

**If deployed as a public-facing website:**
- Omit `user-scalable=no` (WCAG compliance)
- Keep `touch-action: manipulation` on interactive elements only
- The `onPointerDown` / `onClick` patterns still apply

**If a back arrow button is added to NavBar:**
- Use `<button>` with `aria-label="Go back"` — not a `<div>` or `<span>`
- Minimum rendered size: 44×44px
- Place it consistently at the same position across levels so touch users develop muscle memory

---

## Version Compatibility

| Package | Compatible With | Notes |
|---------|----------------|-------|
| `react@^19.2.0` | `react-dom@^19.2.0` | Must match exactly; already in sync in `package.json` |
| `vite@^7.3.1` | Node.js 20.19+ or 22.12+ | Vite 7 dropped Node 18 (EOL April 2025); verify deployment environment |
| `vite@^7.3.1` | `@vitejs/plugin-react@^5.1.1` | Compatible; no upgrade needed |
| `eslint@^9.39.1` | `eslint-plugin-react-hooks@^7.0.1` | Flat config format (`eslint.config.js`) already in place; no migration needed |

---

## Sources

- [React v19 Blog Post — react.dev](https://react.dev/blog/2024/12/05/react-19) — stable release, concurrent rendering by default (HIGH confidence)
- [React 19.2 Blog Post — react.dev](https://react.dev/blog/2025/10/01/react-19-2) — 19.2 release date and changelog (HIGH confidence)
- [Vite 7.0 Release — vite.dev](https://vite.dev/blog/announcing-vite7) — Node.js requirements, `baseline-widely-available` target (HIGH confidence)
- [touch-action — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action) — `manipulation` value, browser support matrix (HIGH confidence)
- [300ms tap delay, gone away — Chrome for Developers](https://developer.chrome.com/blog/300ms-tap-delay-gone-away) — canonical reference for `touch-action: manipulation` as the fix (HIGH confidence)
- [Pointer Events — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events) — `onPointerDown` unified model for mouse/touch/stylus (HIGH confidence)
- [WCAG 2.5.5 Target Size — W3C WAI](https://www.w3.org/WAI/WCAG21/Understanding/target-size.html) — 44×44px Level AAA requirement (HIGH confidence)
- [WCAG 2.5.8 Target Size Minimum — W3C WAI](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html) — 24×24px Level AA minimum in WCAG 2.2 (HIGH confidence)
- [Breadcrumb Pattern — W3C WAI ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/breadcrumb/examples/breadcrumb/) — `<nav aria-label>`, `aria-current="page"` pattern (HIGH confidence)
- [CSS and JavaScript Animation Performance — MDN](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/CSS_JavaScript_animation_performance) — compositor-thread properties (transform, opacity) vs CPU-bound (fill, stroke) (HIGH confidence)
- [Web Animation Performance Tier List — motion.dev](https://motion.dev/magazine/web-animation-performance-tier-list) — transform/opacity as tier 1; fill/stroke as CPU-bound (MEDIUM confidence — single source, but consistent with MDN)
- [pointer-events SVG attribute — MDN](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/pointer-events) — SVG-specific pointer event handling (HIGH confidence)

---

*Stack research for: Interactive SVG data visualization — touch navigation and storytelling refinement (React + Vite SPA)*
*Researched: 2026-03-24*
