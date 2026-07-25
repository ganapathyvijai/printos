# 06 — Module Communication

## Purpose

Define how business modules within `printos_core` (e.g. `estimating`, `production`, `materials`, `sales`) communicate with each other without creating tight coupling or circular dependencies.

## Preferred Communication Patterns

1. **Direct use-case call (same-layer, explicit dependency).** One module's Application layer may call another module's public use case interface directly when the relationship is a genuine, stable dependency (e.g. `production` reads confirmed data from `estimating`).
2. **Domain events.** For side effects that should happen when something occurs in another module (e.g. "job estimate approved" triggers "create production order"), publish a domain event and let interested modules subscribe. This avoids modules calling into each other's internals for cross-cutting effects.
3. **Frappe hooks (Infrastructure layer only).** Standard Frappe document events (`on_submit`, `on_update`, etc.) are used at the Infrastructure layer to trigger Application use cases — never to embed business logic directly in the hook.

## Forbidden Patterns

- Reaching directly into another module's Domain or Infrastructure internals.
- Importing another module's doctype controller directly instead of going through its Application use case.
- Circular module dependencies (Module A's Application depending on Module B's Application, which depends back on Module A).

## Shared Contracts

- Cross-module DTOs and event payloads are defined once in a shared location and imported by both sides — never duplicated.
- Any new cross-module contract is documented in `docs/blueprint` before implementation.

## Related Documents

- [04_Dependency_Rules.md](04_Dependency_Rules.md)
- [07_Request_Lifecycle.md](07_Request_Lifecycle.md)
