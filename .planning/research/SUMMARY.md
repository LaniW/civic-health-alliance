# Project Research Summary

**Project:** Civic Health Alliance — Touch Navigation and Storytelling Refinement
**Domain:** Interactive SVG data visualization — civic/public-health narrative kiosk SPA (refinement milestone)
**Researched:** 2026-03-24
**Confidence:** HIGH

## Executive Summary

Civic Health Alliance is a refinement milestone on an existing, working React 19 + Vite 7 SPA. The core four-level drill-down (US map to NYC districts to hospital scene to individual story) is built and functioning. This milestone's purpose is to make the app credible and usable on a touch screen: add back navigation controls and tappable breadcrumbs to NavBar, fix the hospital scene SVG layout, and replace fictional patient stories with real transcript-based content. No new dependencies are warranted — the required patterns (touch-action CSS, Pointer Events API, semantic breadcrumb HTML) are all native browser APIs already available in the existing stack.

The recommended implementation order is: first decouple PersonStory from hard-coded person IDs by adding a `summary` field to the data constants, then add NavBar touch navigation, then fix SVG coordinates in HospitalScene, then replace story content. This order means each change can be independently verified before the next begins, and the riskiest change (content replacement) is done last with all structural coupling already removed. The entire milestone touches only `src/App.jsx` — the project constraint against new files or dependencies is firm.

The key risks are not technical — they are editorial. Real patient transcript content triggers obligations around consent scope, ecological fallacy framing, deficit framing of marginalized communities, and precise immigration-status language. These pitfalls are credibility-destroying if missed and invisible to technical review. They require domain-specific editorial scrutiny before any story content is written, not after. The research identified specific verification requirements (CDC NVSR Volume 74 Number 12 for life expectancy, KFF non-elderly denominator for uninsured rates, MAP VEP-based turnout for the correct election cycle) that must be confirmed before declaring existing data correct.

## Key Findings

### Recommended Stack

The existing stack (React 19.2, Vite 7.3, ESLint 9 flat config) is correct and current. No new packages are needed for this milestone. The no-new-dependencies constraint is firm and consistent with the patterns required. See [STACK.md](./STACK.md) for full details.

**Core technologies:**
- React 19.2: UI rendering with concurrent mode on by default — the existing two-phase fade transition (zoomTo 400ms out, 450ms in) is correctly structured for it; `useState`/`useCallback` already used throughout
- Vite 7.3: Dev server and build — requires Node.js 20.19+ or 22.12+; Node 18 reached EOL April 2025; verify deployment environment before any production build
- `touch-action: manipulation` CSS: Eliminates the 300ms tap delay on all interactive elements — one CSS property, no library, universal browser support since 2015; apply globally to `body` in index.css
- `onPointerDown` on SVG hit targets: Fires immediately on touch without ghost-click risk; unifies mouse, touch, and stylus; keep `onClick` on `<button>` elements for keyboard and accessibility
- Semantic `<nav>/<ol>/<button>` breadcrumb: Covers the 4-level hierarchy without a library; WCAG-compliant with `aria-current="step"`; minimum 44px touch targets on all nav controls

### Expected Features

See [FEATURES.md](./FEATURES.md) for full feature landscape and prioritization matrix.

**Must have (table stakes) — this milestone:**
- Back arrow button in NavBar — kiosk users have no browser back button; dead-end states cause abandonment; non-negotiable
- Tappable breadcrumbs showing current path — users get lost in a 4-level hierarchy without orientation cues; tappable crumbs let users jump multiple levels in one tap
- Hospital scene: person figures centered within department rooms — current layout signals "broken" to first-time viewers
- Hospital scene: text and label overlap resolved — visual polish is credibility for a public-facing civic piece
- Replace fictional stories with real patient transcript content — authenticity is the product's core value; fictional composites next to real CDC/KFF stats undermine credibility
- Verify all left-panel stats against cited primary sources — real stories next to wrong numbers worsens the credibility problem

**Should have (add after validation):**
- Touch target size audit across all map elements — states and districts are visually small; tap areas may need expanded hit zones beyond visual bounds
- "Return to start" home button at Level 3 — breadcrumbs cover this, but an explicit restart CTA reduces cognitive load for kiosk users
- Data vintage dates visible on every metric — "Life expectancy: 78.2 years (NYC DOH, 2022)" rather than a bare number

