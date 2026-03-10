# Feature Landscape

**Domain:** Civic health data visualization (bidirectional feedback loop between civic engagement and health outcomes)
**Researched:** 2026-03-10

## Data Sources by Visualization Layer

### Layer 0: US State Hexmap

Real, publicly accessible data exists for all three current metrics.

| Metric | Source | Format | Confidence |
|--------|--------|--------|------------|
| Voter turnout by state (2024) | University of Florida Election Lab, Ballotpedia | Downloadable tables, state-by-state | HIGH |
| Uninsured rate by state (2024) | US Census Bureau American Community Survey 1-year estimates | Downloadable CSV/Excel via data.census.gov | HIGH |
| Life expectancy by state (2022) | CDC NVSS State Life Tables (released April 2025) | PDF tables, downloadable via FTP | HIGH |

**Key data points for narrative:** States with lowest voter turnout (Hawaii 50.3%, Oklahoma 53.3%, Arkansas 53.5%) overlap significantly with states having highest uninsured rates and lowest life expectancy. Texas (56.6% turnout) has one of the highest uninsured rates nationally. This correlation is the feedback loop made visible at the macro level.

**Recommended additions:**
- Medicaid expansion status (binary yes/no) from KFF state health facts -- this is the clearest policy lever connecting civic action to health coverage
- ACA marketplace enrollment rates from CMS Open Enrollment data

### Layer 1: NYC Community Districts

NYC has unusually rich community-district-level health and civic data.

| Metric | Source | Format | Confidence |
|--------|--------|--------|------------|
| Life expectancy by community district | NYC DOHMH Community Health Profiles (59 districts) | Interactive profiles + downloadable data | HIGH |
| Voter turnout by community district | NYC Campaign Finance Board Community Profiles & 2024 Voter Analysis Report | Interactive profiles + PDF reports | HIGH |
| Uninsured rate by district | NYC DOHMH EpiQuery / Community Health Survey | Queryable via EpiQuery tool | HIGH |
| Premature mortality | NYC DOHMH Community Health Profiles | Per-district data | HIGH |
| Self-rated health | NYC Community Health Survey (annual) | Public use datasets downloadable | HIGH |
| Chronic disease prevalence (diabetes, asthma, hypertension) | NYC DOHMH Community Health Profiles | Per-district data | HIGH |

**Key data points for narrative:** NYC's 59 community districts show dramatic variation. Manhattan CB 7 (Upper West Side) life expectancy differs from Brownsville (Brooklyn CB 16) by roughly 10 years. Voter turnout in 2024 general election was 60.2% citywide, but primary turnout was 6.6-10.1%, meaning local health-affecting elections are decided by tiny fractions of eligible voters.

**Recommended additions:**
- HOLC redlining grade overlay (digitized maps from Mapping Inequality project at University of Richmond) -- shows historical structural racism persisting in present-day health disparities
- Hospital/health facility locations (HRSA Find a Health Center API + NYC Health Map)

### Layer 2: Health Center Scene

This layer needs the most redesign. Current architecture shows a generic hospital with departments and wait times. Real data should show the institutional-level feedback loop: how civic engagement (or its absence) affects health institutions.

| Data Type | Source | Format | Confidence |
|-----------|--------|--------|------------|
| NYC hospital closures since 2000 (20 closures) | NYSNA Hospital Closures list; NYC City Council Committee on Hospitals testimony (Oct 2024) | Named list with dates and neighborhoods | HIGH |
| FQHC locations, patient counts, funding | HRSA Data Explorer / UDS Awardee Data (32.4M patients nationally in 2024) | Downloadable datasets by state/county | HIGH |
| NYC Health + Hospitals system data | NYC Comptroller Report 17-2026 (Dec 2025) | PDF report with strategic initiatives, financial data | MEDIUM |
| Community organizing around closures | NYC City Council testimony transcripts; CSSNY testimony on effects of hospital closures | Public hearing records | MEDIUM |
| Staffing shortages and impacts | NYSNA reports; NYC H+H strategic initiatives report | Reports and testimony | MEDIUM |

