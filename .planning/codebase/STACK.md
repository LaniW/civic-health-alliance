# Technology Stack

**Analysis Date:** 2026-03-10

## Languages

**Primary:**
- JavaScript (ECMAScript 2020+) - Frontend UI and components
- JSX - React component syntax

**Secondary:**
- CSS - Styling (inline and imported stylesheets)
- HTML - Page structure in `index.html`

## Runtime

**Environment:**
- Node.js (version not explicitly specified in package.json, inferred from npm ecosystem)

**Package Manager:**
- npm
- Lockfile: `package-lock.json` present

## Frameworks

**Core:**
- React 19.2.0 - UI library and component framework
- React DOM 19.2.0 - React rendering for web browsers

**Build/Dev:**
- Vite 7.3.1 - Bundler and dev server
- @vitejs/plugin-react 5.1.1 - Vite plugin for React with HMR and Fast Refresh

**Development Tools:**
- ESLint 9.39.1 - Code linting
- eslint-plugin-react-hooks 7.0.1 - React hooks linting rules
- eslint-plugin-react-refresh 0.4.24 - React Refresh/Fast Refresh linting

## Key Dependencies

**Production:**
- `react` ^19.2.0 - React library for building UI components
- `react-dom` ^19.2.0 - React rendering engine for web

**Development:**
- `@eslint/js` ^9.39.1 - ESLint core configuration
- `@types/react` ^19.2.7 - TypeScript type definitions for React (development only)
- `@types/react-dom` ^19.2.3 - TypeScript type definitions for React DOM (development only)
- `@vitejs/plugin-react` ^5.1.1 - React integration for Vite
- `eslint` ^9.39.1 - Code quality linting framework
- `eslint-plugin-react-hooks` ^7.0.1 - ESLint rules for React hooks best practices
- `eslint-plugin-react-refresh` ^0.4.24 - ESLint rules for React Fast Refresh
- `globals` ^16.5.0 - Global variables for ESLint environments
- `vite` ^7.3.1 - Frontend build tool and dev server

## Configuration

**Environment:**
- No `.env` files detected in repository
- Configuration is managed through inline JavaScript constants (e.g., `STATE_DATA`, `HEX_GRID`, `CD_PATHS`, `NYC_DISTRICTS` in `src/App.jsx`)

**Build:**
- `vite.config.js` - Vite configuration with React plugin
- `eslint.config.js` - ESLint flat config with React-specific rules
- `package.json` - Project metadata and dependency management

**Build Output:**
- Production build directory: `dist/`
- Entry point: `index.html`
- Main source file: `src/main.jsx`

## Platform Requirements

**Development:**
- Node.js (version unspecified, recommend Node 16+)
- npm (version unspecified, recommend 8+)
- Modern browser with ES2020 support

**Production:**
- Modern web browsers (target ES2020)
- Static file hosting (HTML, JS, CSS, assets)
- CDN for Google Fonts (googleapis.com) - required for custom fonts
- Optional: Vercel deployment (`.vercel` directory present in `.gitignore`)

## Scripts

**Available commands:**
- `npm run dev` - Start Vite development server with HMR
- `npm run build` - Build for production to `dist/` directory
- `npm run lint` - Run ESLint on all files
- `npm run preview` - Preview production build locally

## External Resources

**Fonts:**
- Google Fonts API (googleapis.com)
  - Playfair Display (weights: 400, 700, 800, 900)
  - Libre Baskerville (weights: 400, 700, italic variants)
  - JetBrains Mono (weights: 400, 600, 700)

---

*Stack analysis: 2026-03-10*
