# Phase 1: Story Data Decoupling - Context

**Gathered:** 2026-03-24
**Status:** Ready for planning

<domain>
## Phase Boundary

Make PersonStory render any person's story from data fields alone, with no knowledge of specific person IDs. The only hard-coded ID dependency is the callout box summary text at lines 543-548 of `src/App.jsx` — four `person.id === "..."` conditionals. Everything else (name, age, role, bg, timeline) already renders from data.

</domain>

<decisions>
## Implementation Decisions

### Claude's Discretion

All three gray areas deferred to Claude's judgment by the user:

- **D-01: Callout content scope** — Make the per-person bold summary a data field on the person object. Keep the shared tagline ("Civic engagement isn't abstract — it is the mechanism by which need becomes care.") as a hardcoded constant. Rationale: the tagline is a universal message that applies to all stories and doesn't need to vary per person.

- **D-02: Data field structure** — Use a simple string field (`person.summary`). No structured object needed. Phase 4 will swap the summary text content, but the shape stays the same. KISS.

- **D-03: Missing data fallback** — If `person.summary` is falsy, hide the callout box entirely. Consistent with the existing defensive pattern (`if(!person) return null`). All four people will have summaries, so this is a safety net only.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

No external specs — requirements fully captured in decisions above.

### Codebase
- `src/App.jsx` lines 200-205 — `HOSPITAL_PEOPLE` data array (add `summary` field here)
- `src/App.jsx` lines 512-553 — `PersonStory` component (remove ID checks, read from data)
- `src/App.jsx` lines 543-548 — Hard-coded `person.id` conditionals (the specific lines to replace)

### Requirements
- `.planning/REQUIREMENTS.md` §STORY-01 — "PersonStory component uses data fields instead of hard-coded person ID checks"

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `HOSPITAL_PEOPLE` array (line 200-205): Already holds all person data fields; just needs a `summary` string added to each entry
- `typeColor` object (line 215): Used by PersonStory timeline — no changes needed

### Established Patterns
- All data lives as inline JS object literals at the top of `src/App.jsx`
- PersonStory already reads every other field from the person object via props
- Defensive null check pattern: `if(!person) return null`

### Integration Points
- `App` component passes `selectedPerson` to `PersonStory` as the `person` prop (line ~724)
- No other components read the summary text — change is isolated to HOSPITAL_PEOPLE + PersonStory

</code_context>

<specifics>
## Specific Ideas

No specific requirements — open to standard approaches

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 01-story-data-decoupling*
*Context gathered: 2026-03-24*
