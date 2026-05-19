---
name: primitive-extraction
description: When and how to extract UI primitive components. Use when building UI to decide whether to inline styles or extract a reusable primitive component.
---

# Primitive Extraction

Extract UI primitives earlier than feels necessary. The cost of a premature extraction is low; the cost of retrofitting a primitive out of tangled one-off markup is high.

## The threshold

Extract a primitive when any of the following is true:

- You have used the same visual pattern twice anywhere in the codebase
- You are about to use a pattern for the first time but it is clearly general (a status badge, a card shell, an icon button)
- The inline markup is making the parent component harder to read

A primitive with a single current usage is fine. Clarity at the callsite is reason enough.

## What a primitive is

A primitive is a small, focused, unstyled-to-the-outside component that owns its own visual appearance and nothing else. It has no opinion about layout, spacing relative to siblings, or the data it renders.

Good candidates: badges, tags, avatars, cards, dividers, icon buttons, empty states, loading spinners, tooltips.

Poor candidates: full page sections, data-fetching containers, anything that manages its own state beyond basic open/closed.

## How to extract

1. Identify the repeated or generalizable visual pattern.
2. Create a subdirectory for the primitive under `components/ui/`.
3. Move the markup into a component file, accepting a `class`/`className` prop for caller overrides.
4. Define variants in a `*.variants.ts` file if more than one visual variant exists or is foreseeable.
5. Export from `index.ts`.
6. Replace all existing usages with the new component.

## What not to extract

Do not extract for the sake of DRY alone. A one-line `<span className="...">` that appears in one place and has no variant needs does not need to be a component. The extraction should make the callsite clearer, not just shorter.
