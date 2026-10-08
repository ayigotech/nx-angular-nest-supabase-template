# Nx Angular + NestJS + Supabase Template

Production-ready Nx monorepo template for building modern full-stack applications with Angular (PWA), NestJS, and Supabase.

## Stack

| Layer | Technology | Version |
|---|---|---|
| Monorepo | Nx | 23 |
| Frontend | Angular | 22 |
| Frontend Runtime | PWA + Service Worker | 22 |
| Backend | NestJS | 11 |
| Database & Auth | Supabase | latest |
| Package Manager | pnpm | 10 |
| Testing | Jest (Node) · Vitest (Angular) · Playwright (E2E) |
| Deployment | Vercel |

## Project Structure

```
.
├── api/                          # NestJS backend
├── api-e2e/                      # Backend E2E tests (Playwright)
├── client/                       # Angular PWA
├── client-e2e/                   # Frontend E2E tests (Playwright)
├── libs/
│   ├── data-access/
│   │   └── supabase-client/      # Supabase wrapper (shared)
│   └── shared/
│       ├── types/                # Shared TS interfaces
│       └── ui/                   # Shared Angular components
├── nx.json                       # Nx workspace config
├── tsconfig.base.json            # Root TS config + path aliases
└── pnpm-workspace.yaml           # pnpm workspace definition
```

## Getting Started

### 1. Clone this template

```bash
git clone https://github.com/ayigotech/nx-angular-nest-supabase-template.git my-new-project
cd my-new-project
rm -rf .git
git init
git branch -M main
```

### 2. Rename the workspace

Edit `package.json` and change:

```json
"name": "nx-angular-nest-supabase-template"
```

to your project name (e.g., `"name": "my-new-project"`).

### 3. Install dependencies

```bash
pnpm install
```

### 4. Configure environment variables

Create `.env` files in each app (they are gitignored).

**`client/.env`** — frontend, safe to expose to the browser:

```
NG_APP_SUPABASE_URL=https://your-project.supabase.co
NG_APP_SUPABASE_ANON_KEY=your-anon-key
```

**`api/.env`** — backend, never commit:

```
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
CORS_ORIGIN=http://localhost:4200
```

### 5. Verify the build

```bash
npx nx run-many -t build
```

All projects should build successfully.

## Daily Workflow

### Serve apps

```bash
# Both apps at once (client on :4200, api on :3000)
npx nx run-many -t serve --projects=client,api

# Single app
npx nx serve client
npx nx serve api
```

### Build

```bash
# Only what changed since last commit
npx nx affected -t build

# Everything
npx nx run-many -t build
```

### Test

```bash
npx nx affected -t test
npx nx test client
npx nx test api
```

### Lint

```bash
npx nx affected -t lint
```

### Full verification (before committing)

```bash
npx nx affected -t lint test build
```

### Generate new code

```bash
# New Angular component
npx nx g @nx/angular:component my-component --project=client

# New Angular library
npx nx g @nx/angular:lib feature --directory=libs/my-feature/feature

# New NestJS library
npx nx g @nx/nest:lib feature-api --directory=libs/my-feature/feature-api

# New plain TS library
npx nx g @nx/js:lib utils --directory=libs/shared/utils
```

### Visualize the dependency graph

```bash
npx nx graph
```

### List all projects

```bash
npx nx show projects
```

## Import Aliases

Defined in `tsconfig.base.json`:

```typescript
import { /* types */ } from '@org/types';
import { /* components */ } from '@org/ui';
import { /* supabase client */ } from '@org/supabase-client';
```

## Deployment (Vercel)

Create **two** Vercel projects from the same repository.

### Project 1 — Frontend

| Setting | Value |
|---|---|
| Root Directory | `client` |
| Install Command | `cd .. && pnpm install` |
| Build Command | `cd .. && npx nx build client` |
| Output Directory | `../dist/client/browser` |

### Project 2 — Backend

| Setting | Value |
|---|---|
| Root Directory | `api` |
| Install Command | `cd .. && pnpm install` |
| Build Command | `cd .. && npx nx build api` |
| Output Directory | `../dist/api` |

Add environment variables for each project in the Vercel dashboard.

## Testing

| Scope | Runner | Command |
|---|---|---|
| Angular app | Vitest | `npx nx test client` |
| NestJS backend | Jest | `npx nx test api` |
| Shared TS libs | Jest | `npx nx test types` |
| E2E (frontend) | Playwright | `npx nx e2e client-e2e` |
| E2E (backend) | Playwright | `npx nx e2e api-e2e` |

## Conventions

- **TypeScript strict mode** is enabled workspace-wide.
- **Path aliases** use `@org/*` prefix (rename when you fork).
- **pnpm only** — do not use npm or yarn inside this workspace.
- **Never commit `.env` files** — they are gitignored.
- **Commit format** — `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`.

## License

MIT