# Technology Stack

**Project:** Civic Health Alliance -- Data Sourcing & Management
**Researched:** 2026-03-10

## Recommended Stack

### Existing Stack (Keep As-Is)

| Technology | Version | Purpose | Why Keep |
|------------|---------|---------|----------|
| React | 19.2.0 | UI framework | Already in place, works well for SVG-based viz |
| Vite | 7.3.1 | Build tool & dev server | Native JSON imports, fast HMR, zero config needed |
| Raw SVG | N/A | All visualizations | Custom hexmap, NYC district map, hospital scene already built; no charting library needed |

### Data Layer (New)

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Static JSON files | N/A | Data storage | Separate data from components; Vite imports JSON as ES modules at build time with tree-shaking support. No runtime fetch needed. |
| Vite JSON imports | Built-in | Data loading | `import stateData from '../data/states.json'` -- zero config, type-safe with named imports, bundled at build time |

### No New Runtime Dependencies

The project should add **zero new npm dependencies** for data management. Vite's built-in JSON import is sufficient. The existing SVG-based visualizations are purpose-built and do not benefit from charting libraries.

## Data Architecture Decision: JSON Files via Vite Import

**Confidence: HIGH**

### Current State
All data is hardcoded as JavaScript constants inside `src/App.jsx` (567 lines total, roughly 240 lines are data). This mixes data with UI logic and makes data updates error-prone.

### Recommended Approach: Static JSON files imported at build time

```
src/
  data/
    us-states.json          # 51 entries: voter turnout, uninsured rate, life expectancy
    nyc-districts.json      # 59 entries: life expectancy, voter turnout, uninsured rate
    hospital-departments.json  # 4 departments with wait times, severity
    person-stories.json     # 4 composite character profiles with timelines
    sources.json            # Citation metadata for all data points
  geo/
    hex-grid.json           # US hexmap coordinates (unchanged)
    cd-paths.json           # NYC community district SVG paths (unchanged)
```

**Why this approach:**
- Vite imports JSON as ES modules at build time -- no runtime fetch, no loading states, no CORS
- Data is separated from components but still bundled into the JS output (no separate network requests)
- Named imports work: `import { AL, NY } from '../data/us-states.json'`
- JSON is human-readable and diff-friendly in git -- data updates are reviewable
- Total data payload is small (estimated <50KB across all files) -- no chunking or lazy loading needed

**Why NOT fetch at runtime:**
- Site is fully static with no backend
- All data is known at build time
- Adding fetch introduces loading states, error handling, and CORS complexity for zero benefit
- PROJECT.md explicitly states: "all data stays hardcoded in the client"

**Why NOT keep inline in App.jsx:**
- 240+ lines of data constants obscure component logic
- Data and UI change at different rates -- separating them reduces merge conflicts
- JSON files are easier to validate and lint independently
- Enables a `sources.json` file that maps every data point to its citation

## Public Data Sources

### US State-Level Data

#### Voter Turnout by State
**Source:** US Elections Project / UF Election Lab
**URL:** https://www.electproject.org/election-data/voter-turnout-data
**Data years:** 2020 and 2024 presidential elections available
**Format:** Spreadsheet (XLS), manually transcribable to JSON
**What it provides:** Voting-eligible population (VEP) turnout rate by state -- the correct denominator (excludes non-citizens, felons in some states)
**Confidence: HIGH** -- this is the standard academic source for turnout data, maintained by Dr. Michael McDonald

**Alternative (do not use):** Census Bureau CPS Voting Supplement -- self-reported, overestimates turnout by 5-10 points due to social desirability bias. Use only if you need demographic breakdowns (age, race, income) within states.

#### Uninsured Rate by State
**Source:** KFF State Health Facts (sourced from Census Bureau ACS)
**URL:** https://www.kff.org/state-category/health-coverage-uninsured/
**Data years:** 2023-2024 (from ACS 1-year estimates)
**Format:** Downloadable table (CSV-like), filterable by year
**What it provides:** Percentage of population without health insurance, by state
**Confidence: HIGH** -- KFF is the standard policy source; underlying data is Census ACS

