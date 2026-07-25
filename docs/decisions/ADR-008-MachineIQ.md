# ADR-008: MachineIQ as a Future, API-Consumed Analytics Capability

Status:
Accepted (strategy); Scope Deferred

Date:
2026-07-18

---

## Context

PrintHub's long-term technology stack (`docs/blueprint/07_Technology_Stack.md`) anticipates MachineIQ — a machine intelligence/analytics capability — built on future technologies such as ClickHouse and FastAPI. MachineIQ is classified as a future Core Domain in `docs/blueprint/05_Domain_Model.md`, since it is expected to become a key differentiator once mature, but its detailed scope is not yet defined.

## Decision

MachineIQ is planned as a logically and physically separate capability that consumes operational data from PrintOS (Production, Reporting) via API, rather than being embedded directly inside `printos_core` or given direct database access. Its full scope, data contracts, and architecture are deferred to a dedicated future Blueprint document.

## Reasons

- Keeping MachineIQ as an API consumer (not a direct database consumer) preserves the ERPNext/PrintOS architectural boundary (see [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md)) and avoids coupling core transactional logic to analytics workloads.
- Analytics workloads (ClickHouse-style aggregation) have different scaling and technology needs than transactional ERP workloads (MariaDB), justifying separate infrastructure.
- Deferring detailed scope avoids over-designing a capability whose business requirements are not yet fully known, while still committing to the architectural boundary (API-based integration) now.

## Consequences

- Production and Reporting modules must expose or emit the data (via API or events, see `standards/Event_Standards.md`) that MachineIQ will eventually need, even before MachineIQ itself is built.
- No direct database access from a future MachineIQ service to PrintOS's operational database is permitted under this decision.
- A dedicated Blueprint document scoping MachineIQ's domain model, data contracts, and architecture is required before implementation begins.

## Related Documents

- `docs/blueprint/05_Domain_Model.md`
- `docs/blueprint/06_Bounded_Contexts.md`
- `docs/blueprint/07_Technology_Stack.md`
- `standards/Event_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
