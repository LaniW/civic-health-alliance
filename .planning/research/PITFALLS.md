# Pitfalls Research

**Domain:** Civic health data visualization with individual stories of marginalized communities
**Researched:** 2026-03-10, updated 2026-03-24
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

## Implementation Pitfalls (Milestone-Specific)

The following pitfalls are specific to the four implementation tasks in the current milestone: SVG layout fixes, transcript-to-story replacement, public health stat verification, and touch navigation. They are technical rather than editorial.

---

### Pitfall 12: SVG Coordinate Arithmetic That Breaks When Department Size Changes

**What goes wrong:**
The hospital scene (Level 2) places text labels, wait-time numbers, and "note" strings at hardcoded offsets from department rectangle origins (`d.y+4.5`, `d.y+8`, `d.y+10.5`, `d.y+d.h-2.5`). Person figures are placed at absolute coordinates (`x:60,y:69`). When department rectangles or person positions are adjusted to fix centering or overlap, the text lines and figure coordinates do not automatically follow -- they must be manually recalculated. One off-by-one in a y-offset cascades into new overlaps.

**Why it happens:**
The SVG uses a small viewBox (`0 0 100 96`) where every coordinate is in SVG user units, not pixels. Developers reason about layout in pixel terms ("move this down by 4px") but the viewBox scale means a 1-unit move is not 1 pixel. The arithmetic is invisible -- no layout engine enforces constraints.

**How to avoid:**
- Before touching any coordinate, record the full set of existing coordinates for the affected department/figure as a comment or scratch document. Treat layout adjustment as a batch operation, not individual tweaks.
- Calculate person centering from department bounds, not by trial-and-error pixel nudging: `personX = d.x + d.w/2`, `personY = d.y + d.h * 0.65` (two-thirds down the room). This makes the logic reproducible.
- Text stacking order within a department: establish a fixed vertical rhythm (label at `+4.5`, number at `+8`, subtext at `+11`) and verify it fits within the room height (`d.h`) before committing. If `d.h` is 28 units and text needs 13 units of vertical space, there are 15 units of margin -- document this.
- After any coordinate change, visually verify at the actual rendered viewport size, not just in code. The `viewBox="0 0 100 96"` makes things look fine in code but stretched or compressed at the real aspect ratio.

**Warning signs:**
- Any commit that changes `d.x`, `d.y`, `d.w`, or `d.h` without also updating associated text y-offsets.
- Person figure y-coordinates that are absolute values rather than computed from department bounds.
- "Looks fine on my screen" without testing at 1280x800, 1440x900, and 768x1024 viewport sizes.

**Phase to address:**
Hospital scene layout fix. Establish coordinate conventions before making any changes.

---

### Pitfall 13: SVG Text That Does Not Wrap and Silently Overflows

**What goes wrong:**
SVG `<text>` elements do not wrap. A department label like "Primary Care" fits. A longer label from a transcript-based story event does not -- it renders past the right edge of its container rect with no visible error. In the small-viewBox coordinate system, even a label that is 2 characters too long can overflow a 30-unit-wide room without any clipping because SVG overflow defaults to visible.

The current "note" text fields (e.g., `"1 PCP per 850 patients"`) are close to their container widths. Story-derived content may be longer.

**Why it happens:**
Developers copy-paste text into SVG text elements and check the output on one screen size. The overflow is invisible unless you specifically look for it. React does not warn about SVG text overflow.

**How to avoid:**
- Enforce a character budget for every SVG text field before integrating new content. Count the characters in the widest department (32 units wide). At `fontSize: 2.4` (SVG units), roughly 16-18 characters fit per line safely.
- For any text derived from transcript content, truncate to the budget and test at the minimum viewport width.
- If longer labels are unavoidable, use two `<text>` elements with line-break offsets rather than one long line.
- Add a `clipPath` on rooms that contain variable text if the content source is uncertain.

**Warning signs:**
- Any new text string longer than the longest existing string in the same position.
- Text content sourced directly from a transcript without a character-count check.
- Department "note" fields longer than 22 characters.

**Phase to address:**
Hospital scene layout fix and transcript integration, simultaneously.

---

### Pitfall 14: Transcript Content That Implies Identifiability Despite Being Real

