---
name: bits-ui-composition
description: Wrapping Bits UI primitives for interactive components. Use when the project uses Bits UI. Skip if the project uses a different component library or plain HTML elements.
---

# Bits UI Composition

Bits UI provides accessible, headless interactive primitives. Wrap them rather than reimplementing keyboard behavior, focus management, or ARIA attributes from scratch.

## When to use Bits UI

Use for: dialogs, dropdown menus, context menus, toggle buttons, switches, popovers, tooltips, tab panels, checkboxes, radio groups.

Do not use for static elements (badges, avatars, dividers) that require no interaction behavior.

## Composition pattern

Wrap Bits UI components in your own component. Apply token classes via the `class` prop. Use snippet-based composition rather than re-exposing every Bits UI prop.

```svelte
<script lang="ts">
  import { Dialog } from 'bits-ui';
  import type { Snippet } from 'svelte';

  type Props = {
    open?: boolean;
    onOpenChange?: (open: boolean) => void;
    trigger: Snippet;
    children: Snippet;
  };

  let { open = $bindable(false), onOpenChange, trigger, children }: Props = $props();
</script>

<Dialog.Root bind:open {onOpenChange}>
  <Dialog.Trigger>
    {@render trigger()}
  </Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay class="fixed inset-0 bg-black/50" />
    <Dialog.Content class="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 ...">
      {@render children()}
    </Dialog.Content>
  </Dialog.Portal>
</Dialog.Root>
```

## Key rules

- Apply all visual styling via token classes on Bits UI's `class` prop. Never override styles with global CSS targeting Bits UI internals.
- Prefer snippet composition over prop drilling. A `title` snippet is more flexible than a `title` string prop.
- Prefer `bind:open` with `onOpenChange` over managing open state internally — callers often need to observe or control it.
- Do not wrap Bits UI more than one level deep without a clear reason.
