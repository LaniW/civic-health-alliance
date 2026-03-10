# Requirements: Civic Health Alliance

**Defined:** 2026-03-10
**Core Value:** Show that civic exclusion and poor health are a self-reinforcing loop — visible through real data and real NYC stories.

## v1 Requirements

### Architecture

- [ ] **ARCH-01**: App.jsx monolith extracted into separate component files (`USMap`, `NYCMap`, `HospitalScene`, `PersonStory`, etc.)
- [ ] **ARCH-02**: All data moved from inline App.jsx constants to `src/data/*.js` modules with colocated source citations
- [ ] **ARCH-03**: One data file per zoom layer: `states.js`, `nycDistricts.js`, `nycPaths.js`, `hospital.js`, `stories.js`

### US State Layer (Layer 0)

- [ ] **STATE-01**: All 50 states have real voter turnout data from 2024 general election (University of Florida Election Lab)
- [ ] **STATE-02**: All 50 states have real uninsured rate from US Census ACS 2023–2024
- [ ] **STATE-03**: All 50 states have real life expectancy from CDC NVSS State Life Tables (2022)
- [ ] **STATE-04**: Medicaid expansion status (yes/no) added per state from KFF State Health Facts
- [ ] **STATE-05**: State hexmap uses bivariate color encoding — one axis voter turnout, one axis life expectancy — with a 3×3 color matrix legend

### NYC District Layer (Layer 1)

- [ ] **NYC-01**: All 59 community districts have real life expectancy from NYC DOHMH Community Health Profiles
- [ ] **NYC-02**: All 59 community districts have real voter turnout approximated from NYC CFB 2024 Voter Analysis Report (crosswalk from assembly districts)
- [ ] **NYC-03**: All 59 community districts have real uninsured rate from NYC DOHMH EpiQuery / Community Health Survey
- [ ] **NYC-04**: NYC district map uses bivariate color encoding matching Layer 0 approach
- [ ] **NYC-05**: HOLC redlining grade overlay rendered as semi-transparent layer on NYC map (Mapping Inequality GeoJSON converted to SVG paths)

### Health Center Layer (Layer 2)

- [ ] **HOSP-01**: Health center scene redesigned to show institutional impact: NYC hospital closures since 2000 with location, date, and affected neighborhood
- [ ] **HOSP-02**: Closed hospitals shown as distinct markers; surviving hospitals sized by relative ED visit volume
- [ ] **HOSP-03**: Community civic response shown: 2024 NYC Council oversight hearing, S8843/A1633A legislation requiring community input for closures
- [ ] **HOSP-04**: Funding context shown: HRSA FQHC patient counts and NYC H+H funding summary from Comptroller Report
- [ ] **HOSP-05**: Animated causal loop diagram shown at zoom transition into this layer, visualizing: Civic Participation → Health Policy & Funding → Health Outcomes → Capacity to Participate

### Individual Stories — Layer 3

- [ ] **STORY-01**: Story 1 — Undocumented immigrant in NYC; composite portrait grounded in KFF/NYT 2025 Survey data; timeline shows health avoidance driven by deportation fear, impact of NYC Care enrollment decline
- [ ] **STORY-02**: Story 2 — Legal permanent resident under 5-year Medicaid bar; composite portrait grounded in PRWORA/Aliessa v. Novello/NY Essential Plan data; timeline shows coverage gap during politically excluded period
- [ ] **STORY-03**: Story 3 — Elderly Black woman in historically redlined NYC neighborhood; composite portrait grounded in NCRC redlining study, NYC DOHMH data story, hospital closure records; timeline connects HOLC grades → hospital closures → present-day health burden
- [ ] **STORY-04**: Story 4 — Formerly incarcerated NYC resident navigating reentry; composite portrait grounded in Sentencing Project data (33,000+ disenfranchised NYers), AMA Journal of Ethics reentry health data; timeline shows voting rights stripped → Medicaid gap at release → reentry barriers
- [ ] **STORY-05**: Each story's timeline interleaves personal health events and civic/policy moments (elections, legislation, ICE enforcement shifts, hospital closures)
- [ ] **STORY-06**: Stories use agency-affirming language — each person takes action, not only suffers harm (deficit framing prevention)
- [ ] **STORY-07**: Immigration status distinctions are precise — undocumented and LPR stories never conflate the two policy situations

