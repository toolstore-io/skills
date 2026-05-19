# Toolstore Skills

Coding practices, patterns, and workflow conventions for Toolstore LLC projects, packaged as agent skills following the [Agent Skills specification](https://agentskills.io/specification).

## Usage

Add as a git submodule to any project:

```sh
git submodule add ssh://git@toolstore.dev:2222/toolstore/skills.git skills
```

Point your agent runtime at the `skills/` directory. Load the `toolstore` skill first — it serves as the index and routes to the appropriate skill for your task.

## Structure

Each skill is a directory containing a `SKILL.md` file with frontmatter and instructions. Skills are organized with two sub-indexes for progressive disclosure:

- `toolstore/` — root index, load this first
- `backend/` — sub-index for backend and architecture skills
- `svelte/` — sub-index for Svelte frontend skills

## License

Copyright (c) 2026 Toolstore LLC. See [LICENSE](./LICENSE) for terms.
