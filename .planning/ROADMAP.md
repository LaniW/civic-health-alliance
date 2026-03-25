# Roadmap: Civic Health Alliance

## Overview

Refine the Civic Health Alliance from a working four-level visualization with placeholder stories into a credible, touch-navigable data narrative grounded in real patient experiences and verified public data. The work proceeds in strict dependency order: decouple PersonStory from hard-coded person IDs so content can be swapped cleanly, add touch navigation controls (back arrow, breadcrumbs, swipe), fix hospital scene SVG layout (centering, overlaps, tap targets), then replace fictional stories with real transcript content and verify all displayed statistics against primary sources.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Story Data Decoupling** - Decouple PersonStory from hard-coded person IDs so story content is data-driven (completed 2026-03-24)
- [ ] **Phase 2: Touch Navigation** - Add back arrow, tappable breadcrumbs, swipe navigation, and touch optimizations to NavBar
- [x] **Phase 3: Hospital Scene Layout** - Center person figures, fix text overlaps, and add touch-friendly tap targets in the hospital SVG (completed 2026-03-24)
- [ ] **Phase 4: Real Content and Data Verification** - Replace fictional stories with real patient experiences and verify all stats against cited sources

## Phase Details

### Phase 1: Story Data Decoupling
**Goal**: PersonStory renders any person's story from data fields alone, with no knowledge of specific person IDs
**Depends on**: Nothing (first phase)
**Requirements**: STORY-01
**Success Criteria** (what must be TRUE):
  1. PersonStory component renders summary, timeline, and narrative from data fields on the person object -- not from conditional checks on person.id
  2. Changing a person's id in HOSPITAL_PEOPLE does not break or blank their story display
**Plans:** 1/1 plans complete

Plans:
- [x] 01-01-PLAN.md -- Add summary field to HOSPITAL_PEOPLE and replace person.id conditionals in PersonStory

### Phase 2: Touch Navigation
**Goal**: Users can navigate the full four-level hierarchy forward and backward using only touch gestures -- no mouse, no browser back button required
**Depends on**: Phase 1
**Requirements**: NAV-01, NAV-02, NAV-03, NAV-04
**Success Criteria** (what must be TRUE):
  1. User can tap a back arrow button (44px+ touch target) in the NavBar to return to the previous level from any level deeper than the title screen
  2. User can tap any breadcrumb segment to jump directly to that level (backward-only; no forward jumps)
  3. All interactive elements respond immediately to touch with no 300ms delay
  4. User can swipe left/right to navigate between adjacent levels
**Plans**: TBD
**UI hint**: yes

Plans:
- [ ] 02-01: TBD

### Phase 3: Hospital Scene Layout
**Goal**: The hospital scene is visually clean -- person figures sit inside their rooms, text is readable, and every figure is tappable on touch screens
**Depends on**: Phase 1
**Requirements**: HOSP-01, HOSP-02, HOSP-03
**Success Criteria** (what must be TRUE):
  1. Every person figure is visually centered within the bounds of its assigned department room
  2. No text overlaps between department labels, wait times, or room notes at any supported viewport size
  3. Every person figure has a tap target of at least 44px equivalent in SVG units, responding to touch without requiring precise finger placement
**Plans:** 1/1 plans complete

Plans:
- [x] 03-01-PLAN.md -- Fix person figure coordinates (centering + overlap) and add invisible touch target rects

### Phase 03.1: address remote and local conflicts (INSERTED)

**Goal:** Merge remote branch geometry/content redesign with local Phase 1 summary fields and Phase 3 touch infrastructure into a unified codebase
**Requirements**: HOSP-01, HOSP-02, HOSP-03, STORY-01
**Depends on:** Phase 3
**Plans:** 1/1 plans complete

Plans:
- [x] 03.1-01-PLAN.md -- Merge remote DEPARTMENTS geometry, content text, NavBar redesign with local summary fields and touch targets

### Phase 4: Real Content and Data Verification
**Goal**: The visualization tells real stories from clinical experience and displays statistics that match their cited primary sources exactly
**Depends on**: Phase 1, Phase 2, Phase 3
**Requirements**: STORY-02, DATA-01, DATA-02, DATA-03
**Success Criteria** (what must be TRUE):
  1. Patient stories reflect real experiences from Stella Saffo's clinical transcript -- covering Medicaid/pancreatitis, HIV care continuity, and pregnant person housing -- not the fictional Maria/Dr. Okafor/James/Sofia placeholders
  2. Every US state stat (voter turnout, uninsured rate, life expectancy) in STATE_DATA matches the cited primary source (MAP VEP turnout, KFF non-elderly uninsured, CDC NVSR life expectancy) exactly
  3. Every NYC district stat (voter turnout, life expectancy) in NYC_DISTRICTS matches the cited primary source (NYC Equity, NYC DOHMH) exactly
  4. Data methodology is documented in code comments: denominator definitions, election cycles, population bases, and specific source table references for every metric
**Plans**: TBD

Plans:
- [ ] 04-01: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 03.1 -> 4
Note: Phases 2 and 3 can run in parallel after Phase 1 completes (they touch non-overlapping code regions).

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Story Data Decoupling | 1/1 | Complete   | 2026-03-24 |
| 2. Touch Navigation | 0/1 | Not started | - |
| 3. Hospital Scene Layout | 1/1 | Complete   | 2026-03-24 |
| 03.1. Remote/Local Merge | 0/1 | Complete    | 2026-03-25 |
| 4. Real Content and Data Verification | 0/1 | Not started | - |
