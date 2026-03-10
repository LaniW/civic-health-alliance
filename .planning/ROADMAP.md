# Roadmap: Civic Health Alliance

## Overview

Transform the Civic Health Alliance from a working four-layer visualization with fake data into a defensible, emotionally resonant data story grounded in real public sources. The work proceeds in strict sequence: decouple the monolith so data domains can be touched independently, replace map data at both geographic scales, redesign the health center layer around real hospital closure data and the feedback loop thesis, build four composite individual stories from documented statistics, then audit the entire site for source citations, accessibility, and editorial integrity.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Architecture Split** - Extract App.jsx monolith into component files and data modules with zero visual change
- [ ] **Phase 2: Real Map Data** - Replace fake US state and NYC district data with real sourced metrics and bivariate color encoding
- [ ] **Phase 3: Health Center Redesign** - Rebuild Layer 2 around NYC hospital closures, community civic response, HOLC overlay, and animated feedback loop
- [ ] **Phase 4: Individual Stories** - Build four composite NYC portraits from documented data, each showing a distinct civic exclusion mechanism
- [ ] **Phase 5: Editorial Polish** - Add visible source citations UI, colorblind-accessible palettes, and associative language audit

## Phase Details

### Phase 1: Architecture Split
**Goal**: Codebase is modular enough that each data domain and zoom level can be worked on independently
**Depends on**: Nothing (first phase)
**Requirements**: ARCH-01, ARCH-02, ARCH-03
**Success Criteria** (what must be TRUE):
  1. Each zoom level (US map, NYC map, health center, person story) renders from its own component file, not from App.jsx
  2. All data constants live in separate `src/data/*.js` files, not inline in components
  3. The application looks and behaves identically to the pre-split version (no visual or functional regressions)
**Plans**: TBD

Plans:
- [ ] 01-01: TBD
- [ ] 01-02: TBD

### Phase 2: Real Map Data
**Goal**: Users see real, sourced health and civic data for all 50 US states and all 59 NYC community districts with bivariate color encoding
**Depends on**: Phase 1
**Requirements**: STATE-01, STATE-02, STATE-03, STATE-04, STATE-05, NYC-01, NYC-02, NYC-03, NYC-04
**Success Criteria** (what must be TRUE):
  1. Every US state hexagon displays real voter turnout (2024), uninsured rate (ACS 2023-2024), life expectancy (CDC 2022), and Medicaid expansion status on hover
  2. The US hexmap uses a 3x3 bivariate color matrix (voter turnout axis vs. life expectancy axis) with a visible legend
  3. Every NYC community district displays real life expectancy, voter turnout approximation, and uninsured rate on hover
  4. The NYC district map uses bivariate color encoding matching the US map approach
  5. Data vintage years are visible for every metric displayed
**Plans**: TBD

Plans:
- [ ] 02-01: TBD
- [ ] 02-02: TBD

### Phase 3: Health Center Redesign
**Goal**: Layer 2 tells the institutional story -- NYC hospital closures, community civic response, federal funding context, HOLC redlining overlay, and the animated feedback loop diagram
**Depends on**: Phase 2
**Requirements**: NYC-05, HOSP-01, HOSP-02, HOSP-03, HOSP-04, HOSP-05
**Success Criteria** (what must be TRUE):
  1. User sees a map of NYC hospital closures since 2000 with distinct markers for closed hospitals and size-scaled markers for surviving hospitals
  2. The HOLC redlining overlay is visible as a semi-transparent layer on the NYC map, showing historical redlining grades
  3. Community civic response information (Council oversight hearing, closure legislation) and FQHC funding context are displayed
  4. An animated causal loop diagram plays at the zoom transition into this layer, showing the Civic Participation to Health Outcomes to Capacity to Participate cycle
**Plans**: TBD

Plans:
- [ ] 03-01: TBD
- [ ] 03-02: TBD

### Phase 4: Individual Stories
**Goal**: Four composite NYC portraits each illustrate a specific, documented mechanism by which civic exclusion creates health gaps
**Depends on**: Phase 3
**Requirements**: STORY-01, STORY-02, STORY-03, STORY-04, STORY-05, STORY-06, STORY-07
**Success Criteria** (what must be TRUE):
  1. User can view four distinct stories: undocumented immigrant, legal permanent resident under 5-year Medicaid bar, elderly Black woman in redlined neighborhood, and formerly incarcerated person navigating reentry
  2. Each story timeline interleaves personal health events with civic/policy moments (elections, legislation, enforcement shifts, hospital closures)
  3. Each story includes at least two agency/resilience events (community organizing, policy victories, mutual aid) -- not only suffering
  4. The undocumented immigrant and LPR stories use precise, distinct legal language and never conflate the two policy situations
  5. Each story displays a composite disclosure statement explaining the portrait is built from documented statistics
**Plans**: TBD

Plans:
- [ ] 04-01: TBD
- [ ] 04-02: TBD

### Phase 5: Editorial Polish
**Goal**: Every data point is traceable to a primary public source via visible UI, color palettes pass colorblind accessibility checks, and all narrative text uses associative (not causal) language
**Depends on**: Phase 4
**Requirements**: EDIT-01, EDIT-02, EDIT-03
**Success Criteria** (what must be TRUE):
  1. Every displayed data point has a visible citation linking to a primary public source with vintage date
  2. All captions, tooltips, and narrative text use associative language ("associated with," "in neighborhoods where") and never causal language ("causes," "leads to")
  3. Color palettes across all four layers pass colorblind simulation checks (ColorBrewer diverging schemes)
**Plans**: TBD

Plans:
- [ ] 05-01: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Architecture Split | 0/2 | Not started | - |
| 2. Real Map Data | 0/2 | Not started | - |
| 3. Health Center Redesign | 0/2 | Not started | - |
| 4. Individual Stories | 0/2 | Not started | - |
| 5. Editorial Polish | 0/1 | Not started | - |
