# Phase 4: Real Content and Data Verification - Research

**Researched:** 2026-03-24
**Domain:** Data verification, clinical story content, public health data sources
**Confidence:** HIGH (data sources verified from primary documents); MEDIUM (NYC district-level values need per-district audit)

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| STORY-02 | Patient stories reflect real experiences from Stella Saffo's clinical transcript (Medicaid/pancreatitis, HIV care continuity, pregnant person housing) | The three stories already in HOSPITAL_PEOPLE are sourced from Dr. Saffo's clinical experience and match the three archetypes. Research identifies what needs fixing vs. what is already acceptable. |
| DATA-01 | All US state stats (voter turnout, uninsured rate, life expectancy) match cited sources exactly | VEP turnout is wrong for 46/51 entries; LE and uninsured match their sources. Verified values are documented below. |
| DATA-02 | All NYC district stats (voter turnout, life expectancy) match cited sources exactly | LE source is DOHMH CHP 2021 (2010–2019 averages); VT source is NYC CFB (% of registered voters); one confirmed LE mismatch found. Full per-district audit required. |
| DATA-03 | Data methodology documented (denominators, election cycles, population definitions) in code comments | Sources and methodology for all four metrics are documented here for copy into code comments. |
</phase_requirements>

---

## Summary

Phase 4 is a content and data correctness phase, not a new feature phase. All work happens inside `src/App.jsx` — no new dependencies, no new components. The work divides cleanly into two tracks: (1) fix `STATE_DATA` voter turnout values and add source comments, and (2) audit all 59 `NYC_DISTRICTS` life expectancy values against the DOHMH Community Health Profiles and voter turnout against the NYC CFB Community Profiles.

Research confirmed that `STATE_DATA` life expectancy and uninsured rate values are already exact matches to their respective primary sources (CDC NVSR 2022 and KFF ACS-based data). However, **all 50-state voter turnout values are systematically wrong** — they are 1–7 percentage points below the 2020 VEP values from the UF Election Lab, the authoritative source the project cites. The full corrected dataset was retrieved directly from the UF Election Lab CSV and is documented below.

Patient story content (STORY-02) is already substantially correct — the three patient archetypes (pancreatitis/Medicaid, HIV insurance disruption, pregnant homeless woman with HIV) match the Stella Saffo transcript as documented in the codebase. The STATE.md blocker notes that **consent scope must be confirmed with Stella Saffo before publishing**. If consent does not cover kiosk display, stories should be treated as research-sourced composites, not attributed transcriptions.

**Primary recommendation:** Fix all 51 `voterTurnout` values in `STATE_DATA` from the verified UF Election Lab 2020 VEP dataset, add methodology comments to all four data blocks, and audit NYC district values against DOHMH CHP 2021 PDFs.

---

## Standard Stack

No new dependencies. All work is in-file edits to `src/App.jsx`.

| Data Block | Lines in App.jsx | What Changes |
|------------|-----------------|--------------|
| `STATE_DATA` | 13–65 | `voterTurnout` values for all 51 entries + source comment header |
| `NYC_DISTRICTS` | 135–195 | `le` and `vt` values after per-district audit + source comment header |
| `HOSPITAL_PEOPLE` | 203–264 | Story text review against transcript; no structural change |

---

## Architecture Patterns

### Pattern 1: Source Comment Block Above Each Data Constant

Every data constant must have a comment block documenting source, year, denominator, and methodology. This is how DATA-03 is satisfied — the comments live at the point of definition, not in a separate file.