**Also available directly:** Census Bureau ACS Table S2701 via data.census.gov

#### Life Expectancy by State
**Source:** CDC NCHS -- U.S. State Life Tables
**URL:** https://www.cdc.gov/nchs/nvss/life-expectancy.htm
**Data years:** 2022 state life tables (released 12/2025); 2023 national only
**Format:** PDF data briefs + downloadable life table files
**What it provides:** Life expectancy at birth by state and sex
**Confidence: HIGH** -- official vital statistics from NCHS/NVSS

**Supplementary:** County Health Rankings (https://www.countyhealthrankings.org/) provides county-level life expectancy, useful for more granular analysis if needed.

#### Medicaid Enrollment by State
**Source:** KFF Medicaid Enrollment and Unwinding Tracker
**URL:** https://www.kff.org/medicaid/medicaid-enrollment-and-unwinding-tracker/
**Data years:** Monthly data through 2024-2025
**Format:** Downloadable tables
**What it provides:** Total Medicaid/CHIP enrollment by state, monthly; disenrollment during post-COVID unwinding
**Confidence: HIGH**

### NYC Community District Data

#### Life Expectancy by Community District
**Source:** NYC Department of Health -- Community Health Profiles
**URL:** https://a816-health.nyc.gov/hdi/profiles/
**Supplementary portal:** https://a816-dohbesp.nyc.gov/IndicatorPublic/data-explorer/
**Data years:** 2024 profiles available (based on 2022-2023 data)
**Format:** Interactive portal with per-district PDFs; Environment & Health Data Portal offers CSV/download
**What it provides:** Life expectancy, premature mortality, chronic disease rates, insurance coverage, poverty -- all by community district (59 districts)
**Confidence: HIGH** -- official NYC DOHMH data

**Data extraction note:** The portal is interactive, not a single bulk CSV. You will need to either:
1. Download individual district PDFs and manually extract the ~6 key metrics per district, OR
2. Use the Environment & Health Data Portal (a816-dohbesp.nyc.gov) data explorer which offers indicator-level downloads

#### Voter Turnout by NYC Geography
**Source:** NYC Campaign Finance Board -- Voter Analysis Report 2024
**URL:** https://www.nyccfb.info/media/reports/voter-analysis-report-2024/
**Supplementary:** NYC Election Atlas (https://www.electionatlas.nyc/)
**Data years:** 2020, 2024 general elections
**Format:** Report with data tables; Election Atlas has interactive maps
**What it provides:** Turnout by borough, assembly district; NYC CFB report includes neighborhood-level analysis
**Confidence: MEDIUM** -- turnout data is available by assembly district and election district, but community districts do not map 1:1 to election districts. Crosswalk/approximation needed.

**Important caveat:** Community districts and assembly districts are different geographies. The existing app uses community district IDs (BK01, BX03, etc.). Voter turnout is reported by election/assembly district. You will need to approximate using the NYC CFB's neighborhood-level aggregations or use the Election Atlas crosswalk tools. This is the trickiest data join in the project.

#### NYC Health Insurance / Uninsured Rate
**Source:** NYC Community Health Profiles + Environment & Health Data Portal
**URL:** https://a816-dohbesp.nyc.gov/IndicatorPublic/data-explorer/health-care/?id=2132
**Data years:** 2022-2023
**Format:** Interactive data explorer with download option
**What it provides:** Health insurance coverage rates by community district
**Confidence: HIGH**

### Hospital / Health Institution Data

#### NYC Hospital Closures
**Source:** Community Service Society of New York (CSSNY) testimony data
**URL:** https://www.cssny.org/news/entry/testimony-effects-hospital-closures-mccap-community-healthcare
**Supplementary:** Fiscal Policy Institute report (2025)
**Data years:** 1996-2024
**Format:** Reports and testimony (not structured data)
**What it provides:** 50+ hospital closures statewide since 1996, 21,000+ beds lost; qualitative and quantitative analysis
**Confidence: MEDIUM** -- well-documented but requires manual extraction from reports, not a downloadable dataset

#### Community Health Center Staffing
**Source:** HRSA Health Center Program data
**URL:** https://data.hrsa.gov/tools/data-reporting
**Data years:** Annual UDS (Uniform Data System) reports
**Format:** Downloadable tables
**What it provides:** Patient-to-provider ratios, services offered, patient demographics by FQHC
**Confidence: MEDIUM** -- available but requires navigating HRSA's data tools

### Individual Story Data (Composite Portraits)

These are not sourced from a single dataset but constructed from:

| Story Element | Source | Confidence |
|---------------|--------|------------|
| Lead exposure in children | NYC DOHMH lead poisoning data (by community district) | HIGH |
| Primary care wait times | HRSA UDS data + NYC DOHMH Community Health Profiles | MEDIUM |
| Dental access waitlists | NYC DOHMH oral health data | MEDIUM |
| Medicaid 5-year bar for LPRs | Federal statute (PRWORA 1996); KFF policy briefs | HIGH |
| Community health center funding | HRSA data + NYC Council testimony records | MEDIUM |
| Health board election turnout | NYC Campaign Finance Board reports | MEDIUM |

## Visualization Library Decision: Stay with Raw SVG

**Confidence: HIGH**

### Do NOT add D3.js, Recharts, MapboxGL, or any charting library.

**Rationale:**

1. **The visualizations already exist.** The hexmap, NYC district map, hospital scene, and person timeline are fully built as custom SVG. Adding D3 would mean rewriting working code.

2. **D3 fights React.** D3 wants to own the DOM (enter/update/exit pattern). React wants to own the DOM (virtual DOM reconciliation). Integrating them requires careful boundary management. The current approach -- React rendering SVG elements directly -- is simpler and has no integration friction.

3. **No new chart types are needed.** The project is replacing fake data with real data in existing visualizations, not adding new visualization types. If the data shape matches (it does -- same fields, same structure), no new rendering logic is needed.

4. **Bundle size matters for a static site.** D3 is ~230KB minified. Recharts adds ~150KB. For a project with zero charting needs beyond what already exists, this is pure bloat.

5. **MapboxGL is overkill.** The NYC map uses pre-drawn SVG paths for community districts. There is no need for tile-based rendering, zoom/pan on a geographic projection, or satellite imagery. The existing SVG approach renders instantly with no API key, no tile loading, and no WebGL requirement.

**The one exception:** If a future phase adds a new visualization type (e.g., scatter plot of turnout vs. life expectancy across all 59 districts), consider adding **Observable Plot** (~30KB) or **Recharts** rather than D3 directly. But this is not needed for the current scope.

## Data Format Specification

### us-states.json

```json
{
  "AL": {
    "name": "Alabama",
    "voterTurnout": 58.9,
    "voterTurnoutYear": 2024,
    "voterTurnoutSource": "US Elections Project",
    "uninsured": 8.2,
    "uninsuredYear": 2023,
    "uninsuredSource": "KFF/Census ACS",
    "lifeExp": 73.8,
    "lifeExpYear": 2022,
    "lifeExpSource": "CDC NCHS State Life Tables"
  }
}
```

### nyc-districts.json

```json
[
  {
    "id": "BK01",
    "name": "Williamsburg, Greenpoint",
    "borough": "Brooklyn",
    "lifeExp": 84.4,
    "lifeExpSource": "NYC DOHMH Community Health Profiles 2024",
    "voterTurnout": 42.6,
    "voterTurnoutNote": "Approximated from assembly district data",
    "voterTurnoutSource": "NYC CFB Voter Analysis Report 2024",
    "uninsuredRate": null,
    "uninsuredSource": "NYC Environment & Health Data Portal"
  }
]
```

**Key design choice:** Every data point carries its source and year inline. This makes the data self-documenting and supports a "sources" or "methodology" page if desired.

## Alternatives Considered

| Category | Recommended | Alternative | Why Not |
|----------|-------------|-------------|---------|
| Data storage | JSON files via Vite import | Keep inline in App.jsx | 240 lines of data cluttering component; poor separation of concerns |
| Data storage | JSON files via Vite import | Fetch from `/public/data/` at runtime | Adds loading states, error handling, potential CORS issues for zero benefit on a static site |
| Data storage | JSON files via Vite import | SQLite / IndexedDB | Massive overkill; all data fits in <50KB of JSON |
| Data loading | Vite build-time import | API calls to data.gov / NYC Open Data | Introduces runtime dependency on external services; site should work offline after load |
| Visualization | Raw SVG (existing) | D3.js | Fights React DOM model; existing viz works; adds 230KB for no benefit |
| Visualization | Raw SVG (existing) | Recharts | Designed for standard charts (bar, line, pie) -- not custom hex maps or hospital scenes |
| Visualization | Raw SVG (existing) | MapboxGL | Requires API key, WebGL, tile loading -- overkill for a pre-drawn SVG district map |
| Visualization | Raw SVG (existing) | Observable Plot | Lighter than D3 (~30KB) but still unnecessary; reconsider only if new chart types added |

## Data Collection Workflow

This is not a code dependency but a critical part of the project work:

1. **US States (~2 hours):** Download voter turnout (Elections Project spreadsheet), uninsured rates (KFF table), life expectancy (CDC state life tables). All three are single-page downloads. Merge into `us-states.json`.

2. **NYC Districts (~4-6 hours):** This is the most labor-intensive step.
   - Life expectancy: Extract from 59 individual Community Health Profile pages on NYC DOHMH portal
   - Voter turnout: Approximate from NYC CFB Voter Analysis Report assembly district data (requires geographic crosswalk)
   - Uninsured rate: Extract from Environment & Health Data Portal indicator pages
   - Consider: The NYC DOHMH may have a bulk data download not easily found via search. Check https://a816-dohbesp.nyc.gov/IndicatorPublic/ data explorer's download functionality first.

3. **Hospital/Health Center Data (~2-3 hours):** Extract from CSSNY testimony, HRSA UDS reports. This is qualitative + quantitative -- supports the hospital scene layer and department data.

4. **Person Stories (~3-4 hours):** Research and construct composite portraits from NYC DOHMH data (lead poisoning rates, dental access), HRSA data (provider ratios), federal policy sources (Medicaid 5-year bar). Each story should cite 2-3 specific data sources.

## Installation

No new packages to install. The existing stack handles everything:

```bash
# No changes needed
npm install    # existing dependencies only
npm run dev    # Vite handles JSON imports natively
```

The only "installation" is creating the `src/data/` directory and populating JSON files with researched data.

## Sources

- [CDC NCHS Life Expectancy Data](https://www.cdc.gov/nchs/nvss/life-expectancy.htm) -- state-level life tables
- [US Elections Project / UF Election Lab](https://www.electproject.org/election-data/voter-turnout-data) -- voter turnout by state (VEP)
- [KFF State Health Facts](https://www.kff.org/state-category/health-coverage-uninsured/) -- uninsured rates by state
- [KFF Medicaid Enrollment Tracker](https://www.kff.org/medicaid/medicaid-enrollment-and-unwinding-tracker/) -- Medicaid enrollment by state
- [NYC DOHMH Community Health Profiles](https://a816-health.nyc.gov/hdi/profiles/) -- 59 community district health data
- [NYC Environment & Health Data Portal](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-explorer/) -- indicator-level downloads
- [NYC CFB Voter Analysis Report 2024](https://www.nyccfb.info/media/reports/voter-analysis-report-2024/) -- NYC voter turnout by geography
- [NYC Election Atlas](https://www.electionatlas.nyc/) -- historical election data mapped to NYC geographies
- [CSSNY Hospital Closures Testimony](https://www.cssny.org/news/entry/testimony-effects-hospital-closures-mccap-community-healthcare) -- NYC hospital closure data
- [HRSA Data Reporting](https://data.hrsa.gov/tools/data-reporting) -- community health center data
- [County Health Rankings](https://www.countyhealthrankings.org/) -- county-level health data (supplementary)
- [Vite JSON Import Documentation](https://vite.dev/guide/features) -- build-time JSON handling

---

*Stack research: 2026-03-10*
