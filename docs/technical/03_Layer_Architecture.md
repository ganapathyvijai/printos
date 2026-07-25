# 03 — Layer Architecture

## Purpose

Define each layer inside `printos_core`, its responsibilities, and what it must never contain.

## Domain Layer

**Responsibility:** Core business entities, value objects, and rules that are true regardless of ERPNext, database, or UI.

**Contains:** Plain Python classes/dataclasses, business rule validation, domain exceptions.

**Must never contain:** `frappe.*` imports, doctype references, HTTP concerns, ORM queries.

## Application Layer

**Responsibility:** Use cases — the orchestration of domain objects to fulfill a specific business operation (e.g. "create print job estimate", "confirm production order").

**Contains:** Use case classes/functions, port interfaces (abstract repositories/services), DTOs for input/output.

**Must never contain:** Direct ERPNext ORM calls, UI logic, HTTP routing.

## Infrastructure Layer

**Responsibility:** Concrete implementations of Application-layer ports using ERPNext/Frappe: doctype CRUD, background jobs, email, file storage, third-party integrations.

**Contains:** Repository implementations, ERPNext doctype controllers, hooks.py wiring, migration/patch scripts.

**Must never contain:** Business rule decisions that belong in Domain/Application.

## Interface Layer

**Responsibility:** Entry points that expose Application use cases to the outside world.

**Contains:** Whitelisted REST API methods, Desk client scripts, portal web pages, print formats.

**Must never contain:** Business logic — it only translates requests into Application use case calls and formats responses.

## Layer Diagram

```
 Interface  ──depends on──▶  Application  ──depends on──▶  Domain
     ▲                              ▲
     │                              │
 Infrastructure ───implements ports─┘
```

## Related Documents

- [02_Clean_Architecture.md](02_Clean_Architecture.md)
- [04_Dependency_Rules.md](04_Dependency_Rules.md)
- [05_Project_Structure.md](05_Project_Structure.md)
