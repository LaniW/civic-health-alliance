# External Integrations

**Analysis Date:** 2026-03-10

## APIs & External Services

**Not Detected:**
- No third-party API client SDKs detected in dependencies
- No REST client libraries (axios, fetch abstractions) implemented
- No authentication service integrations detected

## Data Storage

**Databases:**
- None - Application uses client-side data only
- All data is embedded in JavaScript constants within `src/App.jsx`

**File Storage:**
- Not applicable - application contains no file upload/download functionality
- Uses CDN for fonts and assets only

**Caching:**
- Browser-based caching only (no caching service configured)

## Authentication & Identity

**Auth Provider:**
- None - No authentication system implemented
- Application is stateless and requires no user login

**Authorization:**
- No role-based access control or permission system

## Data & State Management

**Application Data:**
- Hardcoded state data in `src/App.jsx`:
  - `STATE_DATA`: US state-level health metrics (voter turnout, uninsured rates, life expectancy)
  - `HEX_GRID`: Hexagonal grid coordinates for state visualization
  - `CD_PATHS`: Congressional district SVG path definitions for NYC map
  - `NYC_DISTRICTS`: NYC district-level health metrics and voter turnout

**State Management:**
- React hooks (`useState`, `useCallback`) for local component state
- No global state management library (Redux, Zustand, Context API) implemented
- Unidirectional data flow with component-level state

## Monitoring & Observability

**Error Tracking:**
- None - No error tracking service integrated

**Logs:**
- Browser console only (no centralized logging)

**Analytics:**
- None - No analytics service integrated

## CI/CD & Deployment

**Hosting:**
- Vercel support detected (`.vercel` in `.gitignore`)
- Static file hosting compatible (HTML/JS/CSS)

**CI Pipeline:**
- None detected - No GitHub Actions, GitLab CI, or other CI configuration

## Environment Configuration

**Required env vars:**
- None - Application requires no environment configuration
- No `.env` file patterns detected

**Secrets location:**
- Not applicable - No secrets management required

## Webhooks & Callbacks

**Incoming:**
- None

**Outgoing:**
- None

## External Dependencies Summary

**Content Delivery:**
- Google Fonts API (`https://fonts.googleapis.com/css2`) - Provides custom typography
  - Network request required at runtime for font files

**Frontend Libraries:**
- React ecosystem (react, react-dom, @vitejs/plugin-react)
- Development tools (ESLint plugins, Vite)

**Build-Time Only:**
- All development dependencies are build-time only
- No runtime dependencies on npm packages beyond React and React DOM

## Notes

This is a **client-side only application** with:
- No backend API integration
- No database connectivity
- No external service dependencies at runtime
- Fully self-contained data and logic
- Zero authentication or authorization requirements

The application can function entirely offline once the HTML, CSS, and JavaScript bundles are loaded. The only external resource required is Google Fonts for typography, which can be preloaded or fallback fonts used.

---

*Integration audit: 2026-03-10*