**Defer to v2+:**
- Hash-based URL routing per level — enables shareable deep links for web deployment; irrelevant for kiosk use case
- WCAG accessibility pass — requires a dedicated multi-sprint effort; SVG ARIA labels and keyboard navigation for 59 districts cannot be done piecemeal without inconsistent partial compliance
- Full mobile responsive layout — this is a kiosk/presentation app; phone viewport optimization is explicitly out of scope per PROJECT.md
- Additional patient stories — valuable after real transcript content is integrated; not urgent for this milestone
- Directional slide transition animations — the current fade works correctly; directional slides reinforce the drill-down metaphor but are a refinement, not a fix

**Anti-features to reject:**
- New zoom levels or data dimensions — breaks the narrative arc and multiplies content maintenance burden
- Real-time data or API integration — invalidates the source-cited static numbers that are the product's credibility foundation
- User filtering and custom queries — this is a narrative product, not an analytics dashboard; free exploration breaks the authorial arc
- User-configurable color themes — the red/green/cream palette is semantically load-bearing for the data encoding; theming breaks it

### Architecture Approach

All changes for this milestone are edits within `src/App.jsx` (730 lines). The component boundary map is clear and the data flow is strictly top-down: App owns all state and passes callbacks down as props. No circular dependencies, no shared mutable state outside App's useState variables. The `zoomTo` transition function is correct as-is and used unchanged by all navigation changes. See [ARCHITECTURE.md](./ARCHITECTURE.md) for the full component map and build order rationale.

**Components and change type for this milestone:**
1. `App` (lines 638–730) — State owner; add one `onBack` prop to NavBar wire; no other changes
2. `NavBar` (lines 558–633) — Add back arrow button (`level > 0` guard); add `onClick` to level 2/3 breadcrumb spans currently rendered as display-only `<span>` elements
3. `PersonStory` (lines 512–553) — Remove hard-coded `person.id === "maria"` switch on summary card; replace with `{person.summary}` from data
4. `HospitalScene` (lines 467–507) — Update person x/y coordinates in `HOSPITAL_PEOPLE` data constants using computed room-center values, not trial-and-error
5. `HOSPITAL_PEOPLE` constant (lines 200–205) — Full content replacement (id, name, role, age, bg, summary, timeline); done last after PersonStory is decoupled

**Recommended build order:** (1) Add summary field + decouple PersonStory, (2) NavBar touch navigation, (3) HospitalScene SVG layout, (4) Story content replacement. Phases 2 and 3 can run in parallel once Phase 1 is complete.

### Critical Pitfalls

See [PITFALLS.md](./PITFALLS.md) for full analysis including recovery costs and phase-to-pitfall mapping.

1. **Ecological fallacy in the macro-to-micro zoom** — The four-level drill-down from aggregate state/district data to individual stories creates an implicit causal chain that aggregate data cannot support. Use associative language ("In neighborhoods with lower voter turnout, residents face higher uninsured rates") never causal language ("People who don't vote are more likely to be uninsured"). Individual stories must illustrate specific documented policy mechanisms, not statistical patterns. Address before story writing begins.

2. **Deficit framing — reducing story subjects to their suffering** — Story timelines built only around negative health events and policy harms feel exploitative and disengage audiences. Each timeline must include agency moments (community organizing, policy navigation, family milestones) alongside harms. People-first language throughout. Frame the structural system as the subject of critique. Must be built into story templates before writing; adding it retroactively feels tokenistic.

3. **Conflating undocumented and legal permanent resident experiences** — The 5-year Medicaid bar (LPR) and total exclusion (undocumented) are categorically different policy problems requiring precise, distinct language. Using "immigrant" as a catch-all reinforces the misinformation the project aims to counter. Label immigration statuses precisely in every data label, tooltip, and narrative text without exception.

4. **Transcript consent scope** — Stella Saffo's clinical interview consent may cover educational presentation but not public interactive kiosk display. These are different consent contexts. Confirm explicitly before using any transcript content. If scope is unclear, treat transcript as a research source for documented patterns, not content for direct display.

5. **Sticky hover state on touch** — `onMouseEnter`/`onMouseLeave` handlers do not fire correctly on touch screens. The first tap on an SVG element fires `onMouseEnter` but no `onMouseLeave` fires when the finger lifts, leaving tooltips and highlights stuck. Add `onTouchEnd`/`onTouchCancel` handlers that clear hover state. Test on real touch hardware — Chrome DevTools device emulation fires mouse events differently from actual touch hardware and will not catch this bug.

