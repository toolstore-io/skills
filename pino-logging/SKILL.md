---
name: pino-logging
description: Use when adding, reviewing, or removing logging in a project that uses Pino — anywhere `logger.error`, `logger.info`, `logger.warn`, `logger.debug` appears, or whenever a raw error is first caught. Covers logger-as-injected-dependency, the structured-context-object form, when to use each level, and the log-once-at-the-boundary rule. Skip for other loggers.
---

# Pino Logging

## Logger as a dependency

Logger is always injected as a dependency, never imported directly. This keeps logging testable and the dependency chain explicit.

```ts
export function createInviteRepository(sql: Sql, logger: Logger) { ... }
export function createSynapseGateway(logger: Logger) { ... }
```

## Structured logging

Always pass context as a second argument object. Never use string interpolation to embed values.

```ts
// correct
logger.error('failed to create invite', { cause: e, tenantId });

// wrong
logger.error(`failed to create invite for tenant ${tenantId}: ${e.message}`);
```

## Log at the right level

| Level | When to use |
|---|---|
| `error` | An operation failed in a way that requires attention |
| `warn` | Something unexpected happened but the operation recovered |
| `info` | A significant event occurred (startup, provisioning complete) |
| `debug` | Development detail, not emitted in production |

## Where to log

Log at the boundary where the raw error is first observed — inside gateways and repository `fromPromise` callbacks. Do not re-log the same error as it bubbles up through services.

```ts
// correct — logged once at the boundary
ResultAsync.fromPromise(
  sql`...`,
  (e) => {
    logger.error('failed to create invite', { cause: e, tenantId });
    return 'db_error' as const;
  },
);

// wrong — logged again in the service that calls this
if (result.isErr()) {
  logger.error('invite creation failed', { error: result.error });
  return err(result.error);
}
```

## Include relevant identifiers

Always log the identifiers that would let you find the relevant records in a database or trace: `tenantId`, `userId`, `inviteId`, etc.
