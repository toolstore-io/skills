---
name: biome
description: Use when running, configuring, or suppressing the linter/formatter in a project that uses Biome. Triggers on "lint", "format", `biome check`, lint errors, or deciding whether to add a `biome-ignore` comment. Skip for ESLint or Prettier projects.
---

# Biome

Biome is the unified formatter and linter. A single `biome.json` at the repo root covers all packages.

## Formatter settings

- Indentation: 2 spaces
- Line width: 100 characters
- Quotes: single
- Semicolons: always
- Trailing commas: all

## Running Biome

```sh
# check and auto-fix all files
pnpm biome check --write .

# check without fixing
pnpm biome check .

# format only
pnpm biome format --write .
```

## Suppression comments

Avoid per-line suppression (`// biome-ignore`) except for genuinely unavoidable cases. When one is necessary, include a reason:

```ts
// biome-ignore lint/suspicious/noExplicitAny: external SDK type is untyped
const payload: any = sdk.getRawPayload();
```

If a rule fires repeatedly on a pattern, reconsider the pattern rather than suppressing the rule.
