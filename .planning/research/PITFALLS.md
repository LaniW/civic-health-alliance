# Pitfalls Research

**Domain:** Civic health data visualization with individual stories of marginalized communities
**Researched:** 2026-03-10
**Confidence:** HIGH (grounded in Urban Institute Do No Harm guide, CDC data equity principles, published health equity research, and ecological fallacy literature)

## Critical Pitfalls

### Pitfall 1: Ecological Fallacy -- Inferring Individual Causation from Aggregate Data

**What goes wrong:**
The site's core thesis is that low civic participation causes worse health outcomes (and vice versa). The data backing this comes from state-level and district-level aggregates (voter turnout, uninsured rates, life expectancy). Presenting a state-level correlation as if it proves that *an individual* who does not vote will have worse health is the ecological fallacy. A classic example from the literature: states with more immigrants had higher average literacy, but individual immigrants were on average less literate than native-born citizens. The aggregate relationship was the opposite of the individual one.

This project is especially vulnerable because it zooms from aggregate (US map, NYC districts) to individual stories. That zoom implies the aggregate pattern explains the individual case, which it may not.

**Why it happens:**
The four-layer zoom structure (US -> NYC districts -> health center -> person) creates an implicit causal chain from macro data to micro narrative. Developers and designers treat the zoom as a storytelling arc without realizing it is also an inferential leap across levels of analysis.

**How to avoid:**
- Never present state/district correlations as proof of individual-level causation. Use language like "In neighborhoods with lower voter turnout, residents face higher uninsured rates" rather than "People who do not vote are more likely to be uninsured."
- Acknowledge structural mechanisms explicitly: "Policies like the Medicaid 5-year bar create health gaps for legal permanent residents" is a verifiable policy-level claim. "Low civic participation causes Maria's health crisis" is an ecological fallacy unless her specific story documents a direct mechanism.
- Individual stories should illustrate *specific documented mechanisms* (policy exclusion, hospital closure, funding cuts), not be presented as instances of a statistical pattern.
- Include a methodology note explaining that aggregate data shows associations, while individual stories illustrate known policy mechanisms.

**Warning signs:**
- Captions or labels that say "because" when connecting aggregate data to individual outcomes.
- Hover tooltips that imply a person's health outcome was directly caused by their district's civic participation rate.
- Story narratives that reference the aggregate statistics as explanations for individual events.

**Phase to address:**
Data integration phase (when real data replaces fake data). The data model and narrative framing must be designed together -- retrofitting caveats after visualizations are built will feel like disclaimers rather than honest framing.

---

### Pitfall 2: Deficit Framing -- Reducing People to Their Suffering

**What goes wrong:**
The four individual stories center people defined by exclusion: undocumented immigrant, Medicaid-barred LPR, elderly Black woman in a redlined neighborhood. When the primary identity of a person in the visualization is their victimhood, it reproduces what health equity researchers call "deficit framing" -- presenting marginalized groups as passive recipients of harm rather than agents with resilience, community ties, and resistance strategies.

The Urban Institute's Do No Harm Guide specifically warns that data visualizations can "perpetuate harmful narratives about health disparities" by encoding people as data points of suffering. The MDPI research on immigrant health notes that behavioral/cultural frameworks "place the onus on the individual" rather than on structures.

This is not just an ethical concern -- it also makes the visualization less persuasive. Audiences disengage from what feels like "poverty porn."

**Why it happens:**
The project's thesis requires showing harm. Developers focus on demonstrating the severity of the problem and assume that more suffering shown = more persuasion. Stories are built around the worst moments (denied coverage, hospital closure, untreated illness) because those are the data points that prove the thesis.

**How to avoid:**
- Each story must show agency alongside harm. Maria is not just "denied Medicaid for 5 years" -- she is also navigating community health networks, raising a family, participating in mutual aid. The timeline should include civic *participation* moments (community organizing, protests, voter registration drives in her community) not only civic *exclusion* moments.
- Use people-first language everywhere: "a woman living in a historically redlined neighborhood" not "a redlining victim." "A legal permanent resident affected by the Medicaid bar" not "a Medicaid-barred immigrant."
- Frame the structural system as the subject of critique, not the person. The 5-year bar is the problem, not the person's immigration status. Redlining policy is the problem, not the neighborhood's current poverty.
- Include a "What changed" or "What could change" element in each story, showing policy interventions that would break the cycle.

