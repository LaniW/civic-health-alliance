# Feature Research

**Domain:** Interactive civic/public-health data visualization with narrative storytelling — touch-first kiosk deployment
**Researched:** 2026-03-24
**Confidence:** HIGH (touch navigation UX) / HIGH (storytelling credibility) / HIGH (anti-patterns)

---

## Feature Landscape

### Table Stakes (Users Expect These)

Features users assume exist. Missing these = product feels incomplete or broken.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Back navigation control | Touch/kiosk users have no browser back button; dead-end states cause abandonment | LOW | Back arrow at top-left is standard convention. Must be visible at every non-root level. KIOSK.com and IxDF both list this as non-negotiable. |
| Breadcrumb location indicator | Multi-level drill-downs (4 levels here) require visible orientation cues; users lose track without them | LOW-MEDIUM | Show current path (e.g. "US > NYC > Williamsburg"). Tappable breadcrumbs let users jump up multiple levels in one tap rather than pressing back repeatedly. |
| Touch targets >= 9-12mm | Kiosk/tablet users trigger mis-taps on small targets; frustration causes abandonment | LOW | KIOSK.com specifies 20mm buttons with clear spacing; IxDF specifies 9-12mm minimum links. This is roughly 36-48px at 96dpi. Applies to nav arrows, breadcrumbs, person figures, district areas. |
| Visible data source citations | Data journalism norm: credibility requires "Source: CDC" style attribution; users distrust unsourced numbers | LOW | Already partially present via left panel. Numbers must match cited sources exactly — no rounding discrepancies or stale values. |
| Smooth level transitions | Drill-down maps are disorienting without visual continuity between zoom levels | MEDIUM | Already implemented (400ms fade-out + state update + 450ms fade-in). Pattern is correct; maintaining it is required. |
| Hover / tap detail on map regions | Users expect to inspect individual states/districts before committing to a drill-down | LOW | Already present for hover. Touch tap equivalents need clear behavior (tap = reveal detail tooltip OR proceed to next level — pick one consistently). |
| Consistent orientation cues | Users need to know what level they are on at all times, especially after returning from a deep level | LOW | NavBar level indicator + breadcrumbs together satisfy this. Neither alone is sufficient on a kiosk. |
| Authentic story content | Civic/health data pieces that cite real sources but use fictional characters undermine trust and purpose | LOW (content) | The fictional Maria/Dr. Okafor/James/Sofia stories create a credibility mismatch next to real CDC/KFF stats. Real patient transcripts are table stakes for a credible civic storytelling piece. |
| Legible text at all levels | Overlapping or clipped labels signal a broken product regardless of underlying data quality | MEDIUM | Hospital scene text overlap is a current failure. Centering figures in rooms and clearing label collisions is required before any public showing. |

### Differentiators (Competitive Advantage)

Features that set the product apart. Not required to function, but deliver the core value proposition.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| 4-level civic-to-individual narrative arc | Most public health visualizations stop at aggregate maps; drilling to an individual patient story creates emotional resonance and makes the systemic argument personal | HIGH (already built) | The drill-down from US to NYC to hospital to patient is rare in civic data viz. It is the product's defining feature. Protect it. |
| Real patient transcript integration | Real voices from Stella Saffo's clinical interviews make the data viscerally credible; fictional composites feel manipulative next to hard statistics | MEDIUM (content work) | Medicaid gaps, HIV care continuity, NY state benefits — specific, verifiable patient situations. Attribution to a named clinician adds a layer of trustworthiness beyond most data journalism. |
| Hexagonal state map + dual-variable color scale | Hex maps give equal visual weight to small states (RI, DE) that standard choropleths distort; combining life expectancy with voter turnout in a single color signal is unusual and clarifying | MEDIUM (already built) | Supports the civic-health thesis visually without requiring a legend explanation. |
| Linked civic and health data at every level | Most visualizations track one dimension; showing voter turnout and life expectancy in parallel at US level, NYC district level, and individual level reinforces the correlation through repetition | HIGH (already built) | The leColor/engagementColor dual-coding is differentiating. Maintaining it through the narrative arc is the product's argument. |
| Animated person figures in hospital scene | Adds humanity to what could be a cold infographic; person-level representation in a spatial (hospital) context is uncommon in public health data viz | HIGH (already built) | Keep. Do not simplify to icons or bullet lists. Fixing the layout problems makes this feature land; breaking layout makes it hurt credibility. |

### Anti-Features (Commonly Requested, Often Problematic)

