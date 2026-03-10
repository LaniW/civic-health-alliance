# Project Research Summary

**Project:** Civic Health Alliance — Data Sourcing & Management
**Domain:** Static civic health data visualization (bidirectional civic engagement / health outcomes feedback loop)
**Researched:** 2026-03-10
**Confidence:** HIGH

## Executive Summary

The Civic Health Alliance is a fully static, client-side React/Vite application that uses custom SVG visualizations to argue a bidirectional feedback loop: civic exclusion worsens health outcomes, and deteriorating health suppresses civic participation. The application already has all four visualization layers built (US state hexmap, NYC community district map, health center scene, individual composite portraits) but currently runs on fake placeholder data. The primary work of the project is replacing that fake data with rigorously sourced, publicly available data from CDC, Census Bureau, NYC DOHMH, and NYC CFB — while refactoring the monolithic 567-line App.jsx into a maintainable module structure.

The recommended technical approach is intentionally minimal: add zero new npm dependencies, keep all data as build-time ES module imports (no fetch, no backend, no loading states), and split the codebase by extracting data files and zoom-level components from the monolith. All data sources are freely available and high-confidence — the US state-level datasets (CDC NCHS life tables, KFF/ACS uninsured rates, UF Election Lab turnout) are single-download operations. NYC district-level data is more labor-intensive, particularly voter turnout, which requires a geographic crosswalk between assembly districts and community districts. The data collection work is estimated at 11–15 hours total; NYC district data is the critical path.

The most consequential risks are not technical but editorial. This project makes policy claims about marginalized communities (undocumented immigrants, formerly incarcerated people, redlined neighborhoods) using real data. Research identified six critical pitfalls: ecological fallacy framing, deficit framing of individuals, conflation of immigrant legal statuses, redlining narrative oversimplification, politically indefensible sourcing, and accessibility gaps in SVG-heavy visualizations. These must be addressed in design and content before implementation, not retrofitted afterward. Recovery costs for most of these pitfalls are HIGH once stories are written and visualizations are built.

## Key Findings

### Recommended Stack

The existing stack (React 19.2, Vite 7.3, raw SVG) is correct and complete for this project — see [STACK.md](.planning/research/STACK.md). No new runtime dependencies should be added. Vite's native JSON/ES module import handles all data loading at build time. The existing SVG-based visualizations are purpose-built and outperform any charting library option for this use case: D3 conflicts with React's DOM model, Recharts is designed for standard chart types, and MapboxGL is overkill for pre-drawn SVG district maps.

**Core technologies:**
- **React 19.2**: UI framework — already in place, purpose-built SVG rendering is already working
- **Vite 7.3**: Build tool — native ES module and JSON import at build time, zero config needed
- **Raw SVG**: All visualizations — hexmap, NYC district map, and hospital scene are fully built; no rewrite needed
- **Static JSON/JS data files**: Data layer — separate data from components; imported at build time, bundled into output, no network requests

### Expected Features

See [FEATURES.md](.planning/research/FEATURES.md) for full feature landscape and visualization approach recommendations.

**Must have (table stakes):**
- Real data for US state layer (voter turnout, uninsured rate, life expectancy) — all sources are single downloads; fake data destroys credibility
- Real data for NYC community district layer (59 districts, same three metrics) — most labor-intensive data collection task
- Bivariate color encoding on at least one map layer — minimum needed to visually show the two-variable relationship
- Source citations visible to users — general public audience requires citation transparency to trust the data
- Hospital closure data for health center layer (14+ NYC closures with dates, neighborhoods, demographics)
- Four individual composite stories with real statistics — each needs 8–12 timeline events grounded in documented data
- Accessible color palettes — WCAG AA compliance and colorblind-safe palettes

