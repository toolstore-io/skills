---
name: pre-push-checklist
description: Steps to verify locally before pushing a branch. Use before running git push.
---

# Pre-Push Checklist

## 1. Types pass

```sh
pnpm typecheck
```

Fix type errors before pushing. Do not suppress errors with `@ts-ignore` or `as unknown as T` casts to make them pass.

## 2. Tests pass

```sh
pnpm test
```

Run tests if you changed code that has test coverage. If tests are failing for reasons unrelated to your change, flag the pre-existing failure rather than holding your push.

## 3. Linting and formatting pass

A pre-commit hook runs automatically on staged files before each commit. If you are unsure whether it ran cleanly:

```sh
pnpm biome check .
```

Never bypass the hook with `--no-verify`.

## 4. Commits are clean

```sh
git log origin/main..HEAD --oneline
```

Verify that:
- Each commit message follows conventional commit format
- No commit has a body or co-author trailer
- Commits are grouped by logical boundary, not one per file

## 5. Branch is up to date with main

If main has moved since you branched, rebase before pushing:

```sh
git fetch origin
git rebase origin/main
```

## 6. Never push directly to main

All changes go through branches and pull requests. Main is protected.
