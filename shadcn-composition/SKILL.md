---
name: shadcn-composition
description: Using shadcn/ui components as building blocks for interactive primitives in React. Use when building dialogs, menus, toggles, popovers, tabs, or other interactive UI in a React project. Skip if the project does not use shadcn/ui.
---

# shadcn/ui Composition

shadcn/ui provides accessible, Radix UI-based interactive primitives styled with Tailwind and cva. Components are copied into your codebase — you own the code and can customize freely.

## When to use shadcn/ui

Use for: dialogs, dropdown menus, context menus, popovers, tooltips, tabs, accordions, toggles, checkboxes, radio groups, selects, sheets.

Do not reimplement keyboard behavior, focus management, or ARIA attributes for these patterns. Use shadcn/ui instead.

## Adding components

```sh
npx shadcn-ui add button
npx shadcn-ui add dialog
```

Components are added to `components/ui/`. They are yours to modify.

## Composition pattern

Build higher-level components by composing shadcn primitives. Apply additional token classes via `className`. Use the `cn()` helper to merge classes safely.

```tsx
import { Dialog, DialogContent, DialogTrigger } from '@/components/ui/dialog';
import { cn } from '@/lib/utils';

type ConfirmDialogProps = {
  trigger: React.ReactNode;
  children: React.ReactNode;
  className?: string;
};

export function ConfirmDialog({ trigger, children, className }: ConfirmDialogProps) {
  return (
    <Dialog>
      <DialogTrigger asChild>{trigger}</DialogTrigger>
      <DialogContent className={cn('max-w-md', className)}>
        {children}
      </DialogContent>
    </Dialog>
  );
}
```

## Key rules

- Apply all additional styling via token classes and `cn()`. Do not override shadcn styles with global CSS targeting component internals.
- Use `asChild` on trigger elements to avoid wrapping in an extra DOM node.
- Prefer composition and children over re-exposing every shadcn prop. Expose only what callers genuinely need to control.
- Do not wrap shadcn components more than one level deep without a clear reason.
- Customize the copied shadcn source directly when a structural change is needed — don't fight it from the outside.