**Should have (differentiators):**
- HOLC redlining overlay on NYC map — visually connects 1930s policy to current health gaps; Mapping Inequality project has digitized polygons
- Animated causal loop diagram — makes the bidirectional feedback loop thesis explicit at layer transitions
- "What changed" policy callouts — text overlays showing specific policies/elections that shifted data
- FQHC location pins on NYC map — shows safety-net care geography vs. hospital closure geography
- Composite disclosure statement — prominent "this is a composite" on each individual story

**Defer (v2+):**
- Timeline scrubber interaction — static timelines work; scrubber adds complexity without changing the story
- Comparison mode (pick two districts/states) — dashboard feature, not narrative feature
- Mobile-responsive redesign — explicitly out of scope per PROJECT.md
- Real-time data feeds — all data is annual; runtime fetch adds complexity for zero benefit

### Architecture Approach

The architecture is a data extraction and file split, not a rebuild — see [ARCHITECTURE.md](.planning/research/ARCHITECTURE.md). The monolithic App.jsx must be split into data modules and zoom-level components before data replacement begins. The recommended split order is: utilities first (colors.js), then geometry (nycPaths.js), then data constants (states.js, nycDistricts.js, hospital.js), then leaf components (Pulse, PersonFig, InfoPanel), then zoom-level components (USMap, NYCMap, HospitalScene, PersonStory). Each extraction step should be a separate commit that changes nothing visually. Data replacement follows the split — each data file can then be updated independently.

