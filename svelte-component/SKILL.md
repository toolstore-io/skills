---
name: svelte-component
description: Svelte 5 component structure, naming, and file organization. Use when creating a new UI component or migrating an existing flat-file component to the current conventions.
---

# Svelte Component Structure

## Directory structure

Each primitive gets its own subdirectory:

```
components/ui/badge/
├── Badge.svelte           # component implementation
├── badge.variants.ts      # cva variant definitions
├── index.ts               # re-exports
├── Badge.test.ts          # colocated unit tests (optional)
├── Badge.stories.svelte   # Storybook stories (optional)
└── Badge.visual.ts        # Playwright visual tests (optional)
```

## Component file

Use Svelte 5 runes. Define a `Props` type inline. Accept an optional `class` prop for caller overrides.

```svelte
<script lang="ts">
  import type { Snippet } from 'svelte';
  import { badgeVariants, type BadgeVariant } from './badge.variants.js';

  type Props = {
    variant?: BadgeVariant;
    class?: string;
    children?: Snippet;
  };

  let { variant = 'default', class: className = '', children }: Props = $props();
</script>

<span class={badgeVariants({ variant }) + (className ? ' ' + className : '')}>
  {@render children?.()}
</span>
```

## index.ts

Re-export the component and any public types consumers need.

```ts
export { default as Badge } from './Badge.svelte';
export type { BadgeVariant } from './badge.variants.js';
```

## Naming conventions

- `.svelte` files use `PascalCase` — Svelte treats capitalised tags as components, so `Badge.svelte` lets you write `<Badge />` in templates.
- `.ts` files use `kebab-case` — variants, tests, and stories follow standard TypeScript file naming.
- The directory name matches the component's lowercase name: `badge/`.

## No layout coupling

Components never set their own margin, external padding, or grid/flex context. The parent controls layout; the component controls only its own visual appearance.

```svelte
<!-- wrong: component sets its own top margin -->
<span class="mt-4 inline-flex ...">

<!-- correct: no external spacing -->
<span class="inline-flex ...">
```