**Key hospital closures for visualization (NYC-specific):**
- St. Vincent's Hospital, Manhattan (2010) -- Greenwich Village
- Long Island College Hospital, Brooklyn (2014) -- closed without community input
- North General Hospital, Manhattan (2010) -- Harlem, predominantly Black community
- Peninsula Hospital Center, Queens (2012) -- Far Rockaway, isolated community
- Westchester Square Medical Center, Bronx (2013)
- Cabrini Medical Center, Manhattan (2008)
- St. John's Queens Hospital, Elmhurst (2009)

**Feedback loop data:** Closures disproportionately hit low-income neighborhoods and communities of color. Communities with lower civic participation had less power to fight closures. After closures, remaining hospitals saw significant increases in ED visits, further straining the system. New legislation (S8843/A1633A) now requires community input for hospital closures -- a direct example of civic engagement producing health infrastructure protection.

### Layer 3: Individual Stories

Four composite portraits grounded in real statistics. Each illustrates a distinct mechanism of the civic exclusion to health gap feedback loop.

#### Story 1: Undocumented Immigrant

| Data Point | Source | Statistic | Confidence |
|------------|--------|-----------|------------|
| Avoidance of healthcare | KFF/NYT 2025 Survey of Immigrants | 75% of likely undocumented immigrants avoid medical care, travel, or work due to deportation fear | HIGH |
| NYC Care enrollment decline | THE CITY reporting (Aug 2025) | Dropped from 143,503 to 141,129 -- first decline since program's 2019 creation | HIGH |
| Negative health impacts from immigration worry | KFF/NYT 2025 Survey | 80% of likely undocumented immigrants report negative health impacts from immigration-related worries | HIGH |
| Fear of data sharing | KFF/NYT 2025 Survey | 80% of likely undocumented immigrants concerned about providers sharing status with ICE | HIGH |
| Skipping/postponing care increase | KFF/NYT 2025 Survey | Increased from 22% to 29% between 2023-2025 | HIGH |

**Civic exclusion mechanism:** Cannot vote. Cannot influence health policy. Fears interacting with any government-adjacent institution. Health deteriorates. Community becomes less visible in political process.

#### Story 2: Legal Permanent Resident (5-Year Medicaid Bar)

| Data Point | Source | Statistic | Confidence |
|------------|--------|-----------|------------|
| Federal 5-year bar | Federal law (PRWORA 1996) | LPRs ineligible for federal Medicaid for first 5 years | HIGH |
| NY state workaround | Aliessa v. Novello (2001) court ruling | NY constitution requires state-funded Medicaid for LPRs in the bar period | HIGH |
| Essential Plan expansion | NY State (2024) | Added 450,000 New Yorkers up to 250% FPL | MEDIUM |
| Federal funding threat | Congressional budget proposals (2025-2026) | "One Big Beautiful Bill" threatens state immigrant health coverage funding | HIGH |

**Civic exclusion mechanism:** Can work and pay taxes but cannot vote during the period most critical for establishing healthcare access. Policy decisions about their coverage are made entirely without their input. When federal funding is cut, they bear consequences of elections they cannot participate in.

#### Story 3: Elderly Black Woman in Historically Redlined Neighborhood

| Data Point | Source | Statistic | Confidence |
|------------|--------|-----------|------------|
| Life expectancy gap in redlined areas | NCRC Redlining and Neighborhood Health study | 3.6 years lower life expectancy in formerly redlined communities | HIGH |
| Redlining to COVID-19 outcomes | PMC study (NYC-specific) | Historically low-graded HOLC neighborhoods had higher COVID-19 infection rates | HIGH |
| Preterm birth disparities | AJPH study (NYC 2013-2017) | Structural racism via redlining associated with preterm birth risk in NYC | HIGH |
| Hospital closures in communities of color | NYSNA data; NYC City Council testimony | 20 NYC hospital closures disproportionately hit communities of color | HIGH |
| HOLC map data (digitized) | Mapping Inequality, University of Richmond | Digitized HOLC maps with grades for all NYC neighborhoods | HIGH |
| Voter suppression history | NYCLU reporting | Black and Latino NYers disproportionately affected by felony disenfranchisement | MEDIUM |

