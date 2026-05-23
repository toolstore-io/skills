---
name: code-style
description: Use when writing or reviewing any code in this codebase — TypeScript, Svelte, or otherwise. Covers verbosity over brevity, simplicity over cleverness, when to use a params object, when to extract a helper vs. inline, the no-premature-optimization rule, and the no-comments-by-default policy.
---

# Code Style

## Core principle

Write code that is immediately understandable to a reader who has not seen it before. Saving characters or lines is never worth a reader having to pause and re-read.

## Verbosity over brevity

- Prefer descriptive variable and function names over short ones.
- Spell out what something does rather than relying on implicit context.
- Avoid abbreviations unless they are universally understood (e.g., `id`, `url`).
- A longer name that leaves no doubt is better than a short name that requires inference.

```ts
// avoid
const r = await svc.reg(sn, tid, u, p);

// prefer
const result = await synapseService.registerUser(serverName, tenantId, username, password);
```

## Simplicity over cleverness

- Prefer the straightforward solution over the elegant one.
- Avoid language tricks or patterns that require specialist knowledge to read.
- If a reader has to think "wait, what does this do?" the code is too clever.

```ts
// avoid
const isAdmin = roles?.includes('admin') ?? false;

// prefer
const isAdmin = roles !== undefined && roles.includes('admin');
```

## No premature optimization

- Do not optimize for performance until a bottleneck is measured.
- Do not add caching, memoization, or batching speculatively.
- Write the clear version first; optimize only with evidence.

## Abstraction for grok-ability, not DRY

Abstract when it makes the callsite clearer, not just to remove duplication. A well-named helper that hides a long "rabbit trail" of implementation detail makes the primary logic easier to follow. Duplication that stays simple is preferable to an abstraction that requires jumping around to understand.

```ts
// a long secondary concern inlined makes the primary logic hard to follow
async function provisionTenant(tenantId: string) {
  const hmac = createHmac('sha1', secret)
    .update(`${nonce}\x00${username}\x00${password}\x00notadmin`)
    .digest('hex');
  const res = await fetch(`https://${serverName}/_synapse/admin/v1/register`, {
    method: 'POST',
    body: JSON.stringify({ nonce, username, password, admin: false, mac: hmac }),
  });
  // ... primary logic continues
}

// extracting the secondary concern reveals the shape of the primary logic
async function provisionTenant(tenantId: string) {
  const mac = computeRegistrationMac(secret, nonce, username, password, false);
  const result = await gateway.register(serverName, { nonce, username, password, admin: false, mac });
  // ... primary logic continues
}
```

Ask: does extracting this make the callsite easier to read, or does it just move code around? If the latter, leave it inline.

## Params objects for non-obvious arguments

Use a params object when positional arguments become non-obvious at the callsite: four or more args, multiple args of the same type, or any case where the order is easy to confuse.

```ts
// hard to read at the callsite — what does each boolean mean?
registerUser(serverName, tenantId, username, password, true, false);

// clear at the callsite
registerUser({ serverName, tenantId, username, password, admin: true, sendWelcome: false });
```

Positional args are fine for one or two clearly distinct parameters where the order is self-evident.

## Comments

- Default to no comments. Well-named identifiers should speak for themselves.
- Add a comment only when the WHY is non-obvious: a hidden constraint, a subtle invariant, a known bug workaround, or behavior that would surprise a reader.
- Never write comments that describe what the code does; write only why it does it.