**Warning signs:**
- Story timelines that contain only negative health events and policy harms with no positive community or agency moments.
- Visual design that uses dark/muted colors exclusively for stories about people of color or immigrants (color-coding suffering by race).
- No mention of community organizing, mutual aid, or policy advocacy within individual stories.
- Review feedback from community members that the stories feel exploitative.

**Phase to address:**
Story research and content creation phase. This must be baked into the story templates *before* writing begins -- adding agency retroactively feels tokenistic.

---

### Pitfall 3: Conflating Undocumented and Legal Immigrant Experiences

**What goes wrong:**
The project includes both an undocumented immigrant and a legal permanent resident under the Medicaid 5-year bar. These are fundamentally different legal, social, and health situations, but they are commonly conflated in public discourse and data visualization. KFF research documents widespread public confusion: about half of U.S. adults incorrectly believe most immigrants are eligible for Medicaid upon arrival. Georgetown CCF documents that "wildly inaccurate reporting" claims undocumented immigrants drive Medicaid spending when they are not even eligible for traditional Medicaid.

If the visualization blurs these distinctions, it reinforces the very misinformation it aims to counter.

**Why it happens:**
Both stories fall under "immigration status as civic exclusion" and developers group them together. The data sources often aggregate "non-citizen" populations without distinguishing legal status. The visual zoom from NYC districts to health center to person does not have an obvious place to surface legal distinctions.

**How to avoid:**
- Treat these as categorically different stories with different mechanisms. The undocumented person faces total exclusion from public health insurance. The LPR faces a time-limited bar that is a specific policy choice (1996 PRWORA). These are different policy problems with different solutions.
- Label immigration statuses precisely. Never use "immigrant" as a catch-all when legal status matters.
- Cite specific policy: "The Personal Responsibility and Work Opportunity Reconciliation Act of 1996 bars most legal permanent residents from Medicaid for their first five years" is defensible. "Immigrants cannot get healthcare" is not.
- Note state-level variation: some states (like New York) have used state funds to cover immigrants during the 5-year bar period for children and pregnant women. The story must reflect the specific policy landscape of NYC.

**Warning signs:**
- Using the word "immigrant" without specifying legal status in any data label, tooltip, or narrative text.
- Presenting both stories with the same visual treatment or in the same category.
- Data sources that do not distinguish between documented and undocumented populations being used as if they do.

**Phase to address:**
Data research phase (before story construction). The data sources must be vetted for whether they distinguish legal statuses. If they do not, the limitation must be disclosed.

---

### Pitfall 4: Redlining Narrative Oversimplification -- Ignoring Gentrification and Displacement

**What goes wrong:**
The story of an elderly Black woman in a historically redlined neighborhood risks presenting redlining as a static historical event with a straight line to current health outcomes. Research (Harvard, JAMA Network Open) shows the reality is more complex: 87% of gentrifying neighborhoods in San Francisco were once redlined. Many formerly redlined NYC neighborhoods have been gentrified, meaning the *current residents* may not be the same population that suffered the original disinvestment. The people displaced by gentrification now live elsewhere, potentially in areas with different health profiles.

Showing a redlined area's current health data as proof of redlining's harm may actually be showing gentrification's effects, or may undercount harm because the most affected people were displaced.

**Why it happens:**
The redlining-to-health-outcomes pipeline is well-documented and emotionally compelling. Overlaying 1930s HOLC maps on current health data is a common and visually striking technique. Developers use it because it is dramatic and the data is readily available (HOLC maps are digitized, current health data is public). The complexity of displacement disrupts the clean narrative.

**How to avoid:**
- Acknowledge gentrification explicitly in the story. If the neighborhood has been gentrified, say so. Show how displacement compounds the original harm of redlining rather than pretending the neighborhood is frozen in 1935.
- Use the specific NYC DOH data that tracks health outcomes by race *within* neighborhoods, not just neighborhood-level aggregates. A gentrified neighborhood may have good average health but persistent disparities for long-term Black residents.
- The elderly woman's story should include her relationship to the neighborhood over time -- did she stay through gentrification? Did her neighbors leave? This is part of the health story (social isolation, loss of community health resources, cultural displacement stress).
- Reference NYC-specific sources: the NYC Environment and Health Data Portal has a data story specifically about redlining's legacy in NYC neighborhoods.

**Warning signs:**
- Using HOLC map grades from the 1930s as the sole explanatory variable for current health outcomes.
- Neighborhood-level health data presented without acknowledging demographic changes since redlining.
- No mention of gentrification, displacement, or neighborhood change in the redlining story.

