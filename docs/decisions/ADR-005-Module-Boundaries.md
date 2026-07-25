# ADR-005: Module Boundaries Align to Bounded Contexts

Status:
Accepted

Date:
2026-07-18

---

## Context

`printos_core` will grow to cover many business areas — CRM, Sales, Estimation, Artwork, Production, Inventory, Procurement, Warehouse, Dispatch, Accounts, GST, HR, Administration, Reporting, and future MachineIQ/Marketplace capability. Without an explicit rule for where module boundaries fall, the codebase risks becoming a tightly-coupled monolith that is difficult to test, extend, or later split into services.

## Decision

Each module inside `printos_core` maps to exactly one bounded context defined in `docs/blueprint/06_Bounded_Contexts.md`. Modules communicate through well-defined inputs/outputs (explicit calls or events), never through shared internal state.

## Reasons

- Bounded contexts already define non-overlapping responsibility and vocabulary at the business level; mirroring them in code keeps the technical structure traceable back to the documented business architecture.
- Low coupling between modules preserves the option to later extract a module into an independent service (see Future Microservices, `docs/blueprint/04_System_Architecture.md`) without a full rewrite.
- Prevents duplicated business logic, since each concept has exactly one owning module, consistent with `PROJECT_RULES.md` Rule 17.

## Consequences

- Cross-module interaction must go through explicit interfaces or events (see `standards/Event_Standards.md`), which adds some upfront design overhead compared to directly reaching into another module's internals.
- Introducing a new business capability requires first deciding which bounded context (existing or new) it belongs to, rather than adding it wherever is most convenient.
- Module structure and Blueprint bounded contexts must be kept in sync; a mismatch is treated as a documentation or architecture defect, not tolerated as harmless drift.

## Related Documents

- `docs/blueprint/06_Bounded_Contexts.md`
- `docs/blueprint/09_PrintOS_Modules.md`
- [ADR-004-Domain-Driven-Design.md](ADR-004-Domain-Driven-Design.md)
- `standards/Coding_Standards.md`
- `standards/Event_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