**What goes wrong:**
The real patient stories come from Stella Saffo's clinical interview transcript describing three patient experiences: Medicaid/insurance gaps, HIV care continuity, and NY state benefits. Even when displayed as a clinical case summary (not a named individual), specific combinations of details -- HIV-positive, navigating NY state benefits, specific year of care disruption -- can narrow identification to a small population within the health center. A person who knows the clinic's patient population might recognize themselves or someone they know.

This is different from Pitfall 8 (composite identification risk). Here the content is *real*, not composite. The risk is not legal but ethical: a real person described in a public kiosk/presentation without having consented to that specific public display context.

**Why it happens:**
The transcript owner (the clinician) may have consent to use clinical material for educational purposes, but "educational use in a presentation" and "public-facing interactive kiosk" are different consent contexts. Teams assume clinical interview consent covers public display.

**How to avoid:**
- Confirm explicitly with Stella Saffo what consent context the transcript was collected under. "Educational use" does not automatically include public interactive display.
- Anonymize more aggressively than feels necessary: remove specific years, change the type of benefit navigated if a more generic equivalent serves the narrative, composite-blend the HIV care continuity story with at least one other documented case to reduce singularity.
- Include a disclosure statement on each story: "This narrative is drawn from clinical documentation and has been anonymized to protect patient confidentiality."
- If consent scope is unclear, treat the transcript as a *research source* for documented patterns rather than as *content* to be directly displayed.

**Warning signs:**
- Story content directly quoting or paraphrasing the transcript without editorial transformation.
- Details that are specific enough to identify a single individual within a small patient population (e.g., the only HIV-positive patient at this clinic who navigated NY state benefits in a specific year).
- No disclosure statement on the story panel.

**Phase to address:**
Transcript integration phase, before writing any display content.

---

### Pitfall 15: Stat Verification That Stops at the Number Without Checking the Definition

**What goes wrong:**
The current `STATE_DATA` contains an uninsured rate for each state (e.g., Texas at 16.8%, Massachusetts at 2.8%). KFF publishes uninsured rates for multiple denominator populations: total population, non-elderly adults (19-64), children under 19, and adults 18-64. CDC NVSR 2022 state life expectancy tables use a specific methodology (period life tables from final mortality data + July 1 Census estimates + Medicare data for ages 66-99). MAP voter turnout data uses the voting-eligible population (VEP), not the voting-age population (VAP) -- a meaningful difference in states with large non-citizen adult populations.

If the verification process confirms "the number matches the source" without also confirming "the definition matches what the visualization implies," the data is technically accurate but substantively misleading.

**Why it happens:**
Verification is done by looking up a number and comparing it to what is displayed. The methodology notes and footnotes on source pages are skipped. "Uninsured rate" feels self-explanatory.

**How to avoid:**
- For each metric, document not just the number and source, but the denominator definition: "KFF state uninsured rate = non-elderly population (under 65), ACS 2023 single-year estimates." If the current data uses a different denominator, it must be updated to match or the label must specify.
- CDC NVSR life expectancy: the 2022 state tables are in NVSR Volume 74, Number 12. Confirm that the existing state values match this specific table, not the provisional 2022 estimates (VSRR-031), which used different methodology and produced slightly different numbers.
- MAP voter turnout: confirm whether figures are VEP-based or VAP-based, and which election cycle (2020, 2022, or 2024). The roadmap requires 2024 data; the existing data appears to be from a prior cycle.
- Cross-check at least 5 states across the full range (highest, lowest, and 3 mid-range values) before declaring a dataset verified.

**Warning signs:**
- Verification notes that record only "confirmed: [number] matches KFF" without recording the specific table, year, and denominator.
- Life expectancy values that do not match CDC NVSR 74-12 exactly (suggesting provisional data was used).
- Voter turnout figures that differ from MAP's 2024 general election VEP turnout by more than 2 percentage points.

**Phase to address:**
Data verification phase, before any story content references specific statistics.

---

### Pitfall 16: Hover State Stuck on Touch -- The Silent Navigation Failure

**What goes wrong:**
The app uses `onMouseEnter`/`onMouseLeave` handlers throughout: on SVG state hexagons, NYC district paths, hospital department rects, and person figures. On touch screens, these events do not fire reliably. The first tap on a state hexagon fires `onMouseEnter` (triggering a hover tooltip). There is no subsequent `onMouseLeave` because the user lifts their finger and the touch event sequence ends without a mouse-leave event. The hover state stays set. The tooltip or highlight persists on the element until something else fires a state update.