**Phase to address:**
Story research phase, specifically when sourcing NYC district-level data. Must verify whether the chosen neighborhood's current demographics match the redlining narrative being told.

---

### Pitfall 5: Making Legally and Politically Vulnerable Claims Without Defensible Sources

**What goes wrong:**
The site "makes political/policy claims using real data" (per project context). In the current political climate around immigration, Medicaid, and racial equity, inaccurate or poorly sourced claims can be weaponized against the project's goals. A single incorrect statistic about immigrant healthcare costs or a misattributed health outcome can discredit the entire project and, worse, provide ammunition for counter-narratives.

Georgetown CCF's 2026 fact-checking work documents how even small inaccuracies in immigrant health data are amplified by opponents of immigrant healthcare access.

**Why it happens:**
Advocacy-oriented projects prioritize narrative impact over source rigor. Developers use the most compelling statistic rather than the most defensible one. Secondary sources (news articles, advocacy org summaries) are cited instead of primary data (CDC, Census, peer-reviewed studies). Data is presented without vintage dates, making it unclear whether it reflects current policy or historical policy.

**How to avoid:**
- Every data point displayed on the site must trace to a primary public source (CDC, Census Bureau, NYC DOH, BLS, peer-reviewed journal). Maintain a source registry mapping each visualization element to its source, date, and methodology notes.
- Include data vintage dates in the visualization. "Uninsured rate: 12.3% (ACS 2023)" not just "Uninsured rate: 12.3%."
- Distinguish between federal policy and NYC/New York State policy. New York has expanded coverage beyond federal minimums in several areas -- presenting federal-only data as NYC reality is inaccurate.
- Pre-emptively fact-check the most politically charged claims (immigrant Medicaid eligibility, healthcare spending by immigration status, redlining and health correlations) against hostile interpretations. Ask: "How would someone trying to discredit this read this data point?"
- Do not round or simplify numbers in ways that change their meaning. "Less than 1% of Medicaid spending" (the real figure for emergency Medicaid) is very different from "a small amount."

**Warning signs:**
- Any data point that lacks a traceable primary source.
- Statistics cited from news articles or advocacy organizations without checking the underlying data.
- Data presented without year/vintage.
- Claims about immigrant healthcare eligibility that do not specify legal status category and jurisdiction.

**Phase to address:**
Data research and verification phase. Must be complete *before* visualization implementation begins. A source registry should be a deliverable of the data phase, not an afterthought.

---

### Pitfall 6: Accessibility Theater -- "General Public" Audience Without Actual Accessibility

**What goes wrong:**
The project targets the "general public" audience with SVG-based interactive visualizations. SVG visualizations are notoriously inaccessible to screen readers, keyboard navigation, and users with cognitive disabilities. A site about health equity that is itself inaccessible to disabled users is a credibility-destroying irony.

Beyond assistive technology, "general public" includes people with low data literacy. If the visualization requires understanding of correlation coefficients, confidence intervals, or geographic data encoding, most of the intended audience cannot use it.

**Why it happens:**
Developers build for sighted mouse users first. SVG elements lack semantic HTML structure. ARIA labels are afterthoughts. The four-layer zoom interaction (click state -> click district -> click room -> click person) is intuitive with a mouse but opaque with keyboard navigation. Data literacy is assumed because the development team has it.

**How to avoid:**
- Every SVG visualization element must have ARIA labels and be keyboard-navigable. Use `role="img"` with `aria-label` for decorative SVGs. Use `role="group"` with labeled children for interactive SVGs.
- Provide text alternatives for every data visualization. Not just alt text, but a "View as table" or "Read summary" option for each layer.
- Test with screen readers (NVDA, VoiceOver) at each layer of the zoom.
- Write all narrative text at an 8th-grade reading level. Use the Hemingway Editor or Flesch-Kincaid readability scoring.
- Avoid jargon: "uninsured rate" needs a tooltip definition. "Civic engagement" needs a plain-language explanation. "Redlining" needs a one-sentence historical context on first appearance.
- Color choices must pass WCAG AA contrast ratios (4.5:1 for text, 3:1 for graphical elements). Do not encode meaning through color alone.

**Warning signs:**
- Cannot navigate from US map to individual story using only the keyboard.
- Screen reader reads SVG elements as "group" or "image" with no meaningful label.
- Narrative text scores above grade 10 on readability tests.
- No text alternative exists for any visualization layer.

