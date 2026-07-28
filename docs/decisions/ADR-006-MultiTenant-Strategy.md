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
- Full multi-tenant mechanics (tenant registry, cross-tenant isolation guarantees, scaling strategy) were an open design area at the time of this ADR's original acceptance; the concrete topology decision was subsequently completed by Accepted [ADR-015-Tenant-Company-Multi-Tenancy-Model](ADR-015-Tenant-Company-Multi-Tenancy-Model.md) (Version 1.0, 2026-07-28) and elaborated in `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0): one isolated Frappe site and one isolated operational database per Tenant is now the governed architecture, with Tenant formally distinct from Company. Implementation of that concrete model remains deferred and unauthorized (see Companion Decision Reference below).
- Any Phase 1 shortcut that hardcodes single-tenant assumptions (e.g., global uniqueness assumptions that ignore Company scope) must be flagged and reconsidered.

## Companion Decision Reference

Accepted [ADR-015-Tenant-Company-Multi-Tenancy-Model](ADR-015-Tenant-Company-Multi-Tenancy-Model.md) (Version 1.0, 2026-07-28) completes the concrete topology and Tenant/Company definition deliberately deferred by this ADR. **This ADR (ADR-006) remains Accepted and is neither amended in substance nor superseded.** ADR-015 is a companion, completing decision, not a replacement.

- Resolving ADR: [ADR-015-Tenant-Company-Multi-Tenancy-Model](ADR-015-Tenant-Company-Multi-Tenancy-Model.md), Accepted, Version 1.0.
- Elaborating architecture document: `docs/blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0 (not Published).
- Company scoping, as an interim rule under this ADR, is retained and formalized by ADR-015 as the ERPNext legal/accounting anchor inside a Tenant, distinct from the Tenant boundary itself.
- Implementation of the concrete topology remains deferred; no implementation authorization is granted by either ADR-006 or ADR-015.

## Related Documents

- `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0; number reserved per [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md))
- [ADR-015-Tenant-Company-Multi-Tenancy-Model.md](ADR-015-Tenant-Company-Multi-Tenancy-Model.md) (companion, completing decision)
- `docs/blueprint/01_Project_Vision.md`
- [ADR-007-Offline-First.md](ADR-007-Offline-First.md)
- [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved path from `15_MultiTenant_Architecture.md` to `25_MultiTenant_Architecture.md` per ADR-010 (numbering collision with the unrelated 11-20 scaffold); no change to decision content|
|1.2|2026-07-28|Companion-Decision Factual Synchronization|Reconciled the Consequences section's stale statement that full multi-tenant mechanics "remain an open design area until `docs/blueprint/25_MultiTenant_Architecture.md` is completed," recording that the concrete topology decision was subsequently completed by Accepted ADR-015 (Version 1.0, 2026-07-28) and elaborated in `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0). Added a new "Companion Decision Reference" section identifying ADR-015 as a companion, completing decision — this ADR (ADR-006) remains Accepted and is neither amended in substance nor superseded. Updated Related Documents to reflect Blueprint 25's current Approval status and to reference ADR-015. This ADR's original Context, Decision, Reasons, and historical rationale are unchanged; implementation of the concrete topology remains deferred and unauthorized.|