This means: on the primary use case device (touch kiosk), hovering is broken. Tooltip panels show stale state. Highlighted elements do not de-highlight.

**Why it happens:**
Mouse event handlers are the standard React pattern for hover interactions. Touch event handlers are added as a separate concern, often never added. The app was likely developed and tested on a desktop browser.

**How to avoid:**
- Add `onTouchStart` handlers that replicate the `onMouseEnter` behavior, and `onTouchEnd` + `onTouchCancel` handlers that replicate `onMouseLeave`. This is the minimum fix.
- For interactive elements (district clicks, person clicks), ensure the `onClick` handler fires on touch via the native touch-to-click conversion that browsers do automatically -- do not replace this with `onTouchStart` as that fires before `onClick`.
- After adding touch handlers, explicitly clear hover state (`setHovState(null)`, `setHovHood(null)`, etc.) on `onTouchEnd` to prevent sticky tooltips.
- The back-arrow and breadcrumb navigation buttons (the primary new feature) must have touch targets of at least 44x44 CSS pixels. The existing NavBar buttons are `height:45px` and `width:208px` -- these are fine. Back arrow buttons in the InfoPanel are smaller (`padding:"10px 20px"`) -- verify rendered pixel size.

**Warning signs:**
- Testing touch navigation on an actual touch device, not just Chrome DevTools device emulation (DevTools emulation fires mouse events differently than real touch hardware).
- Hover tooltips visible after the finger is lifted.
- A back-arrow button that requires two taps to activate (first tap fires hover, second tap fires click).

**Phase to address:**
Touch navigation phase. Must test on a real touch device, not a simulator.

---

### Pitfall 17: Breadcrumb Navigation That Allows Impossible State Transitions

**What goes wrong:**
The roadmap requires tappable breadcrumbs to navigate to any previous level. The existing `zoomTo()` function takes a `nextLevel` integer and handles the animation. But breadcrumbs for levels 2 and 3 (Health Center, Story) present a subtlety: Level 2 (hospital scene) requires knowing which NYC district was selected; Level 3 requires knowing which person was selected. If a user taps the "New York City Map" breadcrumb from Level 3, they should land on Level 1 with whatever NYC district context was active -- but `selectedPerson` and `hovHood` are cleared in `zoomTo()`.

The deeper problem: if a user navigates directly from Level 3 to Level 0 (US Map breadcrumb), they skip the intermediate levels. The `zoomTo(0)` call will work, but on return, the story context is lost. A back tap from Level 0 goes to Level -1 (Title), not back to the story.

This is not a blocking problem for the current milestone (which only adds a back arrow and tappable breadcrumbs), but designing the breadcrumb interaction without thinking through the state transitions will produce a confusing navigation experience.

**Why it happens:**
Navigation logic is designed for linear forward progression (the "Next" button pattern). Non-linear navigation (jump from level 3 to level 1, then press next to get to level 2 again) is not considered.

**How to avoid:**
- Breadcrumbs should navigate backward only, never forward. A user at Level 3 can tap breadcrumbs to reach Level 2, 1, or 0. They cannot use breadcrumbs to reach Level 3 from Level 0.
- Implement breadcrumb navigation as `zoomTo(targetLevel)` where `targetLevel` is always less than or equal to `level`. Do not allow upward jumps via breadcrumbs.
- The back arrow should always navigate exactly one level back: `zoomTo(level - 1)`. This is simpler and safer than multi-level jumps.
- For the current milestone, this is sufficient. Do not implement "jump to any level" breadcrumbs without first resolving what happens to `selectedPerson` state when you skip levels.

**Warning signs:**
- A breadcrumb implementation that calls `zoomTo` with a level *higher* than current level.
- Using breadcrumbs to navigate into Level 2 or 3 without setting `selectedPerson`.
- Pressing "next" after a breadcrumb jump producing a blank screen because required state was cleared.

