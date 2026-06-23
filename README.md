# wm-scaffold-reactjs-skill

An agent skill that scaffolds the standard folder structure, installs core packages, and bootstraps key files for a fresh Vite + React project — without implementing any business logic.

## Install the skill

Add this skill to any repo:

```bash
npx skills add wm-artifacts/wm-scaffold-reactjs-skill
```

## Before using the skill

The skill requires a Vite + React project to already exist. If you haven't created one yet:

```bash
# Create a new React project with Vite
npm create vite@latest app-name -- --template react

# Navigate to the project directory
cd app-name

# Install dependencies
npm install

# Start the development server
npm run dev
```

## Use the skill

Once the project is set up and the skill is installed, ask your agent to scaffold it:

> "Use @wm-scaffold-reactjs/SKILL.md to scaffold my React project structure"

or

> "Use @wm-scaffold-reactjs/SKILL.md to set up my Vite + React app"

The skill will:

- Ask whether you want **MUI (Material UI)** and/or **TanStack Query** installed
- Always install `react-router-dom` and `axios`
- Create a consistent `src/` folder layout with stub files for future features
- Bootstrap `main.jsx`, `App.jsx`, an axios API client, env files, and routing
- Update `.gitignore` and `package.json` scripts

## What the skill does

The skill turns a freshly-created Vite + React project into a ready-to-build foundation. The guiding principle is **structure, not logic** — it lays down the skeleton so you can start building features immediately, without making any assumptions about what the app does.

### 1. Asks two optional questions

Before touching anything, the skill asks:

- **MUI (Material UI)** — install `@mui/material`, `@emotion/react`, `@emotion/styled`? If yes, it creates a `src/theme/index.js` with a base `createTheme` and wires `ThemeProvider` + `CssBaseline` into `main.jsx`. If no, it creates a `src/theme/style.css` stub instead.
- **TanStack Query (React Query v5)** — install `@tanstack/react-query`? If yes, it bootstraps a `QueryClient` in `main.jsx` and creates stub files under `src/hooks/`.

### 2. Always installs

- `react-router-dom` — `BrowserRouter` wraps the app and a default `/` route is wired in `App.jsx`
- `axios` — an `apiClient` instance is bootstrapped reading `VITE_API_BASE_URL` from the environment

### 3. Creates a consistent folder structure

Every non-bootstrap file is a single comment stub — it signals intent without pretending to implement anything:

```
src/
├── api/endpoints.js          # API endpoint constants
├── components/
│   ├── layout/Layout.jsx     # shared page layout wrapper (stub)
│   └── ui/                   # reusable UI components
├── context/ThemeContext.jsx  # theme / dark-light toggle context (stub)
├── data/constants.js         # app-wide constants and route names
├── features/auth/authSlice.js # Redux auth slice stub
├── hooks/                    # custom + TanStack Query hooks
├── pages/HomePage.jsx        # root landing page (real component)
├── redux/store.js            # Redux store stub
├── services/
│   ├── apiClient.js          # axios instance wired to env
│   ├── baseService.js        # generic service layer
│   └── requestHeaders.js     # auth/common headers
├── theme/                    # MUI theme or global CSS
└── utils/formatters.js       # date/string/number helpers (stub)
```

### 4. Bootstraps key files

Fully working code is written for: `main.jsx`, `App.jsx`, `apiClient.js`, `requestHeaders.js`, `endpoints.js`, `constants.js`, `HomePage.jsx`, and `baseService.js`. All other files are one-line comment stubs.

### 5. Sets up environment files

Creates `.env`, `.env.dev`, and `.env.example` at the project root, and updates `.gitignore` so secrets never reach the repo while the template stays committed.

### 6. Updates `package.json` scripts

The `dev` script is updated to `vite --mode dev` so Vite loads `.env.dev` during local development.