**Phase to address:**
Must be addressed in two phases: (1) Architecture/design phase sets accessibility standards and component patterns. (2) Each implementation phase verifies accessibility for the layer being built. Cannot be bolted on at the end.

---

## Moderate Pitfalls

### Pitfall 7: The Bidirectional Claim Without Bidirectional Evidence

**What goes wrong:**
The project's core thesis is a bidirectional feedback loop: civic exclusion causes health gaps AND health gaps suppress civic participation. Most available data only supports one direction well (social determinants -> health outcomes). The reverse direction (poor health -> reduced civic participation) has weaker empirical support and is harder to visualize. If the site claims bidirectionality but only shows evidence for one direction, it undermines its own thesis.

**How to avoid:**
- Source evidence specifically for the health -> civic participation direction. Look for studies on: illness as a barrier to voting, disability and voter suppression, hospital closures reducing community civic infrastructure, chronic disease and political disengagement.
- If the reverse-direction evidence is thin, be honest about it. "Research strongly shows that civic exclusion harms health. Emerging evidence suggests the reverse is also true" is more defensible than presenting both directions as equally proven.
- Design the visualization to visually distinguish the strength of evidence in each direction (e.g., solid arrows vs. dashed arrows).

**Warning signs:**
- The "feedback loop" is described in text but only one direction is supported by data in the visualizations.
- Both directions are presented with equal visual weight despite unequal evidence.

**Phase to address:**
Data research phase, specifically when building the health center layer with bidirectional data.

---

### Pitfall 8: Composite Portraits That Are Legally Composite But Emotionally Specific

**What goes wrong:**
The project uses "composite portraits built from documented statistics" to avoid consent issues. But if a composite is too specific -- a 72-year-old Black woman in Bed-Stuy who had a stroke in 2019 and could not reach her polling place in 2020 -- it may be identifiable even without a real name. This creates both ethical risk (a real person might recognize their story without having consented) and credibility risk (audiences may assume the story is about a specific person and try to verify it).

**How to avoid:**
- Composites should blend at least 3-4 documented cases or data points, not closely track any single individual's story.
- Avoid hyper-specific details (exact age, exact year of medical event, exact address) that could narrow identification. Use ranges: "in her 70s," "in central Brooklyn," "in the years before the pandemic."
- Include a clear, prominent disclosure: "This story is a composite based on documented health data and policy impacts. It does not represent a specific individual."
- Vet composites with someone from the represented community if possible.

**Warning signs:**
- A composite story that reads like journalism about a specific person.
- Details specific enough that someone could search for and potentially identify a matching individual.
- No composite disclosure, or disclosure buried in a footer.

**Phase to address:**
Story creation phase. Composite guidelines must be established before writing begins.

---

### Pitfall 9: Color-Coding Race and Suffering

**What goes wrong:**
The Urban Institute's Do No Harm Guide specifically warns about color associations in data visualization. Using red/dark colors for communities of color and green/light colors for white communities in health outcome maps encodes a visual hierarchy that associates darkness with badness. Similarly, using colors stereotypically associated with racial groups (brown for Latino communities, etc.) reinforces racialization.

**How to avoid:**
- Use a single sequential color scale (light to dark of one hue) for quantitative data rather than diverging scales that assign "good" and "bad" colors.
- Never map racial/ethnic categories to colors that have cultural associations with those groups.
- Test color palettes with the question: "If I removed all labels, would the colors alone suggest which group is 'better' or 'worse'?"
- Use colorblind-safe palettes (avoid red-green combinations). Tools: ColorBrewer, Viz Palette.

**Warning signs:**
- Red/green or dark/light diverging scales on maps where darker areas correspond to communities of color.
- Different color hues assigned to different racial/ethnic groups in a way that creates implicit hierarchy.
- Feedback from reviewers that the color scheme "looks like a crime map" or "heat map of danger."

**Phase to address:**
Design/visualization phase, when establishing the color system for all four layers.

---

## Minor Pitfalls

### Pitfall 10: Stale Data Presented as Current Reality

**What goes wrong:**
Health and civic data have significant lag. The most recent ACS data may be 1-2 years old. NYC DOH data may be 2-3 years old. If the site presents 2022 data without dates in 2026, users assume it reflects current conditions. Policy changes (Medicaid expansion, new immigration enforcement) may have changed the reality since the data was collected.

