---
name: wm-scaffold-reactjs
description: >-
  Scaffolds the standard folder structure, installs core packages, and
  bootstraps key files for a fresh Vite + React project — without implementing
  any business logic. Use this skill whenever the user wants to set up,
  scaffold, bootstrap, initialize, or lay out the structure of a new React
  (Vite) app, or mentions wiring up a project's folders, routing (react-router),
  axios api client, MUI, TanStack Query / React Query, a Redux stub, or an
  environment-file setup. Trigger even if the user only says something like
  "set up my React project structure", "scaffold a new Vite app", or "get my
  fresh React project ready to build on top of", as long as a Vite + React
  project already exists. Do NOT use this for implementing features, writing
  components, or building business logic.
---

# wm-scaffold-reactjs

This skill turns a freshly-created Vite + React project into a ready-to-build
foundation. It creates a consistent folder structure, installs an agreed-upon
set of packages, and writes minimal bootstrap files so the developer can start
building features immediately.

The guiding principle is **structure, not logic**. The skill lays down the
skeleton — folders, provider wiring, an axios client, an env-file setup — but it
deliberately leaves every feature file as a one-line comment stub. This keeps
the scaffold opinionated about *layout* while staying completely unopinionated
about *what the app does*, so it works equally well for any new project.

## Prerequisite (confirm before doing anything)

The user must already have a Vite + React project created and dependencies
installed. Confirm this before proceeding:

```
npm create vite@latest <app-name> -- --template react
cd <app-name>
npm install
```

If they haven't done this yet, point them to these commands and wait until they
confirm the project exists and `npm install` has run. Everything below assumes
you're operating inside that project directory.

## Step 1 — Ask exactly two questions

Two libraries are optional and depend on the developer's taste. Ask only these
two, clearly, before touching anything:

1. **MUI (Material UI) — Recommended.** Install `@mui/material`,
   `@emotion/react`, `@emotion/styled`?
   - **Yes** → install the packages, create `src/theme/index.js` exporting a
     basic `createTheme`, and wire `ThemeProvider` + `CssBaseline` into
     `main.jsx`.
   - **No** → skip the install, but still create `src/theme/style.css` as a
     comment-only stub for global/custom styles.

2. **TanStack Query (React Query v5).** Install `@tanstack/react-query`?
   - **Yes** → install it, bootstrap a `QueryClient` in `main.jsx`, and create
     comment-only stub files under `src/hooks/` for future query hooks.
   - **No** → skip the install, but still create the `src/hooks/` folder (empty,
     no files).

These four are **always** installed/configured — never ask about them:

- `react-router-dom` — always installed. `BrowserRouter` always wraps the app in
  `main.jsx`, and a single default route `/` → `HomePage` is wired in `App.jsx`.
- `axios` — always installed. `src/services/apiClient.js` is always bootstrapped
  with `axios.create` reading `import.meta.env.VITE_API_BASE_URL`.

**Redux note:** do *not* ask about Redux Toolkit. Always create
`src/redux/store.js` and `src/features/auth/authSlice.js` as comment-only stubs.
No Redux package is installed; the stub comments explain how to wire it up later
if the developer chooses to.

After collecting the two answers, **list the full set of packages to be
installed and ask for one final confirmation before running any `npm install`.**
This avoids surprising the developer with installs they didn't expect.

## Step 2 — Install packages

Always run:

```
npm install react-router-dom axios
```

Then, conditional on the answers from Step 1:

- MUI confirmed: `npm install @mui/material @emotion/react @emotion/styled`
- TanStack confirmed: `npm install @tanstack/react-query`

## Step 3 — Create the folder structure

Create the tree below under `src/`. The point of this layout is a clean
separation of concerns: API definitions, the service/transport layer, shared
UI, page-level views, app state, and utilities each get a predictable home.

Every file that is **not** a bootstrap file (see Step 4) must contain **only a
single comment line** describing its purpose — no imports, no logic, no exports.
This signals intent to the next developer without pretending to implement
anything.

