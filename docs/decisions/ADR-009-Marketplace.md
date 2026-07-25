# ADR-009: Marketplace Is Postponed to Phase 5

Status:
Accepted

Date:
2026-07-18

---

## Context

PrintHub's long-term vision includes a Marketplace connecting public buyers (G1) directly with print shops, per `docs/blueprint/01_Project_Vision.md` and `docs/blueprint/03_Product_Roadmap.md`. However, Phase 1 is scoped strictly around PrintOS ERP for print shops (G2), with customers (G1) existing only as ERP customer records.

## Decision

Marketplace functionality — public buyers as active platform users, buyer-facing discovery, and marketplace ordering — is explicitly out of scope until Phase 5, following the Freelancer Portal (Phase 2), Supplier Portal (Phase 3), and Service Engineer (Phase 4) phases. This is recorded as an explicit exclusion in `docs/blueprint/02_Business_Requirements.md`, not merely an unstated gap.

## Reasons

- Phase 1 must ship a focused, provable ERP product before taking on the added complexity of a multi-sided marketplace business model.
- Marketplace introduces materially different concerns (public-facing discovery, buyer trust, multi-shop routing) that would dilute Phase 1 focus if pursued prematurely.
- Sequencing Marketplace after Freelancer, Supplier, and Service Engineer phases lets each prior phase's architecture (module boundaries, multi-tenant strategy) mature and be proven before the highest-complexity phase is attempted.

## Consequences

- CRM and Sales modules must be designed so that a future Marketplace-originated lead/order can be routed in without requiring their redesign (see `docs/blueprint/06_Bounded_Contexts.md`, `docs/blueprint/10_Business_Workflows.md` — Future Marketplace Workflow), even though Marketplace itself is not built yet.
- No Phase 1–4 feature should assume G1 (public buyers) will remain permanently record-only; the eventual transition to active marketplace participants must remain architecturally possible.
- Full Marketplace business model, workflows, and architecture require a dedicated future Blueprint scoping effort at Phase 5 planning.

## Related Documents

- `docs/blueprint/01_Project_Vision.md`
- `docs/blueprint/02_Business_Requirements.md`
- `docs/blueprint/03_Product_Roadmap.md`
- `docs/blueprint/06_Bounded_Contexts.md`
- `docs/blueprint/10_Business_Workflows.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