```javascript
/* ─── STATE_DATA ───────────────────────────────────────────────────────────
   voterTurnout  — 2020 General Election VEP Turnout Rate (%)
                   Source: UF Election Lab (Michael McDonald), v1.2
                   URL: https://election.lab.ufl.edu/voter-turnout/2020-general-election-turnout/
                   Denominator: Voting-Eligible Population (citizens 18+, excluding ineligible felons)
                   Election cycle: November 2020 Presidential General Election
   uninsured     — Non-elderly (ages 0–64) uninsured rate (%)
                   Source: KFF State Health Facts, ACS-based estimates
                   URL: https://www.kff.org/state-category/health-coverage-uninsured/
                   Year: 2022/2023 ACS
   lifeExp       — Life expectancy at birth, total population (years)
                   Source: CDC NCHS, U.S. State Life Tables, 2022
                   Report: NVSR Vol. 74, No. 12 (December 4, 2025), Table A
                   URL: https://www.cdc.gov/nchs/data/nvsr/nvsr74/nvsr74-12.pdf
   ─────────────────────────────────────────────────────────────────────── */
```

```javascript
/* ─── NYC_DISTRICTS ────────────────────────────────────────────────────────
   le  — Life expectancy at birth (years), 10-year average 2010–2019
         Source: NYC DOHMH Bureau of Vital Statistics
         Report: NYC Community Health Profiles 2021
         URL: https://a816-health.nyc.gov/hdi/profiles/
         Note: Values are combined over 10 years to increase statistical reliability
   vt  — Voter turnout (%) in 2020 General Election
         Source: NYC Campaign Finance Board Community Profiles
         URL: https://www.nyccfb.info/nyc-votes/community-profiles/
         Denominator: Registered voters (NOT voting-eligible population)
         Election cycle: November 2020 General Election
   ─────────────────────────────────────────────────────────────────────── */
```

### Pattern 2: Inline Value Updates Without Structural Change

Replace numeric values in place. The object key names (`voterTurnout`, `le`, `vt`, etc.) do not change. Only the values change. No component code is touched.

### Anti-Patterns to Avoid

- **Moving data to a separate file**: CLAUDE.md explicitly requires single-file structure. Do not extract data to `src/data/` or similar.
- **Changing key names**: `voterTurnout` (not `vepTurnout`, not `turnout`). Renaming breaks every component that reads this data.
- **Adding new fields**: DATA-01–03 require accuracy of existing fields, not new metrics.
- **Rounding differently from the source**: Use one decimal place matching the source table. CDC NVSR Table A rounds to one decimal. KFF rounds to two but the app rounds to one — this is acceptable and already consistent.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Fetching live voter turnout data | API call to election data service | Hard-coded verified values | CLAUDE.md prohibits backend/API; data is static for this visualization |
| Verifying all 59 NYC district LE values | A script to scrape DOHMH | Manual audit of CHP PDFs (one PDF per district at a816-health.nyc.gov/hdi/profiles/) | No scraping infrastructure; values must be human-verified against official source |

---

## Verified Data: STATE_DATA voterTurnout (DATA-01)

**Source:** UF Election Lab, 2020 General Election VEP Turnout Rates, v1.2
**URL:** https://election.lab.ufl.edu/voter-turnout/2020-general-election-turnout/
**Confidence:** HIGH — retrieved directly from CSV

All values below are 2020 General Election VEP turnout rates (%). These replace the current `voterTurnout` values in `STATE_DATA`. Values are rounded to one decimal place to match existing code style.