**Civic exclusion mechanism:** Decades of redlining stripped neighborhood wealth, political power, and health infrastructure. Hospital closures followed disinvestment. Community has less capacity to organize politically when burdened by chronic disease and lack of nearby care. Gerrymandering and felony disenfranchisement further reduce political voice.

#### Story 4 Recommendation: Formerly Incarcerated Person (Reentry)

**Recommendation:** The 4th archetype should be a **formerly incarcerated person navigating reentry in NYC**, because this archetype:

1. **Has the strongest bidirectional feedback loop data.** Incarceration strips voting rights (civic exclusion). Upon release, the Medicaid Inmate Exclusion Policy creates a coverage gap. Without healthcare, reentry fails. Recidivism further extends civic exclusion. The loop is tight and data-rich.

2. **NYC data is abundant and specific:**

| Data Point | Source | Statistic | Confidence |
|------------|--------|-----------|------------|
| Disenfranchised NYers | Sentencing Project | 33,000+ New Yorkers disenfranchised due to felony convictions | HIGH |
| Racial disparity | NYCLU | ~50% of disenfranchised NYers are Black, 24% Latino | HIGH |
| Medicaid coverage gap at release | AMA Journal of Ethics | Medicaid suspended during incarceration, creating uninsured period upon reentry | HIGH |
| Health justice needs | AMA Journal of Ethics | Greatest needs at reentry: insurance, substance use treatment, mental health care | HIGH |
| NYC jail system health | AMA Journal of Ethics | NYC jail population has documented high rates of chronic disease, mental illness, substance use | MEDIUM |

3. **Completes the demographic contrast:** The four stories now span: immigration status exclusion (undocumented), immigration-based coverage gap (LPR), structural racism via redlining (elderly Black woman), and carceral system exclusion (formerly incarcerated person). Each is a different mechanism. Each is NYC-grounded. Each has robust public data.

**Alternative considered:** Disabled NYC resident. Good data exists (NYC MOPD Accessible NYC 2025 report, only 23% of subway stations accessible), but the civic exclusion to health gap feedback loop is less tight -- disability creates health barriers but the civic exclusion pathway is primarily physical access, not systemic disenfranchisement. The formerly incarcerated archetype has a more direct and documentable feedback loop.

**Alternative considered:** LGBTQ+/transgender NYC resident. Strong discrimination data (62% of transgender adults experienced discrimination in 2024, 17.3% face cost barriers to care vs 7.3% for non-LGBTQ+), but the civic exclusion mechanism is less clear-cut than literal disenfranchisement. Better suited as a future expansion.

---

## Table Stakes

Features users expect. Missing = product feels incomplete.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Real data at US state level (turnout, uninsured, life expectancy) | Core promise of the site; fake data destroys credibility | Low | All three datasets are freely downloadable. Hardcode ~50 state records. |
| Real data at NYC district level | Same as above for NYC layer | Medium | 59 districts, multiple metrics. EpiQuery + CFB data requires manual extraction or API queries. |
| Bivariate color encoding (health + civic on same map) | Single-variable choropleth cannot show the feedback loop | Medium | Use bivariate choropleth: one axis = voter turnout, other = life expectancy. 3x3 or 4x4 color grid. |
| Source citations visible to user | General public audience needs to trust the data | Low | Footnotes or "Data source" labels on each visualization. |
| Hospital closure data on health center layer | The institutional story is about closures and their community impact | Medium | 14+ NYC closures with dates, neighborhoods, and community demographics. |
| Four individual composite stories with real statistics | Core emotional engagement mechanism | High | Each story needs 8-12 timeline events grounded in documented statistics. |
| Accessible color palettes | General public includes colorblind users; data viz ethics | Low | Use colorblind-safe palettes (e.g., ColorBrewer diverging schemes). |

## Differentiators

