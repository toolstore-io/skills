---
name: testing
description: Testing conventions and taste. Use when writing or reviewing tests. Covers colocation, what to test, what not to mock, and assertion style for both backend and frontend code.
---

# Testing

## Colocation

Test files live next to the source file they test. Never put tests in a separate `__tests__` directory.

```
components/ui/badge/
├── Badge.svelte
├── Badge.test.ts      ✓ colocated
└── badge.variants.ts

services/
├── invite.ts
└── invite.test.ts     ✓ colocated
```

## Test behavior, not implementation

Test what a unit does from the outside, not how it does it internally. If a refactor that preserves behavior breaks a test, the test is wrong.

```ts
// wrong — tests internal state
expect(component.state.isOpen).toBe(true);

// correct — tests observable behavior
expect(screen.getByRole('dialog')).toBeInTheDocument();
```

## Frontend: query by role and label

Use accessible queries. Avoid querying by class name, test ID, or DOM structure.

```ts
// prefer
screen.getByRole('button', { name: 'Submit' });
screen.getByLabelText('Email address');
screen.getByText('Saved');

// avoid
container.querySelector('.submit-btn');
screen.getByTestId('submit-button');
```

## Frontend: assert on behavior, not classes

```ts
// avoid — brittle, tests styling not behavior
expect(button).toHaveClass('btn-primary');

// prefer — tests what the user experiences
expect(button).toBeEnabled();
expect(button).toHaveAttribute('aria-pressed', 'true');
```

## Backend: don't mock the database

Repository and service integration tests should hit a real database. Mock/prod divergence in DB tests is a real failure mode — a mocked test that passes while the real migration is broken provides false confidence.

Mock at the gateway boundary instead: mock external HTTP APIs and third-party SDKs, not internal infrastructure.

```ts
// correct — mock the external gateway, test real DB path
const gateway = createSynapseMock();
const service = createInviteService(invites, gateway); // invites hits real DB

// wrong — mocking the DB hides real integration failures
const invites = createInviteMock();
```

## Minimal mocking

Only mock what you cannot control: external HTTP calls, time, randomness. Do not mock modules just to avoid side effects — restructure the code instead.

```ts
// acceptable — mocking an external HTTP dependency
vi.mock('../gateways/synapse.js', () => ({ createSynapseGateway: () => createSynapseMock() }));

// avoid — mocking internal utilities to avoid a side effect
vi.mock('../utils/token.js');
```

## Async assertions

Use `waitFor` for async state changes. Do not use arbitrary `setTimeout` or `sleep`.

```ts
await userEvent.click(submitButton);
await waitFor(() => expect(screen.getByText('Saved')).toBeInTheDocument());
```

## Test file naming

| File type | Convention |
|---|---|
| Unit / integration test | `name.test.ts` |
| Svelte component test | `Name.test.ts` |
| Visual regression test | `Name.visual.ts` |
| Mock factory | `name.mock.ts` |
