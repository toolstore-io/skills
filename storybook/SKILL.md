---
name: storybook
description: Storybook stories and visual regression testing conventions. Use when adding or reviewing any UI component to ensure all variants are documented and style regressions are caught.
---

# Storybook

Every UI primitive gets a Storybook story file. Stories serve as living documentation and as the basis for visual regression tests.

## Story file

Colocate the story file with the component. Name it `ComponentName.stories.tsx` (React) or `ComponentName.stories.svelte` (Svelte).

```tsx
// badge.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Badge } from './badge';

const meta: Meta<typeof Badge> = {
  component: Badge,
};
export default meta;

type Story = StoryObj<typeof Badge>;

export const Default: Story = {};

export const Green: Story = {
  args: { variant: 'green' },
};

export const Red: Story = {
  args: { variant: 'red' },
};
```

## Rules for stories

- Write one story per meaningful variant. If a component has four variants, it has (at least) four stories.
- Stories should render the component in isolation with no surrounding layout — the parent controls layout, not the story.
- Use `args` for variant configuration so Storybook controls work automatically.
- Do not write stories that require mocked data fetching or complex setup. If a component needs that, it is not a primitive.

## Visual regression tests

Visual regression tests catch unintended style changes — the kind that pass unit tests but break the UI. Run them in CI to prevent regressions from shipping.

Use Vitest with Storybook's test integration for visual regression. Colocate the visual test file as `ComponentName.visual.ts`.

```ts
// badge.visual.ts
import { test, expect } from 'vitest';
import { render } from '@storybook/test';
import * as stories from './badge.stories';

test('Badge/Default', async () => {
  const canvas = await render(stories.Default);
  await expect(canvas).toMatchSnapshot();
});

test('Badge/Green', async () => {
  const canvas = await render(stories.Green);
  await expect(canvas).toMatchSnapshot();
});
```

## When to update snapshots

Update snapshots intentionally, never automatically. When a visual change is deliberate, review the diff, confirm it matches the intent, then commit the updated snapshot alongside the code change.

Never commit snapshot updates without reviewing them — a snapshot update that hides a regression is worse than no snapshot at all.