Features that set product apart. Not expected, but valued.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| HOLC redlining overlay on NYC map | Visually connects 1930s policy to present-day health gaps -- powerfully communicates structural racism | Medium | Mapping Inequality project has digitized HOLC polygons. Overlay as semi-transparent layer on existing NYC district map. |
| Animated causal loop diagram | Makes the feedback loop thesis visually explicit rather than implied | Medium | SVG-based loop diagram (civic exclusion -> underfunded institutions -> worse health -> reduced civic capacity -> ...) with animated arrows. Place at transition points between layers. |
| Timeline scrubber on individual stories | Lets users see how policy changes (ACA passage, Medicaid expansion, ICE enforcement shifts) intersect with health events | High | Requires careful data alignment. Each person's timeline should mark key policy dates alongside personal health events. |
| "What changed" policy callouts | At each layer, highlight specific policies or elections that shifted the data | Low | Text overlays triggered by hover/click. E.g., on state map: "Texas: Did not expand Medicaid. 18% uninsured." |
| Comparison mode (pick two districts/states) | Lets users see the contrast side-by-side | Medium | Split-screen or side-by-side panel showing two geographic units with same metrics. |
| FQHC location pins on NYC map | Shows where safety-net care exists vs. where hospitals closed | Low | HRSA Find a Health Center has geocoded data. Plot as dots on existing NYC SVG. |

## Anti-Features

Features to explicitly NOT build.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| Real-time data feeds / API calls | Adds infrastructure complexity; data changes slowly (annually); project constraint is client-only | Hardcode data snapshots with "Data as of [year]" labels. Update manually per release cycle. |
| Individual-level microdata or named real people | Privacy, consent, ethics. Project scope explicitly excludes this. | Composite portraits built from aggregate statistics with citations. |
| Complex statistical analysis / regression models | General public audience will not engage with p-values or confidence intervals | Show correlations visually. Use plain-language captions. Let the spatial patterns speak. |
| Interactive data filtering / faceting | Overcomplicates the narrative-driven design; turns it into a dashboard | Keep the four-level zoom as the primary interaction. Data complexity increases as you zoom in. |
| Predictive modeling ("what if" scenarios) | Speculative claims undermine credibility with a general audience | Show what IS, not what might be. Historical timelines are more trustworthy. |
| Mobile-responsive redesign | Out of scope per PROJECT.md constraints | Maintain existing layout. Ensure text is readable but do not rebuild for mobile. |

## Feature Dependencies

```
Real state data (Layer 0) -> no dependencies, can be done first
Real NYC district data (Layer 1) -> no dependencies, parallel with Layer 0
Bivariate color encoding -> requires real data for both layers (0 and 1)
HOLC redlining overlay -> requires NYC district map (Layer 1) base
Hospital closure data (Layer 2) -> no dependencies on other layers
FQHC location pins -> requires NYC district map base
Individual stories (Layer 3) -> depends on Layer 2 redesign (stories reference health center context)
Animated causal loop diagram -> no data dependencies, but should be designed after stories are written
Timeline scrubber -> depends on individual stories being written with dated events
Policy callouts -> depends on real data being in place for the relevant layer
Source citations -> depends on all data being finalized
```

## Visualization Approaches

### For the Feedback Loop (Core Challenge)

The central visualization challenge is showing **bidirectionality** -- that civic engagement affects health AND health affects civic engagement. Standard choropleth maps show correlation but not direction.

**Recommended approach: Bivariate choropleth + animated causal loop transitions**

1. **Bivariate choropleth maps** (Layers 0 and 1): Use a 3x3 color matrix where one axis represents civic engagement (voter turnout) and the other represents health outcome (life expectancy or uninsured rate). The legend is a 3x3 colored square. States/districts in the upper-right (high engagement, good health) are one color; lower-left (low engagement, poor health) are another. The diagonal pattern makes the correlation immediately visible. Source: CDC's own published guidance on bivariate choropleths for cancer screening data provides a proven template.

2. **Animated causal loop diagram** at layer transitions: When zooming between layers, briefly show an animated SVG loop diagram: arrows connecting "Civic Participation" -> "Health Policy & Funding" -> "Health Outcomes" -> "Capacity to Participate" -> back to start. Reinforcing loop notation (+). This makes the thesis explicit. Can be implemented with existing SVG + CSS animation stack.