**Major components:**
1. **App.jsx** (~80–120 lines after split) — zoom state machine, breadcrumb nav, conditional rendering of the four zoom-level components
2. **src/data/** — one ES module file per data domain (states.js, nycDistricts.js, nycPaths.js, hospital.js) each with source metadata collocated; sources.json as citation registry
3. **src/components/** — one file per zoom level (USMap, NYCMap, HospitalScene, PersonStory) plus shared primitives (InfoPanel, PersonFig, Pulse)
4. **src/utils/colors.js** — color mapping functions extracted from App.jsx; pure functions with no side effects

### Critical Pitfalls

See [PITFALLS.md](.planning/research/PITFALLS.md) for full analysis including recovery costs and phase-to-pitfall mapping.

1. **Ecological fallacy framing** — the four-layer zoom from aggregate data to individual stories implies aggregate patterns explain individual outcomes. Use associative language ("in neighborhoods with lower turnout...") not causal language ("because turnout is low..."). Individual stories must illustrate specific documented policy mechanisms, not aggregate statistics. Address in data integration phase before story writing.

2. **Deficit framing** — stories built around suffering alone reproduce harmful narratives and disengage audiences. Each story timeline must include at least two agency/resilience events (community organizing, policy victories, mutual aid participation) alongside health harms. Use people-first language; frame the structural system as the subject of critique, not the individual. Address in story design before writing begins.

3. **Immigration status conflation** — undocumented and legal permanent resident experiences are fundamentally different legally and medically. Use precise legal language throughout. The LPR story cites PRWORA 1996 and NY state workarounds; the undocumented story is about total exclusion and fear of care-seeking. Never use unqualified "immigrant" in data labels. Address in data research phase.

4. **Indefensible sourcing** — every displayed data point must trace to a primary public source (CDC, Census, NYC DOH, peer-reviewed journal). Build the source registry as a deliverable of the data phase, not as an afterthought. Include data vintage years on every metric. Political opposition will target the weakest citation. Address before visualization implementation.

5. **Accessibility gaps** — SVG visualizations are inaccessible by default. Every interactive SVG element needs ARIA labels and keyboard navigation. The full US → NYC → health center → person flow must work with Tab/Enter only. Retrofitting ARIA into built SVG components is HIGH cost; build accessibly from the start.

## Implications for Roadmap

Based on combined research, the work naturally falls into four phases with strong sequencing dependencies.

### Phase 1: File Structure Split

**Rationale:** The architecture must be decoupled before data can be replaced safely. The monolithic App.jsx creates merge conflicts if multiple data domains are updated simultaneously; it also makes it impossible to test individual zoom levels in isolation. This phase has zero data changes and zero visual changes — it is purely structural scaffolding.
**Delivers:** A `src/data/` directory with placeholder data in extracted modules; a `src/components/` directory with extracted zoom-level components; App.jsx reduced to ~100 lines of state and layout logic
**Addresses:** Feature foundation (bivariate color encoding requires touching colors.js separately from components)
**Avoids:** Anti-pattern of doing data replacement and refactor simultaneously (Anti-Pattern 5 from ARCHITECTURE.md); merge conflicts during parallel data work

### Phase 2: US State Data Integration

**Rationale:** US state data is the simplest data domain — all three sources are single downloads, high-confidence, and structured consistently. It is the right first data replacement: validates the data module pattern, tests the build pipeline, and delivers visible real data without the geographic complexity of the NYC crosswalk problem.
**Delivers:** `src/data/states.js` with real CDC/Census/UF Election Lab data for 51 states; source metadata collocated; data vintage dates in UI; bivariate color encoding on the US hexmap
**Uses:** Vite ES module import, CDC NCHS State Life Tables (2022), KFF/ACS uninsured rates (2023), UF Election Lab turnout (2024)
**Avoids:** Stale data pitfall (every metric shows source and year); indefensible sourcing pitfall (primary sources only, no advocacy org summaries)

### Phase 3: NYC District Data Integration

**Rationale:** NYC district data is the critical path of the entire project — 59 districts, three metrics each, with a known hard problem (assembly district to community district crosswalk for voter turnout). This phase must be treated as a research and data collection sprint, not just a code change. The geographic crosswalk approximation must be disclosed in the visualization.
**Delivers:** `src/data/nycDistricts.js` with real NYC DOHMH/CFB data for 59 community districts; bivariate color encoding on NYC map; disclosure of turnout approximation methodology; FQHC location pins (HRSA data, low-effort addition once map is working)
**Uses:** NYC DOHMH Community Health Profiles (2024), NYC Environment and Health Data Portal (uninsured rates), NYC CFB Voter Analysis Report 2024, NYC Election Atlas crosswalk
**Avoids:** Ecological fallacy (aggregate district data labeled as associations, not individual causes); stale data pitfall

### Phase 4: Health Center Scene and Individual Stories

**Rationale:** This phase depends on the NYC district layer being in place (stories reference health center context and district geography). It is also the highest-risk phase for editorial pitfalls — deficit framing, ecological fallacy, immigration conflation, and composite identification risk all concentrate here. Story templates defining agency requirements, composite disclosure language, and immigration status precision must be established before writing begins.
**Delivers:** `src/data/hospital.js` with real NYC hospital closure data and four composite stories (undocumented immigrant, LPR under 5-year bar, elderly Black woman in redlined neighborhood, formerly incarcerated person in reentry); source registry (`src/data/sources.json`) as deliverable; composite disclosure on each story
**Implements:** PersonStory component with real timelines; HospitalScene with closure data; animated causal loop diagram (low technical complexity, high narrative value)
**Avoids:** Deficit framing (each story has 2+ agency events); immigration conflation (distinct policy mechanisms, precise legal language); redlining oversimplification (gentrification/displacement addressed in story narrative); ecological fallacy (individual mechanisms cited specifically); composite identification risk (3+ documented cases blended, no hyper-specific details)

### Phase 5: Source Citations, Accessibility, and Polish

**Rationale:** Source registry and accessibility cannot be bolted on at the end without HIGH recovery cost, but final polish (color system audit, readability scoring, footnotes UI) is most efficient after all data is in place. This phase converts the source metadata already collocated in data files into a visible UI element and verifies accessibility across all four zoom levels.
**Delivers:** Source citations visible in UI (footer or expandable overlay); WCAG AA color palette audit; keyboard navigation test passing for full zoom flow; screen reader audit; all narrative text at 8th-grade reading level; data vintage dates visible on every metric
**Avoids:** Accessibility theater pitfall; color-coding race and suffering pitfall; stale data pitfall (final check)

### Phase Ordering Rationale

- **Split before replace:** Architecture research explicitly recommends completing the file split before any data replacement. Doing both simultaneously in a monolith creates untestable, conflict-prone changes.
- **State before NYC:** US state data is lower complexity and validates the data module pattern before tackling the NYC crosswalk problem. NYC data collection is the critical path and should not be blocked on architectural uncertainty.
- **Data before stories:** Individual stories reference the health center and district context — they should be written after that context exists in the application, not speculatively.
- **Editorial guardrails before content:** Story templates and composite guidelines must precede content creation. Adding them retroactively produces disclaimers, not honest framing.

### Research Flags

Phases likely needing deeper research during planning:

- **Phase 3 (NYC District Data):** The assembly district to community district crosswalk is a known hard problem. The NYC CFB neighborhood-level aggregations and Election Atlas crosswalk tools are identified, but the specific approximation methodology needs to be designed and disclosed. Recommend a focused research spike on this before committing to a turnout estimation approach.
- **Phase 4 (Individual Stories):** The formerly incarcerated person archetype (recommended as the 4th story) is well-sourced at the policy level but the NYC-specific health reentry data (Medicaid coverage gap at release, jail population chronic disease rates) may require deeper HRSA/NYC DOH sourcing. Recommend validating source availability before finalizing story templates.
- **Phase 4 (Bidirectional Evidence):** Research flagged that evidence for the health → civic participation direction (the reverse of the primary argument) is weaker than the civic → health direction. This direction needs explicit data points in the health center layer. The layer redesign (closure impact map, community response panel) provides a framework, but specific data points need to be sourced.

Phases with standard patterns (skip additional research):

- **Phase 1 (File Split):** Pure refactoring with established React component extraction patterns. Architecture research provides a specific 12-step extraction order. No unknowns.
- **Phase 2 (US State Data):** All three data sources are identified, high-confidence, and in standard downloadable formats. Data collection is ~2 hours. No unknowns.
- **Phase 5 (Accessibility/Polish):** WCAG AA compliance, ColorBrewer palettes, and Hemingway readability testing are established practices with known tooling. No research needed.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | No new dependencies needed; existing Vite/React/SVG stack is correct; all library decisions are verified against the project constraint of "fully static, no backend" |
| Features | HIGH | US state and NYC district data sources are all identified and high-confidence; visualization approaches (bivariate choropleth, causal loop diagram) have CDC and peer-reviewed backing |
| Architecture | HIGH | Based on direct analysis of the existing 567-line App.jsx; split order is specific and low-risk; no speculative components |
| Pitfalls | HIGH | Grounded in Urban Institute Do No Harm Guide, CDC data equity principles, Georgetown CCF fact-checking, and ecological fallacy literature; all pitfalls are domain-specific, not generic |

**Overall confidence:** HIGH

### Gaps to Address

- **NYC voter turnout crosswalk methodology:** The geographic mismatch between assembly districts (turnout data) and community districts (the app's unit of analysis) is a known problem without a fully specified solution. The NYC CFB and Election Atlas provide raw material, but the specific approximation approach needs to be designed and disclosed to users. Handle during Phase 3 planning with a dedicated research spike.

- **Redlining overlay implementation detail:** HOLC polygons are available from Mapping Inequality in GeoJSON format. Converting these to SVG paths for overlay on the existing NYC district SVG requires a coordinate projection step that was not fully specified in research. Handle during Phase 3 planning.

- **4th story data depth:** The formerly incarcerated person archetype is recommended based on mechanism fit and demographic completeness, but NYC-specific data for health outcomes at reentry (vs. national data) needs verification. Handle during Phase 4 planning before story template is finalized.

- **Bidirectional evidence inventory:** PITFALLS.md flags that the health → civic participation direction needs explicit data points. FEATURES.md recommends a community response panel in the health center layer. A specific inventory of data points for this direction (illness as voting barrier, hospital closures reducing community civic infrastructure) should be built during Phase 4 planning.

## Sources

### Primary (HIGH confidence)

- [CDC NCHS State Life Tables](https://www.cdc.gov/nchs/nvss/life-expectancy.htm) — state life expectancy data (2022)
- [KFF State Health Facts](https://www.kff.org/state-category/health-coverage-uninsured/) — uninsured rates by state (sourced from Census ACS)
- [UF Election Lab / US Elections Project](https://www.electproject.org/election-data/voter-turnout-data) — voter turnout by state (2024)
- [NYC DOHMH Community Health Profiles](https://a816-health.nyc.gov/hdi/profiles/) — all 59 NYC community district health metrics
- [NYC Environment and Health Data Portal](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-explorer/) — indicator-level downloads including uninsured rate by district
- [NYC CFB Voter Analysis Report 2024](https://www.nyccfb.info/media/reports/voter-analysis-report-2024/) — NYC voter turnout by geography
- [Urban Institute Do No Harm Guide](https://www.urban.org/research/publication/do-no-harm-guide-applying-equity-awareness-data-visualization) — equity-aware visualization principles
- [Georgetown CCF: Truth About Medicaid for Immigrants](https://ccf.georgetown.edu/2025/05/21/the-truth-about-medicaid-coverage-for-immigrants-and-the-looming-threats/) — immigration/Medicaid data accuracy
- [KFF/NYT 2025 Survey of Immigrants](https://www.kff.org/immigrant-health/kff-new-york-times-2025-survey-of-immigrants-health-and-health-care-experiences-during-the-second-trump-administration/) — undocumented immigrant healthcare avoidance data
- [NCRC Redlining and Neighborhood Health](https://ncrc.org/holc-health/) — redlining and life expectancy gap data
- [Mapping Inequality (HOLC digitized maps)](https://dsl.richmond.edu/panorama/redlining/) — digitized HOLC polygons for NYC
- [Sentencing Project: NY Voting Rights](https://www.sentencingproject.org/fact-sheet/new-york-should-restore-voting-rights-to-over-33000-citizens/) — felony disenfranchisement data
- [AMA Journal of Ethics: Health Justice and Reentry](https://journalofethics.ama-assn.org/article/what-does-health-justice-look-people-returning-incarceration/2017-09) — reentry health data

### Secondary (MEDIUM confidence)

- [NYC CFB Community Profiles](https://www.nyccfb.info/nyc-votes/community-profiles/) — supplementary NYC turnout by neighborhood (assembly district approximations)
- [NYC Election Atlas](https://www.electionatlas.nyc/) — historical election data crosswalk tools
- [HRSA Data Reporting](https://data.hrsa.gov/tools/data-reporting) — FQHC location and staffing data (requires navigating data tools)
- [CSSNY Hospital Closures Testimony](https://www.cssny.org/news/entry/testimony-effects-hospital-closures-mccap-community-healthcare) — qualitative hospital closure data
- [NYC Comptroller Report on NYC H+H (Dec 2025)](https://www.osc.ny.gov/files/reports/pdf/nyc-health-hospitals-strategic-initiatives.pdf) — NYC Health + Hospitals institutional data
- [CDC Bivariate Mapping Tutorial](https://www.cdc.gov/pcd/issues/2020/19_0254.htm) — bivariate choropleth implementation reference

### Tertiary (LOW confidence — needs validation during implementation)

- NYC voter turnout crosswalk methodology from assembly districts to community districts — approach identified but not validated against actual data
- NYC-specific health outcomes at reentry (formerly incarcerated) — national data available; NYC-specific needs verification

---
*Research completed: 2026-03-10*
*Ready for roadmap: yes*
