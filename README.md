# hstay monorepo

This repository provides a single root workflow for the existing hstay backend and frontend projects while keeping each app in its own Git repository.

## Layout

- `apps/backend` – Git submodule for the NestJS backend
- `apps/frontend` – Git submodule for the Angular frontend
- `package.json` – root Turbo scripts
- `turbo.json` – task pipeline definition

## Clone and initialize

```bash
git clone <root-repo-url> hstay
cd hstay
git submodule update --init --recursive
npm install
```

> In this local setup the submodules currently point at local source repositories under `~/playground`. If you publish the root repo, update `.gitmodules` to your preferred remote URLs before sharing it.

## Install strategy

This monorepo uses npm workspaces plus Turbo:

- run `npm install` at the root to install workspace dependencies and Turbo
- backend and frontend keep their own `package.json` files and Git history
- the root lockfile becomes the main orchestration lockfile for the monorepo clone

```bash
npm install
```

## Root workflows

From the root:

```bash
npm run dev     # backend + frontend in parallel
npm run build   # build both apps
npm run test    # backend jest + frontend headless karma
npm run lint    # frontend lint only for now
npm run check   # frontend biome check only for now
```

## Submodule workflow

Work inside the app directories as usual:

```bash
cd apps/backend
# make changes

git status

git add .
git commit -m "Update backend"
git push
```

Then update the root repo to record the new submodule pointer:

```bash
cd ../..
git add apps/backend
git commit -m "Bump backend submodule"
```

## Update submodules

```bash
npm run submodule:update
```

Or manually:

```bash
git submodule update --init --recursive --remote
```

## Troubleshooting

### Detached HEAD in a submodule

If a submodule is detached, checkout the branch you want to work on:

```bash
cd apps/backend
git checkout release
```

### Commands fail from the root

Run `npm install` at the root to refresh workspace dependencies and Turbo.