```
src/
├── api/
│   └── endpoints.js          # API endpoint constants + VITE_API_BASE_URL (bootstrap)
├── assets/                   # static assets (images, svgs) — no file to create
├── components/
│   ├── layout/
│   │   └── Layout.jsx        # shared page layout wrapper (stub)
│   └── ui/                   # reusable generic UI components — empty folder
├── context/
│   └── ThemeContext.jsx      # theme / dark-light toggle context (stub)
├── data/
│   └── constants.js          # app-wide constants, route names (bootstrap)
├── features/
│   └── auth/
│       └── authSlice.js      # Redux auth slice stub
├── hooks/                    # custom + TanStack Query hooks — always created
├── pages/
│   └── HomePage.jsx          # root landing page — real component (bootstrap)
├── redux/
│   └── store.js              # Redux store stub
├── services/
│   ├── apiClient.js          # axios instance (bootstrap)
│   ├── baseService.js        # generic service layer — imports + comment (bootstrap)
│   └── requestHeaders.js     # auth/common headers (bootstrap)
├── theme/
│   ├── index.js              # MUI createTheme — only if MUI confirmed
│   └── style.css             # global styles fallback — only if MUI NOT confirmed
└── utils/
    └── formatters.js         # date/string/number helpers (stub)
```

The folders `src/redux/`, `src/hooks/`, and `src/theme/` are **always** created,
regardless of which optional packages were chosen — they're load-bearing parts
of the convention even when empty.

## Step 4 — Bootstrap file contents

Bootstrap files get minimal *working* code; everything else gets a one-line
comment. Use the exact content below, applying the conditional pieces only when
the relevant package was confirmed.

### `src/main.jsx`

Compose providers from outermost to innermost in this order — the order matters
because routing and data/query context should be available to the whole tree,
and MUI's theme should wrap the rendered UI:

1. `<StrictMode>` (always)
2. `<BrowserRouter>` (always)
3. `<QueryClientProvider client={queryClient}>` (only if TanStack confirmed)
4. `<ThemeProvider theme={theme}><CssBaseline /></ThemeProvider>` (only if MUI confirmed)
5. `<App />`

When TanStack is confirmed, create the client with these defaults (one retry and
no refetch-on-focus is a sane, quiet starting point most teams keep):

```js
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 1,
      refetchOnWindowFocus: false,
    },
  },
});
```

Import `theme` from `./theme` and `App` from `./App`. Render into
`document.getElementById('root')` as the Vite template does.

### `src/App.jsx` (always)

```jsx
import { Routes, Route } from 'react-router-dom';
import HomePage from './pages/HomePage';
import { ROUTES } from './data/constants';

function App() {
  return (
    <Routes>
      <Route path={ROUTES.HOME} element={<HomePage />} />
    </Routes>
  );
}

export default App;
```

### `src/theme/index.js` (only if MUI confirmed)

```js
import { createTheme } from '@mui/material';

const theme = createTheme({
  palette: {
    mode: 'light',
    primary: {
      main: '#1976d2',
    },
    secondary: {
      main: '#9c27b0',
    },
  },
});

export default theme;
```

### `src/theme/style.css` (only if MUI NOT confirmed)

```css
/* Global and custom styles — add app-wide CSS variables, resets, and overrides here */
```

### `src/api/endpoints.js`

```js
// Base URL consumed from .env.dev via Vite — set VITE_API_BASE_URL in your env file
export const API_BASE_URL = import.meta.env.VITE_API_BASE_URL;

// Add all API endpoint paths here as named exports
export const ENDPOINTS = {};
```

### `src/services/apiClient.js`

```js
import axios from 'axios';
import { API_BASE_URL } from '../api/endpoints';
import { getHeaders } from './requestHeaders';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

apiClient.interceptors.request.use(
  (config) => {
    config.headers = { ...config.headers, ...getHeaders() };
    return config;
  },
  (error) => Promise.reject(error)
);

apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    console.error('API error:', error.response?.data || error.message);
    return Promise.reject(error);
  }
);

export default apiClient;
```

### `src/services/requestHeaders.js`

```js
// Returns common/auth headers built from environment variables
// Add any token or auth header logic here
export function getHeaders() {
  return {
    'Content-Type': 'application/json',
  };
}
```

### `src/data/constants.js`

```js
// App-wide route names and static configuration constants
export const ROUTES = {
  HOME: '/',
};
```

### `src/pages/HomePage.jsx`

`App.jsx` imports `HomePage` as a default export and renders it at the `/`
route, so `HomePage.jsx` **must** be a real component with a default export — a
comment-only stub here causes a runtime error like *"does not provide an export
named 'default'"* and the app won't boot. Give it a minimal but working sample
component so the freshly-scaffolded app renders something immediately.

If **MUI was confirmed**, use MUI primitives so the sample demonstrates the
theme is wired up:

```jsx
import { Box, Typography } from '@mui/material';

function HomePage() {
  return (
    <Box sx={{ p: 4 }}>
      <Typography variant="h4" component="h1" gutterBottom>
        Welcome
      </Typography>
      <Typography variant="body1">
        Your app scaffold is ready. Start building from here.
      </Typography>
    </Box>
  );
}

export default HomePage;
```

