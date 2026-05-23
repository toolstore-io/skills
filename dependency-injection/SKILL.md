---
name: dependency-injection
description: Use when creating a service, repository, gateway, or route factory; wiring concrete implementations together at the app entry point; or deciding whether a module should import a concrete factory vs. an interface type. Covers the factory-function pattern, the `type X = ReturnType<typeof createX>` alias, and the no-DI-container / no-globals rule.
---

# Dependency Injection

Dependencies are passed as explicit function parameters to factory functions. There are no DI containers, no global singletons, and no service locators.

## Factory functions

Every service and repository is created by a `create*` factory function that takes its dependencies as typed parameters.

```ts
// repository takes sql client and logger
export function createInviteRepository(sql: Sql, logger: Logger) { ... }

// service takes a gateway and a repository
export function createInviteService(gateway: SynapseGateway, invites: InviteRepository) { ... }

// route factory takes services and auth
export function inviteRoutes(inviteService: InviteService, auth: Auth, tenants: TenantRepository) { ... }
```

## Type aliases

Export a `type *Service = ReturnType<typeof create*Service>` alias from every factory module. This is what consumers import to type their parameters.

```ts
export type InviteService = ReturnType<typeof createInviteService>;
export type InviteRepository = ReturnType<typeof createInviteRepository>;
```

## Depend on interfaces, not implementations

Services accept gateway interfaces, not concrete gateway factories. This makes swapping implementations (real vs. mock) a callsite decision.

```ts
// service.ts — depends on the interface
import type { SynapseGateway } from '../gateways/synapse.js';
export function createSynapseService(gateway: SynapseGateway) { ... }

// app entry point — provides the concrete implementation
import { createSynapseGateway } from './gateways/synapse.js';
const synapseService = createSynapseService(createSynapseGateway(logger));

// test file — provides the mock
import { createSynapseMock } from './gateways/synapse.mock.js';
const synapseService = createSynapseService(createSynapseMock());
```

## Wiring

Dependencies are wired at the application entry point (e.g., `src/index.ts` or `src/app.ts`). The wiring file is the only place that imports concrete implementations. All other files import only types and interfaces.
