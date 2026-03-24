# External Integrations

**Analysis Date:** 2026-03-24

## APIs & External Services

**None.** This is a fully client-side application with zero API calls. No `fetch()`, `axios`, `XMLHttpRequest`, or WebSocket usage anywhere in the codebase. All data is embedded directly in JavaScript constants.

## Data Sources (All Embedded)

All data lives as JavaScript object literals at the top of `src/App.jsx`. There are no external data fetches at runtime.

**`STATE_DATA`** (line 13, `src/App.jsx`):
- 50 US states + DC with `voterTurnout`, `uninsured`, `lifeExp` fields
- Sources cited in UI:
  - Voter Turnout: [MAP (Movement Advancement Project), 2024](https://www.lgbtmap.org/img/maps/citations-voter-turnout-percentage.pdf)
  - Uninsured Rate: [KFF (Kaiser Family Foundation), 2024](https://www.kff.org/state-health-policy-data/state-indicator/total-population/)
  - Life Expectancy: [CDC NVSR (National Vital Statistics Reports), 2023](https://www.cdc.gov/nchs/data/nvsr/nvsr74/nvsr74-12.pdf)

**`HEX_GRID`** (line 67, `src/App.jsx`):
- `{stateCode: [col, row]}` mapping for hexagonal US map layout
- Hand-authored positioning data

**`CD_PATHS`** (line 71, `src/App.jsx`):
- ~80 KB of SVG path strings for NYC community districts (59 districts across 5 boroughs)
- Includes `p` (SVG path data), `cx`, `cy` (centroid coordinates)
- Likely derived from NYC open geographic data, converted to simplified SVG paths

**`NYC_DISTRICTS`** (referenced in `src/App.jsx`):
- District-level metadata: `name`, `borough`, `le` (life expectancy), `turnout` (voter turnout %)
- Sources cited in UI:
  - Voter Turnout: [NYC Equity, 2024](https://equity.nyc.gov/domains/empowered-residents-and-neighborhoods/voter-turnout-rate)
  - Life Expectancy: [NYC DOHMH (Dept of Health and Mental Hygiene), 2023](https://www.nyc.gov/assets/doh/downloads/pdf/vs/2023sum.pdf)

**`HOSPITAL_PEOPLE`** (referenced in `src/App.jsx`):
- Character profiles with `id`, `name`, `age`, `role`, `color`, `x`, `y`, `bg`, `timeline`
- Fictional/composite narrative data (4 characters: maria, dr-okafor, james, sofia)

**`DEPARTMENTS`** (referenced in `src/App.jsx`):
- Hospital department layout: `id`, `label`, `x`, `y`, `w`, `h`, `wait`, `severity`, `note`
- Fictional health center floor plan data

## CDN & External Resource Dependencies

**Google Fonts** (runtime dependency):
- Loaded via CSS `@import` inside a `<style>` tag in the App component (line 667, `src/App.jsx`)
- URL: `https://fonts.googleapis.com/css2?family=Libre+Franklin:ital,wght@0,400;0,500;0,700;0,900;1,400;1,600&family=JetBrains+Mono:wght@400;600;700&display=swap`
- Fonts loaded:
  - **Libre Franklin** (weights: 400, 500, 700, 900; italic: 400, 600) - Primary UI font for headings, body text, labels
  - **JetBrains Mono** (weights: 400, 600, 700) - Monospace font for data values, statistics, metadata
- **Note:** The CLAUDE.md mentions Playfair Display as a loaded font, but it does NOT appear in the current `@import` URL or anywhere in the source code. Only Libre Franklin and JetBrains Mono are actually used.
- **Impact:** If Google Fonts CDN is unreachable, the app falls back to system fonts. No self-hosted font files exist in the project.

**No other CDN resources** - No external CSS frameworks, no icon libraries, no analytics scripts.

## Data Storage

**Databases:**
- None. Application is entirely client-side with embedded data.

**File Storage:**
- Local filesystem only (Vite serves `public/` directory)
- No file upload/download functionality

**Caching:**
- Browser-native caching only (HTTP cache headers from hosting provider)
- No service worker, no IndexedDB, no localStorage usage

## Authentication & Identity

**Auth Provider:**
- None. No authentication system. The application is a public, read-only data visualization.

## Monitoring & Observability

**Error Tracking:**
- None. No Sentry, LogRocket, or similar service.

**Logs:**
- Browser console only. No structured logging.

**Analytics:**
- None detected. No Google Analytics, Plausible, or similar tracking.

## CI/CD & Deployment

**Hosting:**
- Vercel (inferred from `.vercel` in `.gitignore`)
- Static SPA deployment (no SSR, no serverless functions, no API routes)

**CI Pipeline:**
- None detected. No `.github/workflows/`, no CI configuration files.
- No automated testing, linting, or deployment pipeline.

## Environment Configuration

**Required env vars:**
- None. The application requires zero environment configuration.
- No `.env` files exist in the repository.

**Secrets location:**
- Not applicable. No secrets, API keys, or credentials needed.

## Webhooks & Callbacks

**Incoming:**
- None

**Outgoing:**
- None

## External Links (Informational Only)

The UI contains outbound links to data source documentation. These are citation links only, not API integrations:

| Link Target | Context |
|-------------|---------|
| `lgbtmap.org` (PDF) | Voter turnout data source citation |
| `kff.org` | Uninsured rate data source citation |
| `cdc.gov` (PDF) | Life expectancy data source citation |
| `equity.nyc.gov` | NYC voter turnout data source citation |
| `nyc.gov` (PDF) | NYC life expectancy data source citation |

## Integration Summary

This application has an intentionally minimal integration surface:

- **Zero runtime API dependencies** - All data is embedded
- **One CDN dependency** - Google Fonts for typography (graceful degradation possible)
- **Zero backend services** - Pure static SPA
- **Zero auth/secrets** - Public read-only visualization

The only external network request the production app makes is to `fonts.googleapis.com` for font files. Everything else is self-contained in the JavaScript bundle.

---

*Integration audit: 2026-03-24*
