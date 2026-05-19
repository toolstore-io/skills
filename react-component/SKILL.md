---
name: react-component
description: React component structure, naming, and file organization conventions. Use when creating or reorganizing React components. Skip if the project uses Svelte or another framework.
---

# React Component Structure

## Directory structure

Each primitive gets its own subdirectory:

```
components/ui/badge/
├── badge.tsx              # component implementation
├── badge.variants.ts      # cva variant definitions
├── index.ts               # re-exports
├── badge.test.tsx         # colocated unit tests (optional)
├── badge.stories.tsx      # Storybook stories (optional)
└── badge.visual.ts        # visual regression tests (optional)
```

## Component file

Props are defined as a TypeScript type above the component. Accept a `className` prop for caller overrides. Use `cn()` to merge class names.

```tsx
import { badgeVariants, type BadgeVariant } from './badge.variants';
import { cn } from '@/lib/utils';

type BadgeProps = {
  variant?: BadgeVariant;
  className?: string;
  children?: React.ReactNode;
};

export function Badge({ variant = 'default', className, children }: BadgeProps) {
  return (
    <span className={cn(badgeVariants({ variant }), className)}>
      {children}
    </span>
  );
}
```

## index.ts

Re-export the component and any public types consumers need.

```ts
export { Badge } from './badge';
export type { BadgeVariant } from './badge.variants';
```

## Naming conventions

- Component files and directories: `kebab-case` (e.g., `badge.tsx`, `badge/`)
- Component function: `PascalCase` matching the component name (e.g., `Badge`)
- Props type: `ComponentNameProps` (e.g., `BadgeProps`)

## No layout coupling

Components never set their own margin, external padding, or grid/flex context. The parent controls layout; the component controls only its own visual appearance. The `className` prop allows callers to apply layout from outside.

```tsx
// wrong: component sets its own top margin
<span className="mt-4 inline-flex ...">

// correct: no external spacing
<span className="inline-flex ...">
```
