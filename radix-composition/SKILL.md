---
name: radix-composition
description: Using Radix UI primitives for interactive components in React. Use when building dialogs, menus, toggles, popovers, tabs, or other interactive UI. Skip if the project does not use Radix UI.
---

# Radix UI Composition

Radix UI provides unstyled, accessible interactive primitives. You own all the styling via token classes and cva. This keeps the approach consistent with Bits UI in Svelte — headless primitives, styled entirely through design tokens.

## When to use Radix UI

Use for: dialogs, dropdown menus, context menus, popovers, tooltips, tabs, accordions, toggles, checkboxes, radio groups, selects.

Do not reimplement keyboard behavior, focus management, or ARIA attributes for these patterns. Use Radix UI instead.

## Composition pattern

Wrap Radix primitives in your own component. Apply all styling via token classes. Define variants in a `*.variants.ts` file when multiple visual states exist.

```tsx
import * as Dialog from '@radix-ui/react-dialog';
import { cn } from '@/lib/utils';
import type { ReactNode } from 'react';

type ConfirmDialogProps = {
  trigger: ReactNode;
  children: ReactNode;
  className?: string;
};

export function ConfirmDialog({ trigger, children, className }: ConfirmDialogProps) {
  return (
    <Dialog.Root>
      <Dialog.Trigger asChild>{trigger}</Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay className="fixed inset-0 bg-black/50" />
        <Dialog.Content className={cn(
          'fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-surface-base rounded-token-md p-6',
          className,
        )}>
          {children}
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

## Key rules

- Apply all styling via token classes on Radix's `className` prop. Radix components are unstyled by default — this is intentional.
- Use `asChild` on trigger elements to avoid an extra DOM wrapper.
- Prefer children/composition over re-exposing every Radix prop. Expose only what callers genuinely need to control.
- Do not wrap Radix more than one level deep without a clear reason.
- If a visual variant is foreseeable, define it in a `*.variants.ts` file with cva rather than accepting a freeform `className` at the primitive level.