6. **SVG coordinate arithmetic cascades** — The hospital scene uses hardcoded absolute coordinates in a small `viewBox="0 0 100 96"` coordinate system. Trial-and-error nudging cascades into new overlaps. Compute person positions from department bounds (`d.x + d.w/2`, `d.y + d.h * 0.65`) and establish a fixed text vertical rhythm before touching any coordinates.

7. **Stat verification that stops at the number, not the definition** — KFF publishes uninsured rates for multiple denominator populations; CDC NVSR life expectancy uses a specific methodology distinct from provisional VSRR estimates; MAP voter turnout uses VEP not VAP. Confirming a number matches a source without confirming the denominator definition matches the label is technically accurate but substantively misleading. Document source, year, specific table name, and denominator for every metric.

## Implications for Roadmap

Based on combined research, the milestone has four workstreams with clear dependencies. The suggested phase structure:

### Phase 1: Data Decoupling and Structure Prep

**Rationale:** The hardest content change (story replacement) is blocked by a structural coupling in the existing code: PersonStory has hard-coded `person.id === "maria"` conditionals that will produce silent rendering failures when IDs change. Removing this coupling first makes content replacement in Phase 4 a pure data-constants edit with zero component-layer side effects. This is the fastest phase and has no visual impact — it creates correctness before content exists.
**Delivers:** `summary` field added to all four `HOSPITAL_PEOPLE` entries; PersonStory renders `{person.summary}` instead of the id-switch pattern; PersonStory decoupled from specific person IDs
**Addresses:** Architecture anti-pattern (changing story IDs without first decoupling PersonStory causes silent empty summary cards)
**Avoids:** Pitfall 17 (impossible state transitions) — establishes clean data interface before IDs change

### Phase 2: Touch Navigation Controls

**Rationale:** NavBar back arrow and tappable breadcrumbs are the highest user-value, lowest-risk changes in this milestone. Fully isolated to NavBar (new button + onClick on existing span elements) plus one line in App (new `onBack` prop). No SVG coordinates or content involved. Done before SVG layout work to keep visual diffs clean and attributable.
**Delivers:** Back arrow button in NavBar at all levels > 0; tappable breadcrumbs for levels 2 and 3 (currently display-only spans); `touch-action: manipulation` on all interactive elements; minimum 44px touch targets on nav controls; `onTouchEnd` handlers to clear sticky hover state
**Addresses:** Table stakes features — back navigation and orientation cues (P1 priority from FEATURES.md)
**Uses:** `touch-action: manipulation` CSS, `onPointerDown` on SVG targets, `onClick` on `<button>` elements, `aria-current="step"` on active breadcrumb, semantic `<nav>/<ol>/<button>` structure
**Avoids:** Adding redundant `canGoBack` state (derive `level > 0` inline); using `onTouchStart` instead of `onPointerDown`; allowing forward navigation via breadcrumbs (backward only; forward navigation without setting `selectedPerson` produces blank screen)

### Phase 3: Hospital Scene SVG Layout Fix

**Rationale:** SVG coordinate changes need visual verification isolated from other commits. Doing this after non-visual phases means any visual regression is clearly attributable to this phase. The coordinate work must be a batch operation using room-center calculations, not individual trial-and-error adjustments, to avoid cascading new overlaps.
**Delivers:** Person figures centered within department rooms using computed coordinates (`d.x + d.w/2`, `d.y + d.h * 0.65`); text/label vertical overlap resolved with documented rhythm; SVG text character budgets established for all room text fields; verified at 1280x800, 1440x900, and 768x1024 viewport sizes
**Addresses:** Table stakes — "legible text at all levels" and person figures not floating above room centers; hospital scene credibility before any public showing
**Avoids:** Pitfall 12 (SVG coordinate arithmetic that breaks on department size change — use computed values from room bounds, not absolute hardcoded positions); Pitfall 13 (SVG text that silently overflows — enforce character budget per room width)

### Phase 4: Story Content and Data Verification

