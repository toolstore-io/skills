---
name: adr-process
description: When and how to write an Architecture Decision Record (ADR). Use when making a meaningful architectural choice during implementation.
---

# ADR Process

ADRs capture significant decisions so future contributors understand why the codebase is shaped the way it is. They live in `docs/adr/`.

## When to write an ADR

Write one when your change involves any of:
- Adding a new runtime dependency
- Establishing a new code pattern that will be followed elsewhere
- Making a structural change to a package (moving, splitting, merging)
- Choosing between two or more meaningful approaches

Propose the ADR before or alongside the implementation, not after. If the decision is non-trivial, flag it to the user before writing it.

## Format

Use the next available number and follow the template in `docs/adr/000-template.md` if it exists, otherwise use this structure:

```markdown
# ADR-NNN: Short title

**Date:** YYYY-MM-DD
**Status:** Accepted

## Context

What problem or situation led to this decision?
What options were considered?

## Decision

What was decided, and how should it be applied?

## Consequences

What are the trade-offs? What is now easier or harder?
```

## Immutability rule

ADRs are immutable once written. If a decision is reversed:

1. Write a new ADR with the updated decision, marked "Supersedes ADR-NNN"
2. Update the old ADR's status line to "Superseded by ADR-NNN"
3. Never edit the original decision text

## Registering the ADR

Add a row to `docs/adr/README.md` for every new ADR if that index file exists.
