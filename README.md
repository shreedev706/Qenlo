# Qenlo Monorepo

> Learn. Build. Solve.

Qenlo is a technology learning platform — tutorials, installs, and troubleshooting guides for AI, Robotics & IoT, Software, Games, Windows, and Linux. This repo is a **Turborepo + pnpm workspace monorepo** containing every app and shared package that makes up the Qenlo product.

If you're new here, read this file top to bottom before touching code — it tells you exactly where to work and what not to duplicate.

---

## Quick start

```bash
pnpm install       # installs deps for every app/package
pnpm dev            # runs all apps in dev mode via Turborepo
pnpm dev --filter=web     # run only the main website
pnpm dev --filter=api     # run only the backend
pnpm build          # builds everything
pnpm lint            # lints everything
```

Turborepo caches tasks and only rebuilds what changed, so prefer `pnpm dev --filter=<app>` over running every app at once when you're only working on one.

---

## Folder structure

```
Qenlo/
├── apps/          → deployable applications (frontend + backend)
├── packages/      → shared code used across apps
├── infra/         → infrastructure / deployment config
├── scripts/       → repo-wide helper scripts
├── tests/         → cross-app / integration tests
├── tooling/        → shared dev tooling config
├── turbo.json     → Turborepo pipeline config
└── pnpm-workspace.yaml → defines which folders are workspaces
```

---

## `apps/` — things that get deployed

Each folder here is its own runnable app with its own `package.json`. If you're building a *feature*, it almost always lives in one of these.

| App | What it is | Stack |
|---|---|---|
| **`apps/web`** | The main public Qenlo website — homepage, tutorials, category pages (AI, Robotics, Software, Games, Windows, Linux) | Next.js, Tailwind |
| **`apps/admin`** | Internal admin dashboard — content management, moderation, user management | Next.js |
| **`apps/api`** | Backend server — REST endpoints, business logic, talks to the database | Express.js |
| **`apps/developer`** | Developer portal — for third-party developers submitting/listing their apps (future marketplace feature) | Next.js |
| **`apps/docs`** | Documentation site | Next.js |
| **`apps/status`** | Public status/uptime page | Next.js |

**Rule of thumb:** if you're changing something a *user* sees on qenlo.tech, you're in `apps/web`. If you're changing server logic, routes, or data handling, you're in `apps/api`. If it's internal-only tooling for managing the site, it's `apps/admin`.

---

## `packages/` — shared code, imported by apps

Nothing in here runs on its own — these are libraries consumed by the apps above. **If you find yourself copy-pasting a component, type, or utility function between two apps, it belongs in here instead.**

| Package | Purpose |
|---|---|
| **`ui`** | Shared component library (`Button`, `Card`, `Code`, etc.) — used by `web`, `admin`, `developer` so UI stays consistent |
| **`auth`** | Authentication logic (shared between `api` and any app needing session/user info) |
| **`database`** | Database client, schema, and query logic |
| **`email`** | Email sending logic (transactional emails, newsletter, etc.) |
| **`storage`** | File/media storage handling |
| **`search`** | Search functionality shared across apps |
| **`analytics`** | Analytics/tracking helpers |
| **`core`** | Core shared business logic |
| **`sdk`** | SDK for interacting with the Qenlo API from other apps |
| **`utils`** | General-purpose utility functions |
| **`validation`** | Shared validation schemas (form inputs, API payloads) |
| **`constants`** | Shared constant values (enums, config values) used across apps |
| **`types`** | Shared TypeScript types/interfaces |
| **`env`** | Environment variable handling/validation |
| **`logger`** | Shared logging utility |
| **`eslint-config`** | Shared ESLint rules — every app extends this instead of defining its own |
| **`typescript-config`** | Shared `tsconfig.json` base configs — every app extends `base.json`, `nextjs.json`, or `react-library.json` from here |

---

## Root-level folders

| Folder | Purpose |
|---|---|
| **`infra/`** | Infrastructure and deployment configuration (hosting, CI/CD environment setup) |
| **`scripts/`** | One-off or repo-wide helper scripts (not part of any single app) |
| **`tests/`** | Cross-app or integration tests that don't belong to a single app |
| **`tooling/`** | Additional shared dev tooling/config not covered by `eslint-config` or `typescript-config` |

---

## Where do I put my code?

A quick decision guide:

- **Building a new page on the main site?** → `apps/web/src/app`
- **Building an admin feature?** → `apps/admin`
- **Adding an API endpoint?** → `apps/api`
- **Building a reusable button/card/UI piece?** → `packages/ui`
- **Writing a function two+ apps will need?** → the relevant `packages/*` folder (or `packages/utils` if it's general-purpose)
- **Adding a new content category (e.g. a new Games subcategory page)?** → `apps/web`, following the routing pattern already used for existing categories
- **Changing lint/TS rules for the whole repo?** → `packages/eslint-config` or `packages/typescript-config`

---

## Conventions

- **Package manager:** `pnpm` only — do not use `npm` or `yarn`, it will break the lockfile and workspace resolution.
- **Adding a dependency to a specific app/package:**
  ```bash
  pnpm add <package> --filter=web
  ```
- **Adding a shared dependency to the root:**
  ```bash
  pnpm add -w <package>
  ```
- Before opening a PR, run `pnpm lint` and `pnpm build` locally — Turborepo's cache makes this fast after the first run.

---

## Notes

- There's a legacy `assets/` folder at the repo root (static HTML/CSS/JS) left over from an earlier prototype, pre-dating the monorepo setup. It's not part of the active app structure — confirm with the team whether it should be migrated into `apps/web/public` or removed.
- New to the team? Start by running `apps/web` locally and reading through `packages/ui` — most day-to-day frontend work touches both.