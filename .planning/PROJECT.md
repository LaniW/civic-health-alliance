# Civic Health Alliance

## What This Is

An interactive data visualization website that reveals the bidirectional connection between civic engagement and health outcomes. Users drill down from a US state map → NYC community districts → a health center scene → individual human stories, seeing at each layer how political exclusion creates health gaps — and how health crises suppress civic participation. Audience: general public and voters.

## Core Value

Show that civic exclusion and poor health are a self-reinforcing loop — and make that visible through real data and real NYC stories, not abstractions.

## Requirements

### Validated

- ✓ US hexmap visualization with per-state health and civic metrics — existing
- ✓ NYC community district map colored by health/civic data — existing
- ✓ Health center scene with room departments and person figures — existing
- ✓ Individual person story view with event timeline — existing
- ✓ Animated zoom transitions between all four layers — existing
- ✓ Info panel overlays on hover — existing

### Active

- [ ] Health center layer replaced with real data showing civic engagement ↔ health institution outcomes (funding, staffing, research, closures)
- [ ] Health center visualizations redesigned/extended as needed to fit real data
- [ ] US state data replaced or verified with real voter turnout, uninsured rate, and life expectancy figures
- [ ] NYC district data replaced with real health and civic participation metrics
- [ ] 4 individual NYC-rooted stories built from real documented cases, each showing a different mechanism of civic exclusion → health gap
- [ ] Stories represent demographic contrast: undocumented immigrant, legal permanent resident (Medicaid bar), elderly Black woman in historically redlined neighborhood, and one additional archetype (TBD during research)
- [ ] Each person's timeline shows intersection of health events and civic moments (elections, policy changes, community organizing)
- [ ] Visualizations accessible and emotionally resonant for general public

### Out of Scope

- Named real individuals without consent — composite portraits built from documented statistics are acceptable
- Backend or database — all data stays hardcoded in the client
- Login / authentication
- Mobile-first redesign — existing layout preserved unless data requires changes

## Context

The site already has a fully working four-level zoom structure (US → NYC → Hospital → Person). All current data is fake/sample. The project is to replace that data with real, researched data and reshape visualizations where needed to better tell the story.

The feedback loop thesis: low civic participation → underfunded/inaccessible health institutions → worse health outcomes → further reduced capacity to participate civically. Individual stories illustrate specific mechanisms: immigration status as civic exclusion, the 5-year Medicaid bar for legal permanent residents, structural disenfranchisement of redlined communities.

NYC focus for individual stories maintains geographic coherence with the existing NYC district map layer.

## Constraints

- **Tech stack**: React + Vite, SVG-based visualizations, all data inline — no new external libraries without strong justification
- **Data**: Must be from citable public sources (CDC, Census, NYC DOH, academic studies) — site is for general public but data must be defensible
- **Scope**: Visualization changes are allowed, but the four-level zoom architecture is preserved

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Bidirectional feedback loop framing | More truthful and compelling than one-direction causation | — Pending |
| NYC-rooted individual stories | Keeps geographic coherence with NYC district layer | — Pending |
| Composite data portraits (not named individuals) | Avoids consent issues, still grounded in real statistics | — Pending |
| General public audience | Drives awareness and action, not just research citation | — Pending |

---
*Last updated: 2026-03-10 after initialization*