**Rationale:** Highest-effort phase; requires transcript access and primary source verification. Done last because all structural coupling was removed in Phase 1, meaning content replacement is a pure data-constants edit. Stats verification must happen alongside or before story replacement — real stories next to wrong numbers actively worsens the credibility problem rather than merely leaving it unaddressed.
**Delivers:** Real patient stories in `HOSPITAL_PEOPLE` replacing fictional Maria/Dr. Okafor/James/Sofia; verified left-panel stats with primary source citations matching correct denominators; data vintage dates visible on all metrics; source registry mapping each displayed data point to primary source, year, specific table, and denominator definition
**Addresses:** Differentiator features — real transcript integration and authentic narrative content; table stakes — source citations and correct data
**Uses:** CDC NVSR Volume 74 Number 12 (US State Life Tables, 2022 final); KFF ACS state uninsured tables (non-elderly, under-65 denominator); MAP 2024 VEP voter turnout (confirm VEP not VAP)
**Avoids:** Pitfall 1 (ecological fallacy framing); Pitfall 2 (deficit framing — include agency moments); Pitfall 3 (immigration conflation — precise legal language); Pitfall 8 (composite identification — blend 3+ documented cases, avoid hyper-specific details); Pitfall 14 (transcript consent scope — confirm with Stella Saffo before writing); Pitfall 15 (stat definition mismatch — document denominator for each metric)
**Pre-condition:** Confirm consent scope with Stella Saffo before Phase 4 begins; if scope does not cover public interactive display, pivot to using transcript as research source, not display content

### Phase Ordering Rationale

- Phase 1 before Phase 4: PersonStory must be decoupled from person IDs before those IDs change; the structural fix is fast, has no visual impact, and eliminates an otherwise guaranteed silent regression
- Phase 2 before Phase 3: Isolates visual diffs; NavBar changes have no SVG coordinate dependency and can be verified without worrying about layout changes
- Phase 3 before Phase 4: SVG layout must be stable before story content sets final person figure placements; new story content may change the `variant` field on person objects which affects PersonFig rendering
- Phases 2 and 3 can run in parallel once Phase 1 is complete — they touch non-overlapping sections of App.jsx (NavBar region vs. HospitalScene/HOSPITAL_PEOPLE)
- Phase 4 depends only on Phase 1 completion; it can proceed in parallel with Phases 2 and 3 if the transcript is available early

### Research Flags

Phases likely needing deeper editorial scrutiny before execution:

- **Phase 4 (Story Content):** High domain complexity around consent, framing, and legal accuracy. Requires editorial review by someone with health equity and immigration policy knowledge — not a technical review. The "Looks Done But Isn't" checklist in PITFALLS.md should be used as a formal acceptance checklist before the phase is declared complete.
- **Phase 4 (Stats Verification):** Requires access to specific primary sources: CDC NVSR Volume 74 Number 12; KFF ACS state uninsured tables with the non-elderly denominator; MAP 2024 VEP voter turnout. The existing STATE_DATA voter turnout figures may be from a prior election cycle and may be VAP-based rather than VEP-based — this must be confirmed before verification is declared done.

Phases with standard patterns (additional research not needed):

- **Phase 1 (Data Decoupling):** Pure React data/component pattern; well-documented; explicit 5-step implementation path in ARCHITECTURE.md; zero ambiguity
- **Phase 2 (Touch Navigation):** All patterns from STACK.md are native APIs with official MDN documentation; implementation path is explicit with code examples
- **Phase 3 (SVG Layout):** Coordinate arithmetic is deterministic once room bounds are used as the source of truth; no research needed, only careful batch calculation

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | All patterns verified against official docs (MDN, react.dev, vite.dev); no new dependencies reduce risk surface to zero; version compatibility confirmed |
| Features | HIGH | Kiosk UX requirements sourced from KIOSK.com, IxDF, WCAG; touch target sizes are well-established standards; anti-feature list is grounded in project constraints |
| Architecture | HIGH | Based on direct code analysis of src/App.jsx (730 lines, 2026-03-24); component boundaries and data flow are explicit; build order rationale accounts for all known coupling points |
| Pitfalls | HIGH | Critical pitfalls sourced from Urban Institute Do No Harm Guide, Georgetown CCF, KFF, CDC; editorial pitfalls are well-documented in health equity research; implementation pitfalls are grounded in direct code analysis |

**Overall confidence:** HIGH

### Gaps to Address

- **Transcript consent scope:** Cannot be resolved by research alone; requires a direct conversation with Stella Saffo before Phase 4 begins. If consent does not cover public interactive kiosk display, the content strategy pivots from transcript-as-display-content to transcript-as-research-source. This is a binary gate for Phase 4.

