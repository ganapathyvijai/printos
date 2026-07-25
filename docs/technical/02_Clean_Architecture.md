# 02 — Clean Architecture

## Purpose

Define how Clean Architecture principles are applied within `printos_core`, so business logic remains independent of ERPNext/Frappe framework details and can be tested, reasoned about, and evolved without being tangled with the platform.

## Why Clean Architecture

ERPNext/Frappe is powerful but framework-coupled (doctypes, hooks, whitelisted methods). Without discipline, business logic leaks into controllers and client scripts, making it hard to test and easy to break during ERPNext upgrades. Clean Architecture keeps the **business rules** at the center, with the framework treated as a replaceable detail.

## The Dependency Rule

> Source code dependencies only point inward. Nothing in an inner layer knows anything about an outer layer.

- Domain knows nothing about Application, Infrastructure, or Interface.
- Application knows Domain, but not Infrastructure or Interface.
- Infrastructure and Interface depend on Application/Domain — never the reverse.

Full rules are enumerated in [04_Dependency_Rules.md](04_Dependency_Rules.md).

## Layers (Summary)

| Layer | Responsibility | Depends on |
|-------|-----------------|------------|
| Domain | Entities, value objects, core business rules | Nothing |
| Application | Use cases, orchestration, interfaces (ports) | Domain |
| Infrastructure | ERPNext/Frappe doctypes, ORM, external services (adapters) | Application, Domain |
| Interface | REST endpoints, desk UI, client scripts, portal pages | Application |

See [03_Layer_Architecture.md](03_Layer_Architecture.md) for full detail.

## Ports and Adapters

Application-layer use cases depend on abstract interfaces ("ports"). ERPNext-specific implementations ("adapters") satisfy those interfaces in the Infrastructure layer. This keeps domain/application code free of `frappe.*` calls.

## Benefits for PrintOS

- ERPNext upgrades touch only Infrastructure/Interface adapters, not business rules.
- Business logic is unit-testable without a running Frappe site.
- New delivery channels (portal, API, mobile) can reuse the same Application layer.

## Related Documents

- [03_Layer_Architecture.md](03_Layer_Architecture.md)
- [04_Dependency_Rules.md](04_Dependency_Rules.md)
- [05_Project_Structure.md](05_Project_Structure.md)