### Editorial & Data Integrity

- [ ] **EDIT-01**: Every displayed data point links to a primary public source with vintage date (shown as visible citation in UI)
- [ ] **EDIT-02**: All captions, tooltips, and narrative text use associative language, not causal language (e.g., "associated with" not "causes")
- [ ] **EDIT-03**: Color palettes are colorblind-accessible (ColorBrewer diverging schemes)

## v2 Requirements

### Advanced Interactions

- **ADV-01**: Timeline scrubber on individual stories — scroll through time to see policy changes aligned with personal health events
- **ADV-02**: Comparison mode — pick two districts or states to see side-by-side metrics
- **ADV-03**: FQHC (community health center) location pins on NYC map from HRSA data

### Extended Data

- **DATA-01**: Additional state metrics: ACA marketplace enrollment rates from CMS Open Enrollment data
- **DATA-02**: Chronic disease prevalence per NYC district (diabetes, asthma, hypertension) from DOHMH Community Health Profiles
- **DATA-03**: Per-district premature mortality from DOHMH

### Additional Stories

- **STORY-08**: LGBTQ+/transgender NYC resident story (discrimination data available, civic exclusion mechanism needs refinement)
- **STORY-09**: Disabled NYC resident story (NYC MOPD accessibility data, physical civic access barriers)

## Out of Scope

| Feature | Reason |
|---------|--------|
| Named real individuals | Privacy and consent — composite portraits only |
| Real-time data feeds or API calls | Project constraint: fully static client-only app |
| Mobile-responsive redesign | Out of scope per PROJECT.md; layout preserved |
| Statistical regression models or confidence intervals | General public audience; visual correlation is sufficient |
| Predictive "what if" modeling | Speculative claims undermine credibility |
| Backend, database, or authentication | No infrastructure — static hosting only |
| Individual-level microdata | Privacy and ethics |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| ARCH-01 | Phase 1 | Pending |
| ARCH-02 | Phase 1 | Pending |
| ARCH-03 | Phase 1 | Pending |
| STATE-01 | Phase 2 | Pending |
| STATE-02 | Phase 2 | Pending |
| STATE-03 | Phase 2 | Pending |
| STATE-04 | Phase 2 | Pending |
| STATE-05 | Phase 2 | Pending |
| NYC-01 | Phase 2 | Pending |
| NYC-02 | Phase 2 | Pending |
| NYC-03 | Phase 2 | Pending |
| NYC-04 | Phase 2 | Pending |
| NYC-05 | Phase 3 | Pending |
| HOSP-01 | Phase 3 | Pending |
| HOSP-02 | Phase 3 | Pending |
| HOSP-03 | Phase 3 | Pending |
| HOSP-04 | Phase 3 | Pending |
| HOSP-05 | Phase 3 | Pending |
| STORY-01 | Phase 4 | Pending |
| STORY-02 | Phase 4 | Pending |
| STORY-03 | Phase 4 | Pending |
| STORY-04 | Phase 4 | Pending |
| STORY-05 | Phase 4 | Pending |
| STORY-06 | Phase 4 | Pending |
| STORY-07 | Phase 4 | Pending |
| EDIT-01 | Phase 5 | Pending |
| EDIT-02 | Phase 5 | Pending |
| EDIT-03 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 27 total
- Mapped to phases: 27
- Unmapped: 0 ✓

---
*Requirements defined: 2026-03-10*
*Last updated: 2026-03-10 after initial definition*