- **Election data vintage and methodology in existing STATE_DATA:** It is unclear whether the current voter turnout figures in STATE_DATA are VEP-based or VAP-based, and which election cycle they represent. PITFALLS.md flags that the roadmap targets 2024 data and the existing data appears to be from a prior cycle. This must be confirmed before Phase 4 stats verification can be declared complete.

- **Fourth story archetype selection:** The project has three defined story subjects and a fourth that is TBD. Research establishes that selection should be based on mechanism (what civic-exclusion-to-health mechanism is not already covered?) rather than demographics. The three existing stories cover: total policy exclusion (undocumented), time-limited policy exclusion (5-year bar), and structural/geographic exclusion (redlining). Candidates for a fourth: incarceration and felony disenfranchisement, disability as dual civic and health barrier, or language access barriers. The specific choice requires team input; it must be made early in Phase 4 so all four stories can be evaluated as a coherent set.

- **Real touch device testing:** Chrome DevTools device emulation fires mouse events differently from real touch hardware; sticky hover state bugs are invisible in DevTools emulation. Phase 2 and Phase 4 acceptance criteria must include testing on actual touch hardware or the target kiosk device — not just desktop Chrome.

## Sources

### Primary (HIGH confidence)

**Stack sources:**
- [React v19 / 19.2 Blog Posts — react.dev](https://react.dev/blog/2024/12/05/react-19) — stable release, concurrent rendering, version history
- [Vite 7.0 Release — vite.dev](https://vite.dev/blog/announcing-vite7) — Node.js requirements, baseline-widely-available build target
- [touch-action — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action) — manipulation value, browser support matrix
- [Pointer Events — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events) — onPointerDown unified model for mouse/touch/stylus
- [WCAG 2.5.5 / 2.5.8 — W3C WAI](https://www.w3.org/WAI/WCAG21/Understanding/target-size.html) — 44px touch target requirements
- [Breadcrumb Pattern — W3C WAI ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/breadcrumb/examples/breadcrumb/) — nav/ol/button semantic structure

**Features sources:**
- [Kiosk UI Design — KIOSK.com](https://kiosk.com/kiosk-ui/) — back navigation as non-negotiable; 20mm minimum button size
- [Mobile Breadcrumbs — IxDF](https://ixdf.org/literature/article/mobile-breadcrumbs) — 9-12mm minimum link size on kiosk/touch
- [Narrative Visualization — Stanford Vis Group](http://vis.stanford.edu/files/2010-Narrative-InfoVis.pdf) — narrative arc structures in data journalism
- [Storytelling with Healthcare Data — PatientPartner](https://www.patientpartner.com/blog/storytelling-with-healthcare-data-best-practices) — real vs. composite story credibility

**Architecture sources:**
- Direct analysis of `src/App.jsx` (730 lines, 2026-03-24) — component boundaries, state flow, coordinate system
- `.planning/codebase/CONCERNS.md` — touch interaction gaps, known fragile areas

**Pitfalls sources:**
- [Do No Harm Guide — Urban Institute](https://www.urban.org/research/publication/do-no-harm-guide) — deficit framing, color-coding race and suffering
- [Georgetown CCF immigrant health fact-checking — Georgetown CCF](https://ccf.georgetown.edu) — LPR vs. undocumented conflation, political vulnerability of claims
- [KFF State Health Facts — KFF](https://www.kff.org/state-category/health-coverage-uninsured/) — uninsured rate denominator definitions
- [CDC NVSR Volume 74, Number 12](https://www.cdc.gov/nchs/nvsr) — US State Life Tables, 2022 (final methodology distinct from provisional VSRR estimates)
- [MIT Election Lab / MAP voter turnout](https://electionlab.mit.edu) — VEP vs. VAP distinction in state turnout data

### Secondary (MEDIUM confidence)

- [300ms tap delay, gone away — Chrome for Developers](https://developer.chrome.com/blog/300ms-tap-delay-gone-away) — touch-action: manipulation as the canonical fix
- [Web Animation Performance Tier List — motion.dev](https://motion.dev/magazine/web-animation-performance-tier-list) — transform/opacity as tier 1 compositor properties; fill/stroke as CPU-bound (single source; consistent with MDN)
- [Seeing Your Stories: Visualization for Narrative Medicine — PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC10880175/) — individual narrative in health data visualization

---
*Research completed: 2026-03-24*
*Ready for roadmap: yes*