**How to avoid:**
- Show data vintage dates on every metric. "Life expectancy: 78.2 years (NYC DOH, 2022)" not just "78.2 years."
- Note major policy changes that occurred after the data vintage date.
- Prefer the most recent available data, but never fabricate or extrapolate numbers to make data appear more current.

**Warning signs:**
- No dates visible on any data display.
- Data from different years shown side-by-side without noting the mismatch.

**Phase to address:**
Data integration phase.

---

### Pitfall 11: The Fourth Archetype Problem

**What goes wrong:**
The project has three defined story archetypes and a fourth TBD. The pressure to find a "diverse" fourth story can lead to tokenistic representation -- picking a demographic to "fill the gap" rather than choosing a story that illustrates a genuine, distinct mechanism of civic exclusion leading to health harm.

**How to avoid:**
- Choose the fourth archetype based on *mechanism*, not demographics. What civic-exclusion-to-health mechanism is not already covered? The three existing stories cover: total policy exclusion (undocumented), time-limited policy exclusion (5-year bar), and structural/geographic exclusion (redlining). A fourth could cover: incarceration/felony disenfranchisement and health, disability as both health outcome and civic participation barrier, or language access barriers in healthcare and voting.
- Do not add a story just to represent a demographic group. A story about an Asian American person that uses the same mechanism as an existing story adds demographic diversity but not analytical depth.

**Warning signs:**
- Discussion of the fourth archetype focuses on "we need to represent [demographic]" rather than "we need to illustrate [mechanism]."
- The fourth story's civic-exclusion mechanism overlaps with an existing story.

**Phase to address:**
Story research phase, early -- before the other three stories are finalized, so all four can be evaluated as a set.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hardcoding source citations in JSX | Faster initial build | Impossible to update data without touching component code | During prototype only; must move to a data layer before real data integration |
| Skipping the source registry | Ship visualization faster | Cannot defend claims when challenged; nightmare to update stale data | Never -- source registry is a project requirement, not a nice-to-have |
| Building stories without community review | Avoids coordination overhead | Risk of harmful framing that damages credibility and harms communities | Only for initial drafts; must seek review before publication |
| Using neighborhood-level data as proxy for individual demographics | Avoids complex data joins | Ecological fallacy; may misrepresent the actual population in gentrified areas | Acceptable if disclosed with clear caveats in the visualization |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Four-layer zoom with no breadcrumb or back navigation | Users get lost, cannot return to context | Persistent breadcrumb showing current zoom level with clickable ancestors |
| Hover-only info panels | Mobile users and keyboard users cannot access data | Click/focus to open, with close button; hover as enhancement only |
| Story timelines with only health/policy events | Feels like a litany of suffering; emotional exhaustion | Include community moments, family milestones, agency and resilience events |
| Jargon-heavy labels ("civic engagement index," "SVI score") | General public audience cannot interpret the data | Plain-language labels with expandable definitions |
| No way to see the data behind the visualization | Undermines trust for skeptical users | "View data sources" or "How we calculated this" expandable for each metric |

## "Looks Done But Isn't" Checklist