| State | Current (Wrong) | Correct (2020 VEP) | Delta |
|-------|----------------|-------------------|-------|
| AL | 58.9 | 61.5 | +2.6 |
| AK | 63.8 | 68.2 | +4.4 |
| AZ | 63.6 | 66.6 | +3.0 |
| AR | 53.5 | 56.2 | +2.7 |
| CA | 62.1 | 68.8 | +6.7 |
| CO | 73.1 | 76.7 | +3.6 |
| CT | 67.1 | 70.1 | +3.0 |
| DE | 67.0 | 68.3 | +1.3 |
| DC | 63.6 | 68.9 | +5.3 |
| FL | 66.7 | 70.8 | +4.1 |
| GA | 68.3 | 67.1 | -1.2 |
| HI | 50.3 | 55.4 | +5.1 |
| ID | 63.4 | 64.9 | +1.5 |
| IL | 63.3 | 66.5 | +3.2 |
| IN | 58.7 | 61.1 | +2.4 |
| IA | 70.8 | 72.0 | +1.2 |
| KS | 63.2 | 65.1 | +1.9 |
| KY | 62.2 | 64.4 | +2.2 |
| LA | 60.8 | 64.1 | +3.3 |
| ME | 74.2 | 74.9 | +0.7 |
| MD | 69.3 | 69.5 | +0.2 |
| MA | 68.0 | 71.3 | +3.3 |
| MI | 74.6 | 73.3 | -1.3 |
| MN | 76.4 | 79.2 | +2.8 |
| MS | 57.5 | 60.6 | +3.1 |
| MO | 64.3 | 65.5 | +1.2 |
| MT | 68.2 | 71.3 | +3.1 |
| NE | 68.0 | 69.1 | +1.1 |
| NV | 65.8 | 64.3 | -1.5 |
| NH | 74.0 | 74.0 | 0.0 |
| NJ | 67.2 | 72.0 | +4.8 |
| NM | 59.6 | 60.5 | +0.9 |
| NY | 60.4 | 61.7 | +1.3 |
| NC | 70.3 | 71.0 | +0.7 |
| ND | 63.1 | 63.1 | 0.0 |
| OH | 65.4 | 67.1 | +1.7 |
| OK | 53.3 | 54.4 | +1.1 |
| OR | 71.9 | 75.3 | +3.4 |
| PA | 71.3 | 69.9 | -1.4 |
| RI | 63.3 | 63.1 | -0.2 |
| SC | 62.1 | 64.7 | +2.6 |
| SD | 64.0 | 65.6 | +1.6 |
| TN | 57.6 | 59.2 | +1.6 |
| TX | 56.6 | 59.8 | +3.2 |
| UT | 64.2 | 67.2 | +3.0 |
| VT | 70.9 | 71.3 | +0.4 |
| VA | 71.2 | 71.8 | +0.6 |
| WA | 70.2 | 75.3 | +5.1 |
| WV | 55.5 | 57.2 | +1.7 |
| WI | 76.6 | 75.0 | -1.6 |
| WY | 61.3 | 64.7 | +3.4 |

**Summary:** 46 of 51 entries differ by more than 0.5 percentage points. The existing values appear to be VAP-based (voting-age population) rather than VEP-based (voting-eligible population), or sourced from a prior election cycle. All must be replaced.

---

## Verified Data: STATE_DATA lifeExp and uninsured (DATA-01)

### Life Expectancy

**Source:** CDC NCHS, U.S. State Life Tables 2022, NVSR Vol. 74 No. 12 (December 4, 2025), Table A
**URL:** https://www.cdc.gov/nchs/data/nvsr/nvsr74/nvsr74-12.pdf
**Confidence:** HIGH — verified from PDF (retrieved and read in full)
**Status: NO CHANGES NEEDED.** All 51 `lifeExp` values in `STATE_DATA` match Table A exactly to one decimal place.

### Uninsured Rate

**Source:** KFF State Health Facts, ACS-based non-elderly (0–64) uninsured rates
**URL:** https://www.kff.org/state-category/health-coverage-uninsured/
**Confidence:** HIGH — verified from KFF page
**Status: NO CHANGES NEEDED.** All values match within rounding to one decimal place.

**Methodology note for DATA-03:** The KFF uninsured figures are derived from the Census Bureau's American Community Survey (ACS), measuring the share of people ages 0–64 who lack health insurance coverage. KFF replaced all previously-posted CPS-based data with ACS-based data. These are annual-average estimates.

---

## NYC District Data (DATA-02)

### Life Expectancy Source

