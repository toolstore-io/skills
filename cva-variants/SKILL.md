---
name: cva-variants
description: class-variance-authority (cva) pattern for component variants. Use when the project uses cva for variant definitions. Skip if the project uses a different variant or styling strategy.
---

# CVA Variants

Visual variants are defined with `class-variance-authority` in a colocated `component.variants.ts` file. All classes must be design token classes (see `token-first-styling`).

## File structure

```ts
// badge.variants.ts
import { cva } from 'class-variance-authority';

export const badgeVariants = cva(
  'inline-flex w-fit items-center rounded-token-pill px-2 py-0.5 text-[11px] font-semibold tracking-wide border',
  {
    variants: {
      variant: {
        default: 'bg-brand-blue/10 text-brand-blue border-brand-blue/20',
        green:   'bg-brand-green/10 text-brand-green border-brand-green/20',
        red:     'bg-brand-red/10 text-brand-red border-brand-red/20',
        dim:     'bg-white/[0.04] text-fg-muted border-border-subtle',
      },
    },
    defaultVariants: { variant: 'default' },
  },
);

export type BadgeVariant = NonNullable<Parameters<typeof badgeVariants>[0]>['variant'];
```

## Exports

Always export two things from a variants file:
1. The `cva` function (e.g., `badgeVariants`)
2. The variant type (e.g., `BadgeVariant`) derived from the function's parameter type

The pattern `NonNullable<Parameters<typeof fn>[0]>['variant']` ensures the exported type stays in sync with the defined variants automatically — no need to maintain a separate type definition.

## Rules

- Every class must be a design token class. No arbitrary values, no hard-coded hex.
- Base classes (first argument to `cva`) apply to all variants. Put structural and layout classes here.
- Variant classes (inside `variants`) apply conditionally. Put color, border, and state classes here.
- Always define `defaultVariants` so the component works without an explicit `variant` prop.

## Usage in component

```svelte
<script lang="ts">
  import { badgeVariants, type BadgeVariant } from './badge.variants.js';

  type Props = { variant?: BadgeVariant; class?: string; };
  let { variant = 'default', class: className = '' }: Props = $props();
</script>

<span class={badgeVariants({ variant }) + (className ? ' ' + className : '')}>
  ...
</span>
```
