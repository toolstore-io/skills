---
name: commit-message-formatting
description: Conventional commit message format. Use when writing or reviewing commit messages.
---

# Commit Message Formatting

## Format

```
type(scope): description
```

- **type**: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`
- **scope**: optional; useful in a monorepo to indicate which package changed
- **description**: imperative mood, lowercase, no trailing period

## Rules

- One line only. No body, no footer, no co-author trailers.
- Group commits by functional or logical boundary, not one commit per file changed.

## Examples

```
feat(api): add email invite endpoint
fix(web): correct badge border radius token
refactor(shared): extract invite repository from provisioning module
chore: update pnpm lockfile
docs: add ADR for audit log schema
test(api): add invite acceptance integration tests
```

## Common mistakes

```
# wrong: no type
add email invite endpoint

# wrong: past tense
feat(api): added email invite endpoint

# wrong: has a body
feat(api): add email invite endpoint

This endpoint accepts email invites.

# wrong: co-author trailer
feat(api): add email invite endpoint

Co-Authored-By: Someone <someone@example.com>
```
