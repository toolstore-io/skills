---
name: pnpm-workspaces
description: pnpm monorepo conventions. Use when adding dependencies, importing across packages, or running workspace commands.
---

# pnpm Workspaces

## Adding dependencies

Always use `pnpm add` from the package directory or with `--filter`. Never edit `package.json` version fields directly.

```sh
# runtime dependency in a specific package
cd packages/api && pnpm add zod

# dev dependency
cd packages/web && pnpm add -D @testing-library/svelte

# from the repo root
pnpm add --filter @superteleport/api zod
```

## Cross-package imports

Workspace packages are referenced by their scoped package name. The consuming package must list the dependency in its own `package.json` before importing.

```ts
import { createInviteRepository } from '@superteleport/shared';
```

## Package manager enforcement

pnpm is enforced via `packageManager` in the root `package.json`. Never use npm or yarn — they will produce incorrect lockfiles.

## Running commands

```sh
# one package
pnpm --filter @superteleport/web dev

# all packages
pnpm -r build

# root-level scripts
pnpm typecheck
pnpm test
```
