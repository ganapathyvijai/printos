# ADR-002: Never Modify ERPNext Core — All Customization Inside printos_core

Status:
Accepted

Date:
2026-07-18

---

## Context

Having adopted ERPNext v16 as the application framework ([ADR-001](ADR-001-ERPNext-Framework.md)), PrintHub must decide how print-industry-specific business logic is added. Directly editing ERPNext core files would be the fastest short-term path but would break compatibility with future ERPNext upgrades and put the long-term maintainability of PrintOS at risk.

## Decision

Never modify ERPNext core. All PrintOS-specific business logic, DocTypes, and workflows are implemented inside a dedicated custom application: `printos_core`, using Frappe's supported extension mechanisms (custom apps, custom fields, hooks, custom DocTypes).

## Reasons

- Preserves the ability to upgrade ERPNext versions over the multi-year life of the project without merge conflicts against a modified core.
- Keeps business concepts independent of ERPNext implementation details, supporting long-term maintainability.
- Gives PrintOS a clean, portable definition of "what is PrintHub-specific" versus "what is generic ERP" for future architectural decisions (e.g., multi-tenancy, microservice extraction).

## Consequences

- Some features may take longer to implement than a direct core edit would, because they must go through supported extension points.
- All contributors (human and AI) must verify, before any change, whether it touches ERPNext core — if so, it must be redesigned to live in `printos_core` instead.
- `printos_core` becomes the single place where business logic accumulates, requiring disciplined modular design (see [ADR-005-Module-Boundaries.md](ADR-005-Module-Boundaries.md)) to avoid becoming an unmanageable monolith.

## Related Documents

- [ADR-001-ERPNext-Framework.md](ADR-001-ERPNext-Framework.md)
- [ADR-005-Module-Boundaries.md](ADR-005-Module-Boundaries.md)
- `docs/blueprint/04_System_Architecture.md`
- `standards/Coding_Standards.md`
- `standards/DocType_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