Features that seem like improvements but create more problems than they solve.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| New zoom levels or data dimensions | "More data = more valuable" intuition; stakeholders often push for county level, income data, etc. | Each new level multiplies content maintenance burden and breaks the narrative arc that ends at the individual by design. Scope creep is catastrophic for a single-file monolith. | Hold the line at 4 levels. Additional data dimensions can appear in the left panel without adding a zoom level. Explicitly out of scope per PROJECT.md. |
| Real-time data or API integration | "Live data is more accurate" | Real-time data breaks reproducibility, requires backend infrastructure, introduces stale/error states, and invalidates the source-cited static numbers that are the product's credibility foundation. | Use dated static snapshots with explicit "as of [year]" labels. Static data with proper citation is more trustworthy than live data with uncertainty. |
| User filtering and custom queries | Interactive dashboards let users "explore their own story" | This is a narrative product, not an analytics tool. Free exploration control breaks the authorial arc from national to individual. It also requires significant UI complexity (filters, resets, edge cases) that a single-file monolith cannot cleanly support. | The existing hover-to-inspect pattern is the correct scope of interactivity. Drill-down IS the filtering mechanism. |
| Sharing and social export | Users want to share interesting data points | Adds clipboard/screenshot complexity, raises attribution and data accuracy questions when data is taken out of context. Kiosk deployment makes sharing irrelevant anyway. | If web deployment is added later, a static URL per level (hash routing) is sufficient and less risky. |
| Full mobile responsive layout | Touch support is required, but full mobile breakpoints are not | This is a kiosk/presentation app. Optimizing for 375px phone screens would require a fundamentally different layout. Attempting it in a 730-line single-file component without dedicated CSS is high risk of regressions. | Touch targets and navigation controls are the mobile-necessary subset. Full layout optimization for phone viewports is out of scope per PROJECT.md. |
| Accessibility (WCAG AA) | Important for public-facing civic content | SVG maps with ARIA labels, keyboard navigation, and screen-reader descriptions for 59 districts and 50 states is a multi-sprint effort. Attempting it piecemeal introduces inconsistent partial compliance which is worse than none. | Flag as a future milestone with dedicated scope. Do not attempt in this milestone. |
| User-configurable color themes | "Some users prefer dark mode" | The color coding (red-to-green life expectancy scale, engagement gradient) is load-bearing for the data argument. Allowing theme changes breaks the semantic color mapping that carries the whole visualization's meaning. | Fixed light theme is correct. The cream/red/green palette is intentional design, not a cosmetic choice. |

---

## Feature Dependencies

```
Back navigation control
    └──requires──> Level state tracking (exists: `level` useState in App.jsx)
    └──requires──> Visible NavBar at all non-root levels (exists)

Breadcrumb location indicator
    └──requires──> Level state tracking (exists)
    └──requires──> Named level labels as constants ("US Map", "NYC Map", "Hospital", "Story")
    └──enhances──> Back navigation control (together they satisfy kiosk orientation needs)

Real patient transcript integration
    └──requires──> Source transcript content (from Stella Saffo's clinical interviews)
    └──requires──> Stats verification (cannot have real stories next to wrong numbers)
    └──conflicts──> Fictional composite characters (must fully replace, not coexist)

Stats verification
    └──requires──> Source access (CDC NVSR, KFF, MAP, NYC DOHMH, NYC CFB/Equity)
    └──enhances──> Real patient transcript integration (credibility stack)

Hospital scene layout fix (centering figures, text overlap)
    └──requires──> Understanding of SVG coordinate system and dept room bounds in App.jsx
    └──independent of──> Navigation features (can be developed and tested separately)

Touch targets >= 9-12mm
    └──enhances──> Back navigation control (nav arrow must be large enough to tap reliably)
    └──enhances──> Breadcrumbs (each crumb must be large enough to tap)
    └──enhances──> Map region tap (district/state areas may need expanded hit zones beyond visual bounds)
```

### Dependency Notes

- **Breadcrumbs requires level naming:** The NavBar needs a string label for each level to render meaningful breadcrumbs. These do not exist as named constants yet and need to be added.
- **Real stories conflicts with fictional characters:** The four fictional people in HOSPITAL_PEOPLE must be fully replaced. Partial replacement (some real, some fictional) would be more confusing than either extreme.
- **Stats verification enables real stories:** Replacing story content while leaving incorrect stats in the left panel would be an inconsistent half-fix. Both must ship together to close the credibility gap.
- **Hospital scene fixes are independent:** Layout and text-overlap fixes can be developed and tested without touching navigation code or story content. Good candidate for parallel work or a first step before tackling content changes.

---

## MVP Definition

This is a subsequent milestone, not initial launch. The product core exists. MVP for this milestone = the minimum set of changes that make the app credible and usable on a touch screen.

### Ship in This Milestone (v1.x)

- [ ] Back arrow button in NavBar — kiosk cannot function without it; zero tolerance for dead ends
- [ ] Tappable breadcrumbs showing current path — without orientation, touch users get lost in a 4-level hierarchy
- [ ] Hospital scene: center person figures within their department rooms — current layout signals "broken"
- [ ] Hospital scene: fix text/label overlap — visual polish is credibility for a public-facing piece
- [ ] Replace fictional stories with real patient transcript content — authenticity is the core value; fictional composites undermine it
- [ ] Verify all left-panel stats against cited sources — real stories next to wrong numbers worsens the problem

### Add After Validation (v2)

- [ ] Touch target size audit across all map elements — states and districts are small; tap areas may need expanded hit zones beyond visual bounds beyond what this milestone addresses
- [ ] "Return to start" home button at deepest level — breadcrumbs cover this, but an explicit restart CTA at level 3 (Person Story) reduces cognitive load for kiosk users who want to begin again
- [ ] Transition animation polish — current fade works; directional slides would reinforce the drill-down metaphor more strongly but are a refinement not a fix

### Future Consideration (v3+)

- [ ] Hash-based URL routing per level — enables shareable deep links if deployed to web (not relevant for kiosk use case)
- [ ] WCAG accessibility pass — requires dedicated sprint; do not partially implement
- [ ] Additional patient stories — once real transcript content is integrated, adding more stories is valuable but not urgent

---

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Back arrow button | HIGH | LOW | P1 |
| Tappable breadcrumbs | HIGH | LOW-MEDIUM | P1 |
| Hospital scene centering | HIGH | MEDIUM | P1 |
| Hospital text overlap fix | HIGH | MEDIUM | P1 |
| Real patient transcript | HIGH | MEDIUM (content-heavy) | P1 |
| Stats verification | HIGH | LOW-MEDIUM (research-heavy) | P1 |
| Touch target size audit | MEDIUM | LOW | P2 |
| Home/restart button at level 3 | MEDIUM | LOW | P2 |
| Transition animation polish | LOW | MEDIUM | P3 |
| Hash routing for web deployment | LOW | MEDIUM | P3 |

**Priority key:**
- P1: Must have for this milestone
- P2: Should have, add if time allows
- P3: Nice to have, defer to future milestone

---

## Competitor Feature Analysis

Reference products in the civic/public-health data storytelling space:

| Feature | NYT Graphics / The Pudding | CDC Interactive Data | Our Approach |
|---------|---------------------------|---------------------|--------------|
| Back/breadcrumb navigation | Scroll-based; back = scroll up (no level state) | Browser native back; no custom controls | Explicit back arrow + tappable breadcrumbs — required because browser controls are unavailable on kiosk |
| Source citations | Inline footnotes, methodology pages, source links | Prominent "Data Source" labels on every chart | Left panel source listing; numbers must match cited sources exactly |
| Animation between sections | Scroll-triggered; no fade transitions | None / static | Fade transition already implemented; maintain it |
| Real vs. fictional narratives | Always real people with consent or anonymization | Aggregate only; no individual stories | Real patient transcript from clinician interview; named clinician (Stella Saffo) as attribution layer |
| Touch and kiosk optimization | Web-first; not kiosk-optimized | Web-first | Touch-first by design; back arrow and breadcrumb sizing are the key affordances |
| Depth of drill-down | 1-2 levels typical | Flat / single map | 4 levels is deeper than most; the full arc (national to individual) is the differentiating feature |

---

## Sources

- [Can breadcrumbs replace back arrows in UX design? — LogRocket](https://blog.logrocket.com/ux-design/breadcrumbs-vs-back-arrow-ux-best-practices/)
- [Designing mobile breadcrumbs — LogRocket](https://blog.logrocket.com/ux-design/designing-mobile-breadcrumbs/)
- [Mobile Breadcrumbs: 8 Best Practices in UX — IxDF](https://ixdf.org/literature/article/mobile-breadcrumbs)
- [UX Breadcrumbs in 2026: Patterns, Best Practices & Examples — Eleken](https://www.eleken.co/blog-posts/breadcrumbs-ux)
- [Kiosk UI Design: 6 Tips for a More Intuitive Experience — KIOSK](https://kiosk.com/kiosk-ui/)
- [Kiosk UX UI Interface Design Checklist — AVIXA Xchange](https://xchange.avixa.org/posts/kiosk-ux-ui-design-checklist)
- [Designing Great Kiosk UX: Reducing Friction and User Errors — SiteKiosk](https://sitekiosk.us/kiosk-design-user-experience/)
- [Storytelling with Healthcare Data: Best Practices — PatientPartner](https://www.patientpartner.com/blog/storytelling-with-healthcare-data-best-practices)
- [Seeing Your Stories: Visualization for Narrative Medicine — PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC10880175/)
- [Design Patterns for Data-Driven News Articles — ACM CHI 2024](https://dl.acm.org/doi/10.1145/3613904.3641916)
- [Narrative Visualization: Telling Stories with Data — Stanford Vis Group](http://vis.stanford.edu/files/2010-Narrative-InfoVis.pdf)
- [Map UI Design: Best Practices — Eleken](https://www.eleken.co/blog-posts/map-ui-design)
- [7 real examples of best practices for citing sources in infographics](https://examples-of.com/writing-communication/blog-post-formats/infographics/writing-communication-blog-post-formats-infographics-best-practices-for-citing-sources-in-infographics/)
- [10 Trends in Data Visualization to Watch in 2026 — Infogram](https://infogram.com/blog/10-trends-in-data-visualization-to-watch-in-2026/)

---
*Feature research for: civic health data visualization (touch-first kiosk, narrative drill-down)*
*Researched: 2026-03-24*