**Phase to address:**
Touch navigation phase. Keep the implementation simple: back one level only.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hardcoding source citations in JSX | Faster initial build | Impossible to update data without touching component code | During prototype only; must move to a data layer before real data integration |
| Skipping the source registry | Ship visualization faster | Cannot defend claims when challenged; nightmare to update stale data | Never -- source registry is a project requirement, not a nice-to-have |
| Building stories without community review | Avoids coordination overhead | Risk of harmful framing that damages credibility and harms communities | Only for initial drafts; must seek review before publication |
| Using neighborhood-level data as proxy for individual demographics | Avoids complex data joins | Ecological fallacy; may misrepresent the actual population in gentrified areas | Acceptable if disclosed with clear caveats in the visualization |
| Hardcoding SVG person positions as absolute coordinates | Quick to adjust individually | Positions do not follow room layout changes; every room move requires re-centering | Only for initial placement; must compute from room bounds before layout is stable |
| Skipping real touch device testing | Saves setup time | Touch behavior in DevTools emulation differs from real hardware; sticky hover and event sequence bugs invisible in emulation | Never for touch-primary use cases |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| CDC NVSR life expectancy data | Using provisional VSRR estimates instead of final state life tables | Use NVSR Volume 74, Number 12 (US State Life Tables, 2022); provisional estimates have different methodology and numbers |
| KFF uninsured rate | Mixing total-population and non-elderly-population rates across states | Pick one denominator (non-elderly, ages 0-64 is KFF standard) and use it for all states; document the denominator in the visualization |
| MAP voter turnout | Using VAP-based rates when VEP-based is standard | MIT Election Lab and MAP both publish VEP turnout; confirm which table the existing data came from before verifying against a new source |
| Stella Saffo transcript | Treating educational-consent content as public-display-consent content | Confirm consent scope covers the specific display context (public kiosk/interactive presentation) before using any transcript content verbatim |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Inline style objects recreated on every render | Subtle jank on hover in SVG maps with 59+ districts | Hoist static style objects outside component functions | Noticeable at 60fps animation threshold; not a concern for static styles |
| SMIL animations on many SVG elements | Battery drain and heat on mobile/kiosk hardware | Limit `<animate>` elements to 2-3 visible simultaneously; pause off-screen animations | Kiosk hardware running continuously for 8+ hours |
| `<style>` tag with Google Fonts import inside component | Font re-request on every render | Move `<style>` tag to `index.html` or a top-level effect that runs once | Not a performance issue at small scale, but wastes network round-trips |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Four-layer zoom with no breadcrumb or back navigation | Users get lost, cannot return to context | Persistent breadcrumb showing current zoom level with clickable ancestors |
| Hover-only info panels | Mobile users and keyboard users cannot access data | Click/focus to open, with close button; hover as enhancement only |
| Story timelines with only health/policy events | Feels like a litany of suffering; emotional exhaustion | Include community moments, family milestones, agency and resilience events |
| Jargon-heavy labels ("civic engagement index," "SVI score") | General public audience cannot interpret the data | Plain-language labels with expandable definitions |
| No way to see the data behind the visualization | Undermines trust for skeptical users | "View data sources" or "How we calculated this" expandable for each metric |
| Back arrow only (no breadcrumbs) on a multi-level drill-down | User at Level 3 must tap back 4 times to reach title screen | Both back arrow (one level) and breadcrumbs (jump to US Map or NYC Map) |
| Touch targets under 44px on kiosk hardware | Frequent missed taps, user frustration | Minimum 44x44 CSS pixels for all interactive elements; 48x48 preferred for primary navigation |

## "Looks Done But Isn't" Checklist