- [ ] **Individual stories:** Often missing composite disclosure -- verify a prominent "This is a composite" statement exists on each story
- [ ] **State-level data:** Often missing data vintage -- verify every metric shows its source year
- [ ] **NYC district map:** Often missing gentrification/displacement context -- verify the redlining story addresses neighborhood demographic change
- [ ] **Bidirectional thesis:** Often missing reverse-direction evidence -- verify the health-center layer shows data for health -> civic participation, not just civic -> health
- [ ] **Accessibility:** Often missing keyboard navigation -- verify the full US -> NYC -> health center -> person flow works with Tab/Enter only
- [ ] **Immigration stories:** Often conflating legal statuses -- verify the undocumented and LPR stories use precise, distinct policy language
- [ ] **Source registry:** Often nonexistent -- verify a structured mapping of every displayed data point to its primary source exists
- [ ] **Color system:** Often inaccessible -- verify all color encodings pass WCAG AA and do not rely on color alone for meaning

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Ecological fallacy in framing | MEDIUM | Revise all captions and tooltips to use associative language; add methodology note; restructure story introductions to cite specific mechanisms not aggregate statistics |
| Deficit framing in stories | HIGH | Requires rewriting story content, not just relabeling; must research agency/resilience elements for each archetype; may require new data sources |
| Conflated immigration statuses | LOW | Search-and-replace pass on all text; verify each story references correct policies; update data labels |
| Redlining oversimplification | MEDIUM | Add gentrification context to story narrative; source neighborhood demographic change data; may require additional visualization element |
| Indefensible data claims | HIGH | Audit every data point; build source registry retroactively; may require removing data points that cannot be sourced to primary sources |
| Inaccessible SVG visualizations | HIGH | Retrofitting ARIA labels and keyboard navigation into existing SVG components is significantly more work than building accessibly from the start |

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Ecological fallacy | Data integration + story creation | Review all captions/tooltips for causal language; methodology note exists |
| Deficit framing | Story research + content creation | Each story timeline contains at least 2 agency/resilience events |
| Immigration status conflation | Data research | Text search for unqualified "immigrant" in all display text returns zero results |
| Redlining oversimplification | Story research + NYC data sourcing | Redlining story mentions gentrification/displacement; uses race-stratified neighborhood data |
| Indefensible claims | Data research (before implementation) | Source registry exists with primary source for every displayed metric |
| Accessibility gaps | Architecture/design + each implementation phase | Keyboard-only navigation test passes at each zoom level; screen reader audit |
| Bidirectional evidence gap | Data research for health center layer | At least 2 data points supporting health -> civic participation direction are displayed |
| Composite identification risk | Story creation | Each composite blends 3+ documented cases; no hyper-specific identifying details |
| Color-coding problems | Design/visualization phase | Color palette review against Do No Harm guide checklist; colorblind simulation test |
| Stale data | Data integration | Every displayed metric includes source and year |
| Fourth archetype tokenism | Story research (early) | Fourth story illustrates a mechanism not covered by other three stories |

## Sources

- [Do No Harm Guide: Applying Equity Awareness in Data Visualization -- Urban Institute](https://www.urban.org/research/publication/do-no-harm-guide-applying-equity-awareness-data-visualization) -- PRIMARY reference for equity-aware visualization
- [Principles for Using Public Health Data to Drive Equity -- CDC Foundation](https://www.cdcfoundation.org/data-equity-principles) -- Data equity principles
- [5 Key Facts About Immigrants and Medicaid -- KFF](https://www.kff.org/racial-equity-and-health-policy/5-key-facts-about-immigrants-and-medicaid/) -- Immigration/Medicaid data accuracy
- [The Truth About Medicaid Coverage for Immigrants -- Georgetown CCF](https://ccf.georgetown.edu/2025/05/21/the-truth-about-medicaid-coverage-for-immigrants-and-the-looming-threats/) -- Immigrant healthcare misconceptions
- [Fact Checking Homeland Security Claims -- Georgetown CCF](https://ccf.georgetown.edu/2026/02/26/fact-checking-homeland-security-claims-about-immigrants-and-medicaid-coverage-and-why-u-s-citizen-children-will-suffer-the-harshest-consequences/) -- Current political context for immigrant health data
- [Ecological Fallacy -- data.europa.eu](https://data.europa.eu/apps/data-visualisation-guide/ecological-fallacy) -- Ecological fallacy in data visualization
- [From Redlining to Gentrification -- Harvard Primary Care](https://info.primarycare.hms.harvard.edu/perspectives/articles/redlining-gentrification-health-outcomes) -- Redlining, gentrification, and health complexity
- [Redlining data story -- NYC Environment and Health Data Portal](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-stories/redlining/) -- NYC-specific redlining and health data
- [Immigrant Health Inequities: Exposing Diversions and White Supremacy -- MDPI](https://www.mdpi.com/2076-0760/10/9/341) -- Deficit framing in immigrant health research
- [Telling Truth with Data Visuals -- Springer](https://link.springer.com/article/10.1057/s41271-024-00479-0) -- 4E principles for public health visualization
- [Transforming Civic Data Systems -- Stanford Social Innovation Review](https://ssir.org/articles/entry/data-justice-equity-sovereignty) -- Data sovereignty and community self-determination
- [DATA VISUALIZATION AND HEALTH EQUITY BEST PRACTICES CHECKLIST](https://partnersforfamilyhealth.org/wp-content/uploads/2018/11/HEALTH_EQUITY_DATA_VISUALIZATION_CHECKLIST.pdf) -- Practical checklist for health equity visualization

---
*Pitfalls research for: Civic Health Alliance -- civic health data visualization with stories of marginalized communities*
*Researched: 2026-03-10*
