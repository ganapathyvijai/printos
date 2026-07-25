# ADR-004: Apply Domain-Driven Design to printos_core

Status:
Accepted

Date:
2026-07-18

---

## Context

PrintOS must express print-industry business concepts (estimation, artwork approval, production, machine scheduling) in a way that remains stable and understandable independent of ERPNext's underlying implementation. Without an explicit modeling discipline, business logic risks becoming scattered, duplicated, or tightly coupled to Frappe/ERPNext internals.

## Decision

Apply Domain-Driven Design (DDD) to the design of `printos_core`: classify domains as Core/Supporting/Generic, define bounded contexts with explicit responsibilities and vocabulary, and keep the Domain layer free of direct ERPNext/Frappe dependencies.

## Reasons

- Print-industry vocabulary (substrate, finishing, job card, machine profile) is precise and business-critical; DDD gives that vocabulary a durable, documented home (`docs/blueprint/05_Domain_Model.md`).
- Classifying domains by strategic importance (Core vs. Supporting vs. Generic) directs engineering investment toward what differentiates PrintOS, and toward reusing ERPNext's generic capability everywhere else.
- Bounded contexts (`docs/blueprint/06_Bounded_Contexts.md`) give a clear, non-overlapping map of responsibility, which both prevents duplicated business logic and enables future modular/microservice extraction.

## Consequences

- Domain layer code must not import Frappe/ERPNext modules directly; Infrastructure-layer adapters mediate that dependency (see [ADR-005-Module-Boundaries.md](ADR-005-Module-Boundaries.md) and `docs/blueprint/04_System_Architecture.md`).
- New business concepts must be placed within an existing bounded context or justify a new one, rather than being added ad hoc.
- Terminology introduced in code, UI, and documentation must stay consistent with the Domain Model; divergence is treated as a defect.

## Related Documents

- `docs/blueprint/05_Domain_Model.md`
- `docs/blueprint/06_Bounded_Contexts.md`
- [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md)
- [ADR-005-Module-Boundaries.md](ADR-005-Module-Boundaries.md)
- `standards/Coding_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
