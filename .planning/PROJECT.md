# Civic Health Alliance

## What This Is

An interactive data visualization that maps the relationship between civic engagement and health outcomes across the US, drilling from national to individual level. Built as a single-page React app with SVG-based maps and animations. Currently being refined for accuracy, touch usability, and real patient stories.

## Core Value

The visualization must present accurate, real data and stories — real patient experiences from clinical interviews, verified statistics from cited sources — and be fully navigable on touch screens.

## Requirements

### Validated

- ✓ 4-level drill-down (Title → US Map → NYC Map → Hospital → Person Story) — existing
- ✓ Hexagonal US state map colored by life expectancy + voter turnout — existing
- ✓ NYC community district map (59 districts, SVG paths) — existing
- ✓ Hospital scene with departments, wait times, and person figures — existing
- ✓ Person story timeline with health events and civic actions — existing
- ✓ Left panel with chapter narrative, legends, and data sources — existing
- ✓ Hover interactions showing state/district/person details — existing
- ✓ Zoom transitions with fade animation between levels — existing

### Active

- [ ] NavBar: back arrow button for touch screen navigation
- [ ] NavBar: tappable breadcrumbs to navigate to any previous level
- [ ] Hospital scene: center person figures within their department rooms
- [ ] Hospital scene: fix text overlap in department labels/room content
- [ ] Replace fictional character stories (Maria, Dr. Okafor, James, Sofia) with real patient stories from Stella Saffo's transcript
- [ ] Verify all stats in left panel against cited sources (CDC, KFF, MAP, NYC DOHMH)

### Out of Scope

- Code decomposition / refactoring into multiple files — not part of this work
- Adding new zoom levels or data dimensions — current structure is final
- Backend / API / database — data stays embedded in JS

## Context

- App is a single-file monolith (`src/App.jsx`, ~730 lines) — all data, components, and styling inline
- Touch screen is a primary use case (likely kiosk/presentation), making back navigation critical
- Real patient stories come from a transcript by Stella Saffo, a clinician describing 3 patient experiences involving Medicaid/insurance gaps, HIV care continuity, and NY state benefits
- Stats are sourced from: MAP (voter turnout), KFF (uninsured rate), CDC NVSR (life expectancy), NYC Equity (NYC voter turnout), NYC DOHMH (NYC life expectancy)

## Constraints

- **Tech stack**: React + Vite, no additional dependencies — keep it minimal
- **Single file**: All changes go in `src/App.jsx` for now
- **Data accuracy**: All displayed numbers must match cited sources exactly
- **Touch usability**: All navigation must work without a mouse or browser back button

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Replace fictional stories with real transcript | Authenticity matters — real patient voices are more impactful | — Pending |
| Back arrow + tappable breadcrumbs (both) | Touch screen users need explicit navigation controls | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd:transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-03-24 after initialization*