- [ ] **Individual stories:** Often missing composite disclosure -- verify a prominent "This is a composite" statement exists on each story
- [ ] **State-level data:** Often missing data vintage -- verify every metric shows its source year
- [ ] **NYC district map:** Often missing gentrification/displacement context -- verify the redlining story addresses neighborhood demographic change
- [ ] **Bidirectional thesis:** Often missing reverse-direction evidence -- verify the health-center layer shows data for health -> civic participation, not just civic -> health
- [ ] **Accessibility:** Often missing keyboard navigation -- verify the full US -> NYC -> health center -> person flow works with Tab/Enter only
- [ ] **Immigration stories:** Often conflating legal statuses -- verify the undocumented and LPR stories use precise, distinct policy language
- [ ] **Source registry:** Often nonexistent -- verify a structured mapping of every displayed data point to its primary source exists
- [ ] **Color system:** Often inaccessible -- verify all color encodings pass WCAG AA and do not rely on color alone for meaning
- [ ] **SVG text overflow:** Often invisible until tested at multiple viewport sizes -- verify no text label exceeds room bounds at 1280x800 minimum
- [ ] **Touch hover state:** Often sticky after first tap -- verify tap on any interactive element, then lift finger, and check that tooltip/highlight disappears
- [ ] **Back navigation:** Often untested on real touch hardware -- verify back arrow and breadcrumbs work on the actual kiosk device, not just desktop Chrome
- [ ] **Stat definitions:** Often verified by number only -- verify that the denominator for each metric matches what the label implies (non-elderly uninsured vs. total-population uninsured)
- [ ] **Transcript consent scope:** Often assumed -- verify Stella Saffo's consent covers public interactive display, not just educational presentation

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Ecological fallacy in framing | MEDIUM | Revise all captions and tooltips to use associative language; add methodology note; restructure story introductions to cite specific mechanisms not aggregate statistics |
| Deficit framing in stories | HIGH | Requires rewriting story content, not just relabeling; must research agency/resilience elements for each archetype; may require new data sources |
| Conflated immigration statuses | LOW | Search-and-replace pass on all text; verify each story references correct policies; update data labels |
| Redlining oversimplification | MEDIUM | Add gentrification context to story narrative; source neighborhood demographic change data; may require additional visualization element |
| Indefensible data claims | HIGH | Audit every data point; build source registry retroactively; may require removing data points that cannot be sourced to primary sources |
| Inaccessible SVG visualizations | HIGH | Retrofitting ARIA labels and keyboard navigation into existing SVG components is significantly more work than building accessibly from the start |
| SVG coordinate cascades | LOW | Recalculate affected coordinates as a batch; do not patch one element at a time |
| Sticky hover on touch | LOW | Add onTouchEnd handlers that clear hover state; 1-2 hours of work per interactive SVG layer |
| Breadcrumb state transition bugs | MEDIUM | Constrain breadcrumbs to backward-only navigation; test all possible jump paths |
| Stat definition mismatch | MEDIUM | Re-pull all affected metrics with correct denominators; update source documentation |
| Transcript consent scope unclear | HIGH | May require removing real-transcript content and rebuilding stories from published statistical composites; consent scope must be confirmed before any display content is written |

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
| SVG coordinate cascades | Hospital scene layout fix | Person positions computed from department bounds, not hardcoded; text fits within room height |
| SVG text overflow | Hospital scene layout fix + transcript integration | No text label exceeds container bounds at 1280x800 viewport; character budgets documented |
| Transcript identifiability risk | Before transcript integration begins | Consent scope confirmed; story content editorially transformed, not directly quoted; disclosure statement present |
| Stat definition mismatch | Data verification phase | Each metric's denominator definition documented alongside the value; 5-state cross-check completed |
| Sticky hover on touch | Touch navigation phase | Tap-lift test on real touch hardware passes for all interactive elements |
| Breadcrumb impossible state | Touch navigation phase | Back arrow navigates exactly one level; breadcrumbs never navigate to a level higher than current |

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
- [US State Life Tables, 2022 -- CDC NVSR Volume 74, Number 12](https://www.cdc.gov/nchs/data/nvsr/nvsr74/nvsr74-12.pdf) -- Authoritative source for state-level life expectancy
- [Key Facts about the Uninsured Population -- KFF](https://www.kff.org/uninsured/key-facts-about-the-uninsured-population/) -- KFF methodology for uninsured rate denominators
- [Protecting Patient Privacy in Narratives: Lifespan-Brown Checklist -- PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6519768/) -- Clinical narrative consent and de-identification standards
- [React onMouseLeave unreliability -- GitHub Issue #4492](https://github.com/facebook/react/issues/4492) -- Known React issue with hover events in Chrome
- [Touch Device interaction -- React Flow docs](https://reactflow.dev/examples/interaction/touch-device) -- Touch event handling patterns in SVG/canvas React contexts
- [6 Common SVG Fails -- CSS-Tricks](https://css-tricks.com/6-common-svg-fails-and-how-to-fix-them/) -- SVG layout and overflow pitfalls
- [Touch Target Size -- Deque Docs](https://docs.deque.com/devtools-mobile/2025.7.2/en/ios-touch-target-size/) -- Minimum touch target size standards

---
*Pitfalls research for: Civic Health Alliance -- civic health data visualization with stories of marginalized communities*
*Researched: 2026-03-10, implementation pitfalls added 2026-03-24*