3. **Annotated timeline** (Layer 3): Individual stories use a vertical timeline where health events (red dots) and civic/policy events (blue dots) are interleaved. Connecting lines or proximity shows how they relate. E.g., "2017: ACA repeal attempt" near "2018: Lost coverage, skipped screenings."

### For the Health Center Layer (Layer 2 Redesign)

Current architecture: hospital with departments and person figures. This should shift to an **institutional impact visualization**.

**Recommended approach: Before/after closure map + funding flow Sankey**

1. **Closure impact map**: Show NYC with hospital locations marked. Closed hospitals shown as crossed-out icons with year. Remaining hospitals shown as circles sized by ED visit volume. Draw connection lines showing patient redistribution after each closure. This directly visualizes the cascade effect.

2. **Funding flow diagram**: A simplified Sankey or flow diagram showing: Federal funding -> State allocation -> NYC H+H system -> Community health centers. Annotate with real dollar amounts from HRSA data and NYC Comptroller report. Show where funding has been cut or threatened.

3. **Community response panel**: Alongside the institutional data, show examples of civic action: the 2024 NYC Council oversight hearing on hospital closures, the new community input legislation (S8843/A1633A), NYC Care community organization partnerships. This demonstrates the feedback loop's positive direction: civic engagement protecting health infrastructure.

### For General Audience Accessibility

| Principle | Implementation |
|-----------|---------------|
| Progressive disclosure | Four zoom levels already implement this. Each level adds detail. |
| Plain language | All labels, tooltips, and captions use 8th-grade reading level. No jargon. |
| Emotional anchoring | Statistics at Layers 0-2 set up emotional resonance at Layer 3 (individual stories). |
| Color meaning consistency | Health = warm spectrum (green good, red bad). Civic = cool spectrum (blue high, gray low). Maintain across all layers. |
| Annotation over interaction | Do not require users to discover information through exploration. Key insights should be annotated directly on the visualization with callout text. |
| Narrative arc | The zoom structure IS the narrative: "The country -> your city -> your hospital -> your neighbor." Each layer answers "why?" for the previous one. |

## MVP Recommendation

**Prioritize (in order):**

1. **Real data for Layers 0 and 1** -- this is the foundation. Fake data actively undermines the project's credibility. All sources are freely available and can be hardcoded as JSON constants.
2. **Bivariate color encoding** on at least one map layer -- this is the minimum needed to show the two-variable relationship visually.
3. **Hospital closure data for Layer 2** -- the NYSNA list plus City Council testimony provides ready-made narrative content.
4. **One complete individual story (Story 3: elderly Black woman in redlined neighborhood)** -- this story has the richest data, the most layers of the feedback loop, and connects most directly to the NYC district map via HOLC overlay.

**Defer:**

- HOLC overlay: High impact but requires SVG path conversion from Mapping Inequality GeoJSON. Do after core data is in.
- Timeline scrubber: Nice interaction but not necessary for the story to land. Static timelines work.
- Comparison mode: Dashboard feature, not narrative feature. Lower priority.
- Animated causal loop diagram: Important for thesis clarity but can be added after all four layers have real data.

## Sources