If **MUI was NOT confirmed**, use plain HTML tags so there's no dependency on an
uninstalled library:

```jsx
function HomePage() {
  return (
    <main style={{ padding: '2rem' }}>
      <h1>Welcome</h1>
      <p>Your app scaffold is ready. Start building from here.</p>
    </main>
  );
}

export default HomePage;
```

### `src/services/baseService.js`

Seed the imports so the developer can start writing service functions right
away without hunting down the right paths. The body stays a comment — no
implemented calls — but the `apiClient` and `ENDPOINTS` imports are pre-wired:

```js
import apiClient from './apiClient';
import { ENDPOINTS } from '../api/endpoints';

// Generic service layer — add reusable API call functions here using apiClient and ENDPOINTS
```

### Comment-only stub files

The following files contain a single comment line and nothing else. They mark
where future code belongs without implementing it:

`src/redux/store.js` (always created)
```js
// Redux store — always scaffolded as a stub.
// To activate: npm install @reduxjs/toolkit react-redux
// Then: import { configureStore } from '@reduxjs/toolkit' and register your slices here.
```

`src/features/auth/authSlice.js` (always created)
```js
// Redux auth slice stub — always scaffolded.
// To activate: import { createSlice } from '@reduxjs/toolkit' and define auth state here.
```

`src/utils/formatters.js`
```js
// Utility/helper functions — date formatting, string manipulation, number formatting, etc.
```

`src/context/ThemeContext.jsx`
```jsx
// React context for app-level theme or dark/light mode toggle
```

`src/components/layout/Layout.jsx`
```jsx
// Shared layout wrapper — typically contains Header, Sidebar, and an <Outlet /> for nested routes
```

`src/hooks/` is always created as a folder. If TanStack Query was confirmed and
you add any hook files here, each gets only a single comment describing the
hook's purpose — no imports, exports, or logic.

## Step 5 — Environment files at the project root

The env split keeps local development, production, and a committable template
cleanly separated. `.env.dev` drives local dev, `.env` is for production, and
`.env.example` is the safe-to-commit template.

`.env`
```
# Production environment variables
VITE_API_BASE_URL=
```

`.env.dev`
```
# Development environment variables — used when running: vite --mode dev
VITE_API_BASE_URL=http://localhost:3000
```

`.env.example`
```
# Copy this file to .env.dev and fill in your values
VITE_API_BASE_URL=
```

Then update the project-root `.gitignore` so real env files (which hold secrets)
stay out of version control, while `.env.example` (the safe template) is still
committed. Append this block to the existing Vite `.gitignore` — the `!.env.example`
negation must come *after* the `.env.*` line so it isn't re-ignored:

```
# Environment files — keep secrets out of version control, but commit the template
.env
.env.*
!.env.example
```

## Step 6 — Update `package.json` scripts

Make sure the `dev` script passes `--mode dev` so Vite loads `.env.dev` during
development. Update the `scripts` block to:

```json
"scripts": {
  "dev": "vite --mode dev",
  "build": "vite build",
  "lint": "eslint .",
  "preview": "vite preview"
}
```

## Rules to hold throughout

These constraints are what keep the scaffold reusable across every project — if
you start implementing logic, the scaffold stops being a neutral starting point:

- **No feature logic.** No real API calls, hook implementations, Redux reducers,
  or business component bodies. The one exception is `HomePage.jsx`, which needs
  a minimal working component (and default export) so the app actually boots —
  keep it a placeholder "Welcome" view, not a real feature.
- **Non-bootstrap files are one comment line each.** Bootstrap files
  (`main.jsx`, `App.jsx`, `theme/index.js`, `apiClient.js`,
  `requestHeaders.js`, `endpoints.js`, `constants.js`, `HomePage.jsx`, and
  `baseService.js`) get the minimal working code shown above, conditional on
  confirmed packages. `baseService.js` gets its `apiClient` + `ENDPOINTS`
  imports plus a comment — no implemented calls.
- **`.env` files are git-ignored; `.env.example` is committed.** Update
  `.gitignore` so secrets never reach the repo while the template stays tracked.
- **Always confirm packages before installing.**
- **All config comes from `import.meta.env.VITE_*`** — base URLs, tokens, and
  credentials are read from env, never hardcoded.
- **`src/redux/`, `src/hooks/`, and `src/theme/` are always created**, whatever
  the package choices.
