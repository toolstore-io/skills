---
name: service-layer
description: Service layer conventions. Services orchestrate gateways and repositories; they don't do HTTP or raw SQL. Use when building a new service or adding operations to an existing one.
---

# Service Layer

Services contain business logic. They orchestrate gateways and repositories but have no knowledge of HTTP, SQL, or external SDK details.

## Factory pattern

```ts
export function createSynapseService(signingKeyMasterKey: string, gateway: SynapseGateway) {
  async function registerUser(...): Promise<Result<...>> { ... }
  async function registerOrLoginUser(...): Promise<Result<...>> { ... }

  return { registerUser, registerOrLoginUser, createOrResolveRoom };
}

export type SynapseService = ReturnType<typeof createSynapseService>;
```

## Inner functions

Define operations as named inner functions, then expose them in the return object. Inner functions can call each other freely since they close over the same deps.

```ts
export function createInviteService(invites: InviteRepository, synapse: SynapseService) {
  async function acceptInvite(
    token: string,
    matrixLocalpart: string,
    password: string,
  ): Promise<Result<void, 'not_found' | 'db_error' | 'request_failed'>> {
    const inviteResult = await invites.findActiveByToken(token);
    if (inviteResult.isErr()) return err(inviteResult.error);
    if (!inviteResult.value) return err('not_found');

    const registerResult = await synapse.registerUser(serverName, tenantId, matrixLocalpart, password);
    if (registerResult.isErr()) return err(registerResult.error);

    return ok(undefined);
  }

  return { acceptInvite };
}
```

## What belongs in a service

- Business logic: validation, sequencing of operations, conditional branching
- Orchestrating multiple repository or gateway calls
- Transforming data between what repositories return and what callers need

## What does not belong in a service

- HTTP status codes or response formatting (belongs in route handlers)
- Raw SQL queries (belongs in repositories)
- External HTTP calls (belongs in gateways)

## Error propagation

Always propagate `result.error` directly, never re-wrap it. See the `neverthrow` skill for the full rule.

```ts
const result = await invites.findActiveByToken(token);
if (result.isErr()) return err(result.error);
```
