---
name: neverthrow
description: neverthrow Result<T, E> conventions for error handling. Use when writing any fallible function in service or repository code.
---

# neverthrow

All fallible service and repository functions return `Result<T, E>` or `ResultAsync<T, E>` from neverthrow. Never throw; never use ad-hoc `{ data, error }` tuples.

## Error types

The error type `E` is always a string literal union. Never use error classes, error objects, or numeric codes.

```ts
// correct
Promise<Result<User, 'not_found' | 'db_error'>>

// wrong
Promise<Result<User, Error>>
Promise<Result<User, { code: number }>>
```

## Sync and async

Use `Result` for synchronous operations and `ResultAsync` for async ones.

```ts
import { ok, err, type Result, ResultAsync } from 'neverthrow';

// sync
function parseToken(raw: string): Result<Token, 'invalid_token'> {
  if (!isValid(raw)) return err('invalid_token');
  return ok(parse(raw));
}

// async — wrapping a promise
function findUser(id: string): ResultAsync<User | null, 'db_error'> {
  return ResultAsync.fromPromise(
    db.query('SELECT * FROM users WHERE id = $1', [id]).then((r) => r.rows[0] ?? null),
    () => 'db_error' as const,
  );
}
```

## Bubbling errors

When propagating an error from a callee, use `return err(result.error)` to preserve the original error type. Never re-wrap with a hardcoded string.

```ts
// correct — preserves the callee's error type in the union
const nonceResult = await gateway.fetchNonce(serverName);
if (nonceResult.isErr()) return err(nonceResult.error);

// wrong — swallows the specific error variant, hides new ones at compile time
if (nonceResult.isErr()) return err('gateway_error');
```

## HTTP layer boundary

Services and repositories never produce HTTP status codes. Route handlers are the only place service error strings are mapped to HTTP responses.

```ts
// service — never mention HTTP
return err('not_found');

// route handler — only place HTTP codes appear
if (result.error === 'not_found') return c.json({ error: 'Not found' }, 404);
```

## Exhaustive error handling

Use exhaustive switch statements on error variants in route handlers so TypeScript catches unhandled new variants at compile time.

```ts
if (result.isErr()) {
  switch (result.error) {
    case 'not_found': return c.json({ error: 'Not found' }, 404);
    case 'db_error':  return c.json({ error: 'Server error' }, 500);
  }
}
```
