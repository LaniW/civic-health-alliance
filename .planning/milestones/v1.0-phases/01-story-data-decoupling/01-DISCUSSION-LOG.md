# Phase 1: Story Data Decoupling - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-03-24
**Phase:** 01-story-data-decoupling
**Areas discussed:** Callout content scope, Data field structure, Missing data fallback

---

## Callout Content Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Both data-driven | Make per-person summary AND shared tagline into data fields | |
| Summary only (Recommended) | Per-person summary becomes data field; shared tagline stays hardcoded | ✓ |

**User's choice:** "you decide" — deferred to Claude's discretion
**Notes:** Claude selected summary-only approach. The shared tagline is universal and doesn't need per-person variation.

---

## Data Field Structure

| Option | Description | Selected |
|--------|-------------|----------|
| Simple string (Recommended) | `person.summary` as a plain string | ✓ |
| Structured object | `{headline, tagline}` object for future extensibility | |

**User's choice:** "you decide" — deferred to Claude's discretion
**Notes:** Claude selected simple string. No over-engineering needed; Phase 4 swaps content, not shape.

---

## Missing Data Fallback

| Option | Description | Selected |
|--------|-------------|----------|
| Hide callout (Recommended) | If `person.summary` is falsy, don't render the callout box | ✓ |
| Generic message | Show a default message | |
| Tagline only | Show the shared tagline without per-person text | |

**User's choice:** "you decide" — deferred to Claude's discretion
**Notes:** Claude selected hide. Consistent with existing `if(!person) return null` pattern. All 4 people will have summaries so this is a safety net only.

## Claude's Discretion

All three areas were deferred to Claude by the user ("you decide").

## Deferred Ideas

None — discussion stayed within phase scope.
