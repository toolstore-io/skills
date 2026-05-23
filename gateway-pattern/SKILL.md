---
name: gateway-pattern
description: Use when calling an external HTTP API, third-party SDK, or any service outside the project — wrapping `fetch`, an SDK client, or anything that returns HTTP status codes. Covers the gateway interface + concrete implementation + mock file structure, Result-returning methods, and the rule that nothing outside the gateway sees HTTP status codes or SDK exceptions.
---

# Gateway Pattern

## When this fires

You are about to call something the project does not own — `fetch`, an SDK client, a third-party API. If you are about to write `fetch(` or `new SomeSdkClient(` inside a service, route handler, or repository, stop. Wrap the external dependency in a gateway first and have the caller depend on the interface.

## Core idea

A gateway is a typed abstraction over a single external dependency. Services depend on the gateway interface, not the concrete implementation. This makes the external boundary explicit and swappable for mocks in tests.

## File structure

```
src/gateways/
├── serviceName.ts       # interface + concrete implementation
└── serviceName.mock.ts  # mock implementation for tests
```

## Interface definition

Define the interface in the same file as the implementation. Every method returns a `Result` or `ResultAsync` from neverthrow. Error variants are string literals describing what went wrong from the caller's perspective.

```ts
export interface SynapseGateway {
  fetchNonce(serverName: string): Promise<Result<string, 'unreachable' | 'request_failed'>>;

  register(
    serverName: string,
    payload: RegistrationPayload,
  ): Promise<
    Result<
      { userId: string; accessToken: string },
      'unreachable' | 'request_failed' | 'user_already_exists'
    >
  >;
}
```

## Concrete implementation

The concrete gateway implements the interface. It handles all HTTP/SDK specifics (headers, retries, error mapping) and translates them into string-literal error variants. Nothing outside the gateway knows about HTTP status codes or SDK error shapes.

```ts
export function createSynapseGateway(logger: Logger): SynapseGateway {
  async function fetchNonce(serverName: string): Promise<Result<string, 'unreachable' | 'request_failed'>> {
    try {
      const res = await fetch(`https://${serverName}/_synapse/admin/v1/register`);
      if (!res.ok) return err('request_failed');
      const body = await res.json();
      return ok(body.nonce as string);
    } catch {
      logger.error('synapse unreachable', { serverName });
      return err('unreachable');
    }
  }

  return { fetchNonce, register, ... };
}

export type SynapseGateway = ReturnType<typeof createSynapseGateway>;
```

## Key rules

- Services import the interface type, never the concrete factory.
- Error variants reflect caller-meaningful outcomes (`'user_already_exists'`, `'not_found'`), not HTTP status codes or SDK exception names.
- Never throw from a gateway method; all failures are `err(...)` returns.
- Log the raw underlying error inside the gateway before mapping it to a string variant.
- Export `type GatewayName = ReturnType<typeof createGatewayName>` so callers can reference the concrete type when needed.
