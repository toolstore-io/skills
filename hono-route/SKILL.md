---
name: hono-route
description: Use when adding, modifying, or reviewing an HTTP route handler in a Hono app — `app.get`/`app.post`, request validation with Zod, mapping service `Result` errors to HTTP status codes, route ordering, or auth helpers. Triggers on "endpoint", "route", "API handler", "wire up the API". Skip for Express, Fastify, or other frameworks.
---

# Hono Route Handlers

## Structure

Each route module exports a factory function that accepts service/repository dependencies and returns a `Hono` app instance. Dependencies are explicit function parameters, not imported globals.

```ts
export function inviteRoutes(inviteService: InviteService, auth: Auth, tenants: TenantRepository) {
  const app = new Hono();

  app.get('/members', async (c) => { ... });

  return app;
}
```

## Request validation

Validate request bodies with Zod before processing. Define schemas at the top of the file, outside the factory.

```ts
const acceptSchema = z.object({
  matrixLocalpart: z.string().regex(/^[a-z0-9._-]{1,255}$/, 'Invalid username'),
  password: z.string().min(1),
});

// inside the route:
const parsed = acceptSchema.safeParse(await c.req.json().catch(() => null));
if (!parsed.success) return c.json({ error: 'Invalid request' }, 400);
```

## Error mapping

Route handlers are the only place HTTP status codes appear. Map service error strings to HTTP codes with an exhaustive switch so new error variants surface at compile time.

```ts
const result = await inviteService.getInviteMetadata(c.req.param('token'));
if (result.isErr()) {
  switch (result.error) {
    case 'not_found':
      return c.json({ error: 'Invalid invite' }, 404);
    case 'invite_revoked':
      return c.json({ error: 'Invite is no longer valid' }, 410);
    case 'db_error':
      return c.json({ error: 'Internal server error' }, 500);
  }
}
return c.json(result.value);
```

## Route ordering

Register static paths before parameterized paths to avoid shadowing.

```ts
// correct
app.get('/email', ...);
app.get('/:token', ...);

// wrong — /:token shadows /email
app.get('/:token', ...);
app.get('/email', ...);
```

## Auth helpers

Define auth/session helpers as named inner functions within the factory to keep the dependency chain explicit.

```ts
async function requireTenant(headers: Headers, tenantId: string) {
  const session = await auth.api.getSession({ headers });
  if (!session) return null;
  const result = await tenants.findActiveByEmail(session.user.email);
  if (result.isErr()) return null;
  const tenant = result.value.find((t) => t.id === tenantId);
  return tenant ? { session, tenant } : null;
}
```
