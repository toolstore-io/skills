---
name: repository-pattern
description: Database repository module conventions using pg and neverthrow. Use when adding or modifying a database module in packages/shared/src/db/.
---

# Repository Pattern

Repositories live in `packages/shared/src/db/`. Each file encapsulates all SQL for one domain entity.

## Factory signature

```ts
export function createInviteRepository(sql: Sql, logger: Logger) {
  // inner functions...
  return { create, findActiveByToken, revoke, listActive };
}

export type InviteRepository = ReturnType<typeof createInviteRepository>;
```

Always export the `type *Repository = ReturnType<...>` alias. This is what services and route factories import.

## Return types

Every method returns `ResultAsync<T, 'db_error'>` using `ResultAsync.fromPromise`. Never throw; never return raw promises.

```ts
function create(tenantId: string, createdBy: string, token: string): ResultAsync<Invite, 'db_error'> {
  return ResultAsync.fromPromise(
    sql<Invite[]>`
      INSERT INTO invites (tenant_id, created_by, token)
      VALUES (${tenantId}, ${createdBy}, ${token})
      RETURNING *
    `.then((rows) => rows[0]!),
    (e) => {
      logger.error('failed to create invite', { cause: e, tenantId });
      return 'db_error' as const;
    },
  );
}
```

## Nullable results

For lookups that may return nothing, use `T | null` in the success type and resolve with `?? null`.

```ts
function findActiveByToken(token: string): ResultAsync<InviteWithTenant | null, 'db_error'> {
  return ResultAsync.fromPromise(
    sql<InviteWithTenant[]>`
      SELECT ... WHERE token = ${token} LIMIT 1
    `.then((rows) => rows[0] ?? null),
    (e) => {
      logger.error('failed to find active invite by token', { cause: e });
      return 'db_error' as const;
    },
  );
}
```

## Row type definitions

Define row shape interfaces at the top of the file and export them. Composed types (joining two tables) get their own interface extending the base.

```ts
export interface Invite { id: string; tenant_id: string; token: string; revoked_at: Date | null; created_at: Date; }
export interface InviteWithTenant extends Invite { subdomain: string; server_name: string; }
```

## Logging

Log the raw error cause and relevant identifiers (tenantId, inviteId, etc.) inside the `fromPromise` error callback before returning `'db_error' as const`. This is the only place the raw error is observable.
