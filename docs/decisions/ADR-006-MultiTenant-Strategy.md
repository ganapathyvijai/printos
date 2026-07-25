# ADR-006: Design for Future Multi-Tenant SaaS from Phase 1

Status:
Accepted (strategy); Implementation Deferred

Date:
2026-07-18

---

## Context

PrintOS Phase 1 targets individual print shops (G2), plausibly deployed as single-tenant instances initially. However, PrintHub's long-term goal is a commercial SaaS platform serving many print shops, and retrofitting multi-tenancy onto an architecture that assumed single-tenant operation is historically difficult and risky.

## Decision

Adopt a "future multi-tenant SaaS" design constraint from Phase 1 onward: architectural choices in `printos_core` must not preclude a future move to multi-tenant operation, even though full multi-tenant implementation is deferred. A dedicated architecture document (`docs/blueprint/25_MultiTenant_Architecture.md`, number reserved per [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md)) will formally define the single-tenant, multi-tenant, and hybrid models, tenant registry, and company-level isolation approach.

## Reasons

- Retrofitting tenant isolation after significant Phase 1 development would likely require a costly rearchitecture of data access and company-scoping logic.
- ERPNext already has a concept of Company, which is a natural anchor point for future tenant isolation, provided Phase 1 code never bypasses Company-scoped access assumptions.
- Aligns with `CHATGPT.md`'s stated architecture philosophy: "Future Multi-Tenant SaaS" is listed as a Design Principle, not an optional add-on.

## Consequences

- Phase 1 development must consistently scope data access by Company/Branch rather than assuming a single global dataset, even while operating single-tenant in practice.
- Full multi-tenant mechanics (tenant registry, cross-tenant isolation guarantees, scaling strategy) remain an open design area until `docs/blueprint/25_MultiTenant_Architecture.md` is completed.
- Any Phase 1 shortcut that hardcodes single-tenant assumptions (e.g., global uniqueness assumptions that ignore Company scope) must be flagged and reconsidered.

## Related Documents

- `docs/blueprint/25_MultiTenant_Architecture.md` (reserved per [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md))
- `docs/blueprint/01_Project_Vision.md`
- [ADR-007-Offline-First.md](ADR-007-Offline-First.md)
- [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved path from `15_MultiTenant_Architecture.md` to `25_MultiTenant_Architecture.md` per ADR-010 (numbering collision with the unrelated 11-20 scaffold); no change to decision content|
