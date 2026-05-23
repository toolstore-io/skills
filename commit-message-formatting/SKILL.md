---
name: commit-message-formatting
description: Use whenever you are about to run `git commit`, or when reviewing or rewriting an existing commit message. Covers the conventional `type(scope): description` format, allowed types, imperative mood, the one-line-only rule, and the no-co-author-trailer policy.
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