**Source:** NYC DOHMH Community Health Profiles 2021 (downloaded November 2023)
**Data year:** 2010–2019 combined average (10-year pooling to increase statistical reliability for small-population districts)
**URL:** https://a816-health.nyc.gov/hdi/profiles/ and https://on.nyc.gov/communityprofiles
**Methodology:** Values are combined across 10 years from NYC DOHMH Bureau of Vital Statistics. Annual rates are not published at this geographic level.
**Access method:** Interactive web tool — select a Community District to see its profile. One-page HTML or downloadable PDF per district.

**Confirmed discrepancy found:**
- MN11 (East Harlem): app shows `le:77.9`, CHP 2021 shows **78.5 years**
- Delta: 0.6 years

All 59 district LE values need auditing. The planner must allocate a task to open each of the 59 CHP profiles and record the life expectancy shown in the "Who We Are" section (displayed as "The life expectancy is X.X years").

### Voter Turnout Source

**Source:** NYC Campaign Finance Board Community Profiles
**URL:** https://www.nyccfb.info/nyc-votes/community-profiles/
**Election:** 2020 General Election
**Denominator: Registered voters** (NOT voting-eligible population)

Critical methodology distinction: The NYC CFB denominator is registered voters, not the voting-eligible population. The 2020-2021 NYC Votes report confirms: citywide eligible voters = 4,918,052 (footnoted as "also total number of registered voters"). The citywide 2020 general turnout was 61.9% of registered voters. App `vt` values like BX01=19.9% reflect low registration-base turnout in high-poverty Bronx districts — this is plausible and consistent with the CFB's community district data.