### US State Data
- [CDC NVSS Life Expectancy - State Life Tables](https://www.cdc.gov/nchs/nvss/life-expectancy.htm) - HIGH confidence
- [CDC BRFSS Survey Data](https://www.cdc.gov/brfss/annual_data/annual_2024.html) - HIGH confidence
- [US Census ACS Health Insurance Coverage by State 2023-2024](https://www.census.gov/library/stories/2025/09/uninsured-rates.html) - HIGH confidence
- [University of Florida Election Lab 2024 Turnout](https://election.lab.ufl.edu/2024-general-election-turnout/) - HIGH confidence
- [Ballotpedia 2024 Turnout Analysis](https://ballotpedia.org/Election_results,_2024:_Analysis_of_voter_turnout_in_the_2024_general_election) - HIGH confidence

### NYC District Data
- [NYC DOHMH Community Health Profiles](https://www.nyc.gov/site/doh/data/data-publications/profiles.page) - HIGH confidence
- [NYC DOHMH EpiQuery](https://a816-health.nyc.gov/hdi/epiquery/) - HIGH confidence
- [NYC Campaign Finance Board Community Profiles](https://www.nyccfb.info/nyc-votes/community-profiles/) - HIGH confidence
- [NYC CFB 2024 Voter Analysis Report](https://www.nyccfb.info/media/reports/voter-analysis-report-2024/) - HIGH confidence

### Health Center / Institutional Data
- [NYSNA Hospital Closures Since 2000](https://www.nysna.org/resources/hospital-closures-2000) - HIGH confidence
- [NYC City Council Committee on Hospitals Hearing (Oct 2024)](https://citymeetings.nyc/meetings/new-york-city-council/2024-10-29-0100-pm-committee-on-hospitals/) - HIGH confidence
- [CSSNY Testimony on Hospital Closures Effects](https://www.cssny.org/news/entry/testimony-effects-hospital-closures-mccap-community-healthcare) - MEDIUM confidence
- [HRSA Health Center Data Explorer](https://data.hrsa.gov/topics/health-centers) - HIGH confidence
- [NYC Comptroller Report on NYC H+H (Dec 2025)](https://www.osc.ny.gov/files/reports/pdf/nyc-health-hospitals-strategic-initiatives.pdf) - MEDIUM confidence
- [NY Senate Bill S8843 - Community Input for Hospital Closures](https://www.nysenate.gov/newsroom/press-releases/2024/gustavo-rivera/lawmakers-introduce-bill-requiring-community-input) - HIGH confidence

### Individual Story Data
- [KFF/NYT 2025 Survey of Immigrants](https://www.kff.org/immigrant-health/kff-new-york-times-2025-survey-of-immigrants-health-and-health-care-experiences-during-the-second-trump-administration/) - HIGH confidence
- [THE CITY: Fear Drives Immigrants from Clinics (Aug 2025)](https://www.thecity.nyc/2025/08/12/ice-fear-undocumented-immigrant-medical-services-nyccare/) - HIGH confidence
- [KFF State Health Coverage for Immigrants](https://www.kff.org/racial-equity-and-health-policy/state-health-coverage-for-immigrants-and-implications-for-health-coverage-and-care/) - HIGH confidence
- [NCRC Redlining and Neighborhood Health](https://ncrc.org/holc-health/) - HIGH confidence
- [NYC DOHMH Data Story: Redlining](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-stories/redlining/) - HIGH confidence
- [PMC: Historical Redlining and COVID-19 in NYC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8212581/) - HIGH confidence
- [Mapping Inequality (HOLC digitized maps)](https://dsl.richmond.edu/panorama/redlining/) - HIGH confidence
- [Sentencing Project: NY Voting Rights Restoration](https://www.sentencingproject.org/fact-sheet/new-york-should-restore-voting-rights-to-over-33000-citizens/) - HIGH confidence
- [NYCLU: Racial Disparity in Disenfranchisement](https://www.nyclu.org/commentary/racial-disparity-across-new-york-truly-jarring) - HIGH confidence
- [AMA Journal of Ethics: Health Justice and Reentry](https://journalofethics.ama-assn.org/article/what-does-health-justice-look-people-returning-incarceration/2017-09) - HIGH confidence

### Visualization Approaches
- [CDC Bivariate Mapping Tutorial for Public Health](https://www.cdc.gov/pcd/issues/2020/19_0254.htm) - HIGH confidence
- [Joshua Stevens: Bivariate Choropleth Guide](https://www.joshuastevens.net/cartography/make-a-bivariate-choropleth-map/) - MEDIUM confidence
- [PMC: Visual Communication of Public Health Data (scoping review)](https://pmc.ncbi.nlm.nih.gov/articles/PMC12060258/) - MEDIUM confidence
- [PMC: Community Engagement in Health Data Visualization](https://pmc.ncbi.nlm.nih.gov/articles/PMC10797278/) - MEDIUM confidence
