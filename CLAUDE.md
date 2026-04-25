# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

hstay is a Turborepo monorepo using npm workspaces. The backend and frontend live in Git submodules under `apps/`. Each submodule has its own Git history and CLAUDE.md with app-specific details — read those before working in either app.

## Common Commands

```bash
# Install (root — sets up workspaces + Turbo)
npm install

# Development (runs both apps in parallel)
npm run dev

# Build both apps
npm run build

# Tests (backend Jest + frontend headless Karma)
npm run test

# Frontend-only lint/check (Biome)
npm run lint
npm run check

# Update submodules to latest remote
npm run submodule:update
```

### Backend (from `apps/backend/`)
```bash
npm run start:dev          # NestJS watch mode
npm run test               # Jest unit tests
npm run test:e2e           # End-to-end tests
npm run test -- --testPathPattern=<pattern>  # Run a single test file
npm run openapi:gen        # Regenerate OpenAPI spec
```

### Frontend (from `apps/frontend/`)
```bash
ng serve                   # Main app dev server
ng serve guest --port 4201 # Guest app dev server
ng test --watch=false --browsers=ChromeHeadless  # Headless tests
npx biome check .          # Lint + format check
npx biome check --write .  # Auto-fix
```

## Architecture

### Monorepo Structure
- **Root** — Turborepo orchestration only (`turbo.json` defines task pipeline; `package.json` has workspace scripts). No application code lives here.
- **`apps/backend`** — NestJS 11 backend (Git submodule). Contains three Nest apps (`main`, `super-admin`, `cli`) and shared libs (`email`, `firebase`, `mongo`, `cache`, `queue`, `storage`, `otp`, `identity`, `ocr`, `document-extraction`, `aadhaar-verification`). Uses Typegoose for MongoDB, Firebase Admin for auth/push, Passport for auth strategies.
- **`apps/frontend`** — Angular 20 workspace (Git submodule). Contains projects: `unified` (main app), `main`, `guest`, `website`, `shared-ui` (component library), `shared-types`. Uses Tailwind CSS v4, Biome for linting/formatting, ng-primitives, TanStack (forms/tables).

### Key Patterns
- **Submodule workflow**: Make commits inside `apps/backend` or `apps/frontend`, then bump the submodule pointer in the root repo with a separate commit.
- **Backend shared libs** (`apps/backend/libs/`): Reusable NestJS modules. Always prefer importing from these over duplicating logic.
- **Frontend shared-ui** (`apps/frontend/projects/shared-ui/`): Theme-agnostic component library. It defines component styles using CSS variables but does NOT define theme values — each consuming app provides its own theme variables.
- **Backend validation**: Global `ValidationPipe` with `whitelist: true` and `forbidNonWhitelisted: true`. Always use class-validator DTOs.
- **Backend API prefix**: All routes are under `/api`.

## Tech Stack Summary
- **Backend**: TypeScript, NestJS 11, Typegoose/MongoDB, Firebase Admin, BullMQ/Cloud Tasks, Redis, Passport
- **Frontend**: TypeScript, Angular 20, Tailwind CSS v4, Biome, Valibot, Firebase JS SDK
- **Tooling**: Turborepo, npm workspaces, Jest (backend), Karma/Jasmine (frontend)
- **Node requirement**: `>=24 <25` (backend)
