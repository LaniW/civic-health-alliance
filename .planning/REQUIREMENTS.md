# Requirements: Civic Health Alliance

**Defined:** 2026-03-24
**Core Value:** Accurate, real data and stories -- fully navigable on touch screens

## v1 Requirements

Requirements for this milestone. Each maps to roadmap phases.

### Navigation

- [ ] **NAV-01**: User can tap a back arrow in the NavBar to return to the previous level (44px+ touch target)
- [ ] **NAV-02**: User can tap any breadcrumb segment to jump directly to that level (backward-only navigation)
- [ ] **NAV-03**: All interactive elements use `touch-action: manipulation` to eliminate 300ms tap delay
- [ ] **NAV-04**: User can swipe left/right to navigate between levels

### Hospital Scene

- [x] **HOSP-01**: Person figures are visually centered within their department rooms
- [x] **HOSP-02**: No text overlaps in department labels, wait times, or room notes
- [x] **HOSP-03**: Person figures have touch-friendly tap targets (44px+ equivalent in SVG units)

### Stories

- [x] **STORY-01**: PersonStory component uses data fields instead of hard-coded person ID checks
- [ ] **STORY-02**: Patient stories reflect real experiences from Stella Saffo's clinical transcript (Medicaid/pancreatitis, HIV care continuity, pregnant person housing)

### Data Verification

- [ ] **DATA-01**: All US state stats (voter turnout, uninsured rate, life expectancy) match cited sources exactly
- [ ] **DATA-02**: All NYC district stats (voter turnout, life expectancy) match cited sources exactly
- [ ] **DATA-03**: Data methodology documented (denominators, election cycles, population definitions) in code comments

## v2 Requirements

Deferred to future milestone. Tracked but not in current roadmap.

### Stories

- **STORY-03**: Fourth patient story archetype added (transcript contains 3; a 4th would need additional source material)

### Accessibility

- **A11Y-01**: Full WCAG 2.1 AA audit and remediation
- **A11Y-02**: Screen reader support for SVG map elements

## Out of Scope

| Feature | Reason |
|---------|--------|
| Code decomposition into multiple files | Not part of this milestone -- single-file structure preserved |
| New zoom levels or data dimensions | Current 4-level structure is final |
| Backend / API / database | Data stays embedded in JS |
| Mobile responsive layout | App targets kiosk/presentation, not mobile browsers |
| Real-time data APIs | Static data is appropriate for this visualization |
| User filtering / search | Narrative product, not a dashboard |
| Bivariate color encoding | Deferred from previous milestone scope |
| HOLC redlining overlay | Deferred from previous milestone scope |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| NAV-01 | Phase 2 | Pending |
| NAV-02 | Phase 2 | Pending |
| NAV-03 | Phase 2 | Pending |
| NAV-04 | Phase 2 | Pending |
| HOSP-01 | Phase 3 | Complete |
| HOSP-02 | Phase 3 | Complete |
| HOSP-03 | Phase 3 | Complete |
| STORY-01 | Phase 1 | Complete |
| STORY-02 | Phase 4 | Pending |
| DATA-01 | Phase 4 | Pending |
| DATA-02 | Phase 4 | Pending |
| DATA-03 | Phase 4 | Pending |

**Coverage:**
- v1 requirements: 12 total
- Mapped to phases: 12
- Unmapped: 0

---
*Requirements defined: 2026-03-24*
*Last updated: 2026-03-24 after roadmap creation*