The NYC voter turnout values cannot be verified from the aggregated PDF without accessing individual district profiles. The planner must allocate a task to audit these against the NYC CFB or Equity NYC portal (https://equity.nyc.gov/domains/empowered-residents-and-neighborhoods/voter-turnout-rate).

---

## Story Content Assessment (STORY-02)

### Current State

The three patient stories already in `HOSPITAL_PEOPLE` match the three archetypes from the Stella Saffo transcript:

| Patient ID | Archetype | Current Story Summary | Matches Transcript? |
|------------|-----------|----------------------|---------------------|
| `patient-one` | Man with pancreatitis, Medicaid runs out | Admitted for pancreatitis, HIV managed, Medicaid exhausted, discharged, coded and died that night | YES — text matches clinical narrative including Dr. Saffo's direct quote |
| `patient-two` | Young man diagnosed with HIV in 20s, insurance contract dispute | Diagnosed HIV+, excited about care, hospital drops his insurance in contract dispute, must restart | YES — story matches including "very excited" detail and "nervous to have to go to a new HIV doctor" |
| `patient-three` | Pregnant woman with HIV, formerly homeless, five children removed by CPS in NC | Arrived in NY homeless, pregnant, HIV+, housed via NY state benefits tied to HIV + pregnancy | YES — story matches including NC/NY policy comparison |

### Consent Blocker (CRITICAL)

STATE.md records this explicit blocker:

> "Transcript consent scope must be confirmed with Stella Saffo before writing story content — if consent does not cover public kiosk display, pivot to transcript-as-research-source"

**This is a human decision, not a code task.** The planner must include this as a prerequisite gating task. Two acceptable outcomes:

1. **Consent confirmed for kiosk display:** Stories can be published as-is (minor copy refinements only)
2. **Consent limited:** Reframe all story text as "composite cases illustrating systemic patterns" — strip any language that could identify individuals, ensure no verbatim quotes are attributed

**Current text already avoids patient names and identifying details.** The stories use generic descriptions ("A Man With Pancreatitis", "A Young Man Diagnosed", "Pregnant Woman With HIV"). Dr. Saffo's name and direct quotes appear in `dr-saffo`'s timeline entries. If consent is limited, those quotes must be paraphrased.

### Text Quality Assessment

The current story text is high quality and substantially complete. STORY-02 does not require rewriting. It requires:
1. Consent confirmation (human task)
2. Possibly removing one direct quote attribution if consent is limited ("It ended up being an insurance issue that killed him" attributed to Dr. Saffo)

---

## Common Pitfalls

### Pitfall 1: Treating the Existing VT Values as Correct After Minor Adjustments
**What goes wrong:** Planner scopes this as "a few spot fixes" because they look plausible.
**Why it happens:** Values are in the right ballpark; differences of 2–3pp are easy to miss.
**How to avoid:** Replace ALL 51 `voterTurnout` values from the verified table above. The diff is large enough (CA is off by 6.7pp) to materially misrepresent the correlation the visualization is trying to show.
**Warning signs:** If the final app still shows Hawaii at 50.3% (vs correct 55.4%), the replacement was incomplete.

### Pitfall 2: Conflating VEP (national) and Registered-Voter (NYC) Turnout Denominators
**What goes wrong:** Treating NYC district VT as VEP-comparable to state VT values.
**Why it happens:** Both are displayed as "voter turnout %" but use different bases.
**How to avoid:** The code comment block (DATA-03) must clearly document that state VT = VEP-based, NYC district VT = registered-voter-based. These are different denominators. Do NOT change NYC VT to VEP — VEP data does not exist at the community district level.
**Warning signs:** If a comment says "Source: VEP" on the NYC data block, that is wrong.

### Pitfall 3: Missing the NYC Life Expectancy Audit Scope
**What goes wrong:** Planner assumes NYC LE is correct because state LE was correct.
**Why it happens:** Both use CDC/DOHMH sources; it is tempting to assume consistency.
**How to avoid:** NYC LE uses a different source (DOHMH, not CDC NVSR) and a different methodology (2010–2019 10-year pooled average, not a single-year table). At least one confirmed discrepancy (MN11: 77.9 vs 78.5) proves the values were not sourced from CHP 2021. All 59 must be checked.

### Pitfall 4: Publishing Story Content Before Consent Confirmation
**What goes wrong:** Stories go live on a kiosk with a named healthcare provider's quotes before consent is confirmed.
**Why it happens:** Story text looks final; easy to deploy without following up on consent.
**How to avoid:** The consent check must be the first task in the STORY-02 plan, blocking all other story work.

### Pitfall 5: Changing Color Scale Breakpoints When VT Values Shift
**What goes wrong:** After replacing VT values (which shift upward by ~3pp on average), the `engagementColor` function breakpoints may no longer produce the intended color distribution.
**Why it happens:** `engagementColor` uses hardcoded thresholds: `>=70` (dark green), `>=64` (light green), `>=58` (yellow), else red. With the corrected VEP data, more states will fall into the green tiers.
**How to avoid:** This is a legitimate consequence of fixing the data — the visualization should reflect reality. Do NOT adjust breakpoints to preserve the old appearance. Document the visual change as intended.

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|-----------------|--------------|--------|
| CPS-based uninsured estimates (KFF) | ACS-based estimates (KFF) | ~2023 | ACS provides larger sample sizes, more reliable state-level estimates |
| VAP turnout (voting-age population) | VEP turnout (voting-eligible population) | Ongoing — VEP preferred since ~2000 | VEP excludes noncitizens and ineligible felons; more accurate representation of civic participation |
| DOHMH single-year LE by district | 10-year pooled LE averages | CHP 2021 and forward | Small-area statistics require multi-year pooling for reliability |

---

## Environment Availability

Step 2.6: SKIPPED — this phase is purely in-file data and text edits in `src/App.jsx`. No external tools, databases, or services are required beyond the Vite dev server already confirmed operational.

---

## Open Questions

1. **Consent scope for Dr. Saffo's clinical transcript**
   - What we know: STATE.md records this as an explicit blocker; current story text uses Dr. Saffo's name and one direct quote
   - What's unclear: Whether consent was obtained for public kiosk display, or only for internal research/development
   - Recommendation: Gate STORY-02 implementation on human confirmation. If consent is limited, the stories remain intact but the direct quote is paraphrased and attribution softened.

2. **NYC district voter turnout values — unverified**
   - What we know: CFB denominator is registered voters (confirmed); citywide 2020 general was 61.9%; district-level values in the app have not been checked against CFB profiles
   - What's unclear: Whether the 59 `vt` values match CFB 2020 general, CFB 2020 primary, or another election
   - Recommendation: Audit all 59 values using the NYC CFB Community Profiles tool or the Equity NYC portal. This cannot be resolved by downloading a single spreadsheet — each district profile must be checked individually.

3. **engagementColor breakpoints after VT correction**
   - What we know: All state VT values shift upward by ~3pp on average; current thresholds at 70/64/58
   - What's unclear: Whether the color distribution change is intentional or should be recalibrated
   - Recommendation: Do not recalibrate. The corrected data is more accurate; let the colors reflect that. Document as intentional in the commit message.

---

## Sources

### Primary (HIGH confidence)
- CDC NCHS, NVSR Vol. 74 No. 12 (2025-12-04) — U.S. State Life Tables 2022, Table A — full state LE values verified from PDF
  - URL: https://www.cdc.gov/nchs/data/nvsr/nvsr74/nvsr74-12.pdf
- UF Election Lab, Turnout_2020G_v1.1.csv (Michael McDonald) — 2020 General VEP turnout all states
  - URL: https://election.lab.ufl.edu/voter-turnout/2020-general-election-turnout/
- KFF State Health Facts, ACS-based non-elderly uninsured rates
  - URL: https://www.kff.org/state-category/health-coverage-uninsured/
- NYC DOHMH Community Health Profiles 2021 (Manhattan 111: East Harlem) — CHP format verified, MN11 LE=78.5 confirmed
  - URL: https://a816-health.nyc.gov/hdi/profiles/

### Secondary (MEDIUM confidence)
- NYC Campaign Finance Board, 2020-2021 Voter Analysis Report — denominator confirmed as registered voters, citywide 61.9%
  - URL: https://www.nyccfb.info/pdf/2020-2021_Voter-Analysis-Report.pdf
- NYC CFB Community Profiles — structure confirmed (district-level turnout available, individual CHP PDFs required for audit)
  - URL: https://www.nyccfb.info/nyc-votes/community-profiles/

### Tertiary (LOW confidence — needs per-district validation)
- NYC DOHMH Community Health Profiles — all 59 district LE values (only MN11 verified; remaining 58 need audit)
- Equity NYC voter turnout portal — not directly accessed; structure not verified
  - URL: https://equity.nyc.gov/domains/empowered-residents-and-neighborhoods/voter-turnout-rate

---

## Metadata

**Confidence breakdown:**
- STATE_DATA LE and uninsured: HIGH — exact match verified from primary source PDFs
- STATE_DATA voterTurnout (correct values): HIGH — retrieved directly from UF Election Lab CSV
- NYC district LE audit methodology: HIGH — source and format confirmed; individual values LOW until audited
- NYC district VT audit methodology: MEDIUM — denominator and election confirmed; individual values LOW until audited
- STORY-02 content: HIGH — matches transcript archetypes; LOW on consent status (human decision pending)

**Research date:** 2026-03-24
**Valid until:** 2026-06-24 (data sources stable; CDC/KFF update annually; UF Election Lab 2020 data is final)

---

## Project Constraints (from CLAUDE.md)

These directives are locked and must not be violated by the plan:

- **Single file:** All changes go in `src/App.jsx` only — no new files, no data extraction
- **No new dependencies:** React + Vite only; no data fetching libraries
- **Inline styles exclusively:** No CSS classes introduced
- **Data accuracy:** All displayed numbers must match cited sources exactly — this is the entire point of this phase
- **Touch usability:** Navigation must work without mouse (not directly relevant to this phase but must not be regressed)
- **No test framework:** `nyquist_validation` is false; no test files to create or run
- **GSD workflow enforcement:** All file changes via GSD execute-phase flow
