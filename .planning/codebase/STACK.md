# Technology Stack

**Analysis Date:** 2026-03-24

## Languages

**Primary:**
- JavaScript (ES2020+, JSX) - All application code in `src/App.jsx`, `src/main.jsx`

**Secondary:**
- CSS - Minimal external stylesheets (`src/index.css`, `src/App.css`); the vast majority of styling is inline JS objects within JSX
- SVG - Extensive inline SVG markup for maps and data visualization (hex grid US map, NYC community district paths, hospital floor plan scene)
- HTML - Single entry point `index.html`

## Runtime

**Environment:**
- Node.js v25.8.1 (local development; no `.nvmrc` or `engines` field to enforce a version)
- Browser runtime (production) - modern browsers with ES module support

**Package Manager:**
- npm v11.12.0
- Lockfile: `package-lock.json` (lockfileVersion 3, present and committed)

## Frameworks

**Core:**
- React `19.2.4` (resolved from `^19.2.0`) - UI rendering, component model, hooks
- React DOM `19.2.4` (resolved from `^19.2.0`) - Browser DOM rendering target

**Testing:**
- None configured. No test framework, no test files, no test scripts in `package.json`.

**Build/Dev:**
- Vite `7.3.1` (resolved from `^7.3.1`) - Dev server with HMR, production bundler
- `@vitejs/plugin-react` `^5.1.1` - React Fast Refresh and JSX transform for Vite

**Linting:**
- ESLint `9.39.3` (resolved from `^9.39.1`) - Flat config format (v9)
- `@eslint/js` `^9.39.1` - ESLint recommended rule set
- `eslint-plugin-react-hooks` `^7.0.1` - Enforces React hooks rules
- `eslint-plugin-react-refresh` `^0.4.24` - Ensures components are HMR-compatible
- `globals` `^16.5.0` - Browser global definitions for ESLint

## Key Dependencies

**Runtime (2 packages total):**

| Package | Resolved Version | Purpose |
|---------|-----------------|---------|
| `react` | 19.2.4 | UI component framework |
| `react-dom` | 19.2.4 | DOM rendering |

This is an extremely lean dependency footprint. No routing library, no state management library, no CSS-in-JS library, no animation library.

**Dev Dependencies (9 packages total):**

| Package | Version | Purpose |
|---------|---------|---------|
| `vite` | 7.3.1 | Build tool and dev server |
| `@vitejs/plugin-react` | ^5.1.1 | React plugin for Vite (Fast Refresh, JSX) |
| `eslint` | 9.39.3 | JavaScript linter |
| `@eslint/js` | ^9.39.1 | ESLint base recommended rules |
| `eslint-plugin-react-hooks` | ^7.0.1 | Hooks linting rules |
| `eslint-plugin-react-refresh` | ^0.4.24 | HMR component export validation |
| `globals` | ^16.5.0 | Browser globals for ESLint |
| `@types/react` | ^19.2.7 | TypeScript type defs (unused - see note) |
| `@types/react-dom` | ^19.2.3 | TypeScript type defs (unused - see note) |

**Note on `@types/*` packages:** The project is pure JavaScript (`.jsx` files, no `tsconfig.json`, no TypeScript compiler). The `@types/react` and `@types/react-dom` packages were included by the Vite scaffold template but serve no purpose. They can be safely removed.

## Configuration

**Vite Config:** `vite.config.js`
- Minimal configuration: only enables `@vitejs/plugin-react`
- No custom aliases, no proxy, no SSR, no custom port
- Default Vite behavior: dev on port 5173, output to `dist/`

**ESLint Config:** `eslint.config.js`
- Flat config format (ESLint v9)
- Targets `**/*.{js,jsx}` files
- Ignores `dist/` directory
- Extends: `js.configs.recommended`, `reactHooks.configs.flat.recommended`, `reactRefresh.configs.vite`
- Custom rule: `no-unused-vars` ignores variables starting with uppercase or underscore (`varsIgnorePattern: '^[A-Z_]'`)
- ECMAScript 2020 language options with browser globals

**Module System:** ES Modules (`"type": "module"` in `package.json`)

**No formatter configured** - No Prettier, Biome, or similar formatting tool.

**No TypeScript** - Pure JavaScript with JSX.

## Build & Dev Commands

```bash
npm run dev      # Start Vite dev server with HMR (default port 5173)
npm run build    # Production build to dist/
npm run lint     # Run ESLint (flat config, v9)
npm run preview  # Preview production build locally
```

## React Hooks Used

The application uses only two React hooks from the `react` package:
- `useState` - All component state (level, phase, hover states, selected person)
- `useCallback` - Memoized `zoomTo` navigation function

No `useEffect`, `useRef`, `useMemo`, `useContext`, or `useReducer` detected anywhere in the codebase.

## Platform Requirements

**Development:**
- Node.js v25+ (based on local environment; no constraint enforced)
- npm v11+ (lockfileVersion 3)
- No OS-specific dependencies
- No native modules

**Production:**
- Static file hosting only (SPA with no server-side logic)
- Likely deployed to Vercel (`.vercel` directory listed in `.gitignore`)
- No API server, no database, no backend process

**Browser Compatibility:**
- Requires ES module support (`<script type="module">` in `index.html`)
- Uses modern CSS properties: `inset`, `gap`, `place-items`
- Uses SVG SMIL `<animate>` elements (not supported in older IE)
- No polyfills or compatibility transforms configured
- No explicit browserslist configuration

## Static Assets

- `public/vite.svg` - Favicon (Vite logo, 1.5 KB)
- `src/assets/react.svg` - React logo asset (4.1 KB)
- No images, no custom icon fonts, no other static media

---

*Stack analysis: 2026-03-24*
