---
name: token-first-styling
description: Use when applying styles to any UI component or reviewing whether existing styles are token-compliant — picking class names, suspicious hex/rgb/hsl values, arbitrary Tailwind values like `bg-[#...]`, or anywhere a primitive sets its own margin. Covers the token-class-only rule, opacity modifiers, and the no-self-layout-in-primitives rule.
---

# Token-First Styling

## When this fires

You are typing or reviewing a `class=`/`className=` value on a UI element. If you find yourself reaching for `text-[#...]`, `bg-[rgba(...)]`, an arbitrary Tailwind value in square brackets, or a margin/padding class on a primitive's outermost element, stop and use a token class or push the layout to the parent.

## Core rule

All visual properties are expressed through design token classes. Raw values never appear in component markup.

## The rule

Never use:
- Hard-coded hex, rgb, or hsl values: `text-[#3b82f6]`, `bg-[rgba(0,0,0,0.5)]`
- Arbitrary color syntax: `bg-[#ff0000]`

Always use:
- Named token classes from the design system: `text-brand-blue`, `bg-surface-base`, `text-fg-muted`
- Opacity modifiers on token colors: `bg-brand-blue/10`, `border-brand-blue/20`

```svelte
<!-- wrong -->
<span class="text-[#3b82f6] bg-[rgba(59,130,246,0.1)]">

<!-- correct -->
<span class="text-brand-blue bg-brand-blue/10">
```

## No layout coupling in primitives

Primitive components never set external spacing. No `margin`, `mt-*`, `mb-*`, `mx-*` on the outermost element. The parent controls layout.

```svelte
<!-- wrong: primitive sets its own top margin -->
<button class="mt-4 px-4 py-2 ...">

<!-- correct: no external spacing -->
<button class="px-4 py-2 ...">
```

## Class override pattern

Accept a `class` prop and append it so callers can apply layout from outside.

```svelte
let { class: className = '' }: Props = $props();

<span class={componentVariants({ variant }) + (className ? ' ' + className : '')}>
```
