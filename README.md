# Toolstore Skills

Coding practices, patterns, and workflow conventions for Toolstore LLC projects, packaged as agent skills following the [Agent Skills specification](https://agentskills.io/specification).

## Usage

Most projects consume this repo as a git submodule under `.agents/skills/shared/`, alongside a sibling `.agents/skills/local/` directory for project-specific skills:

```sh
git submodule add ssh://git@toolstore.dev:2222/toolstore/skills.git .agents/skills/shared
mkdir -p .agents/skills/local
```

Point your agent runtime at `.agents/skills/`. Both `shared/` and `local/` are scanned; nothing else is required.

## Structure

The repo is flat — every skill is a top-level directory containing a single `SKILL.md`. There is no index file and no "load this first" entry point. Skills are discovered by the runtime through their `description:` frontmatter, which encodes the trigger conditions for that skill.

```
adr-process/SKILL.md
biome/SKILL.md
code-style/SKILL.md
...
```

Each skill is focused on one pattern or convention and stands on its own.

## Categories (for humans)

The categories below are documentation for the maintainer; the agent runtime does not use them.

### General

- `code-style` — verbosity, simplicity, params objects, comments
- `writing-style` — prose style for human-readable output

### Backend / Architecture

- `gateway-pattern` — wrapping external APIs and SDKs
- `repository-pattern` — database access modules
- `service-layer` — business logic orchestration
- `dependency-injection` — factory-based DI
- `hono-route` — Hono HTTP route handlers
- `neverthrow` — Result-based error handling
- `pino-logging` — structured logging

### Frontend (shared)

- `token-first-styling` — design-token-only styling rule
- `cva-variants` — class-variance-authority variants
- `primitive-extraction` — when and how to extract reusable primitives
- `storybook` — stories and visual regression tests

### Frontend (Svelte)

- `svelte-component` — component structure and naming
- `bits-ui-composition` — wrapping Bits UI primitives

### Frontend (React)

- `react-component` — component structure and naming
- `radix-composition` — wrapping Radix UI primitives

### Tooling / Workflow

- `testing` — test conventions for backend and frontend
- `pnpm-workspaces` — monorepo conventions
- `biome` — linter/formatter conventions
- `commit-message-formatting` — conventional commits
- `pre-push-checklist` — pre-push verification
- `pr-handling` — pull request conventions
- `forgejo` — Forgejo CLI specifics
- `multica` — Multica CLI specifics
- `adr-process` — Architecture Decision Records

## Project-local skills

A project can add its own skills in `.agents/skills/local/`. Local skills follow the same `SKILL.md` format. Prefix the directory name with the project slug (e.g. `superteleport-billing-rules/`) to keep local-vs-shared visible at a glance and avoid name collisions with this repo.

For project-wide invariants that should be in front of the agent on every turn (stack, layout, key conventions), use the project's `CLAUDE.md` instead — `CLAUDE.md` is loaded unconditionally, skills are pulled on description match.

## License

Copyright (c) 2026 Toolstore LLC. See [LICENSE](./LICENSE) for terms.
