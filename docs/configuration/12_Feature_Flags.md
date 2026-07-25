# 12 — Feature Flags

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

> **Terminology note:** "Tenant" below is used per the informal, forward-looking convention defined in [01_Configuration_Architecture.md](01_Configuration_Architecture.md#terminology-note) — not yet an Approved Naming Registry term (Pending ADR, Section 27 item 11).

## Purpose

Define how feature flags are used to toggle PrintOS functionality per tenant, environment, or rollout stage, without branching code paths ad hoc.

## Concept

Feature flags are configuration records that Application-layer use cases and Interface-layer routes consult to decide whether a feature is active, enabling safe rollout, tenant-specific packaging, and quick rollback.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Feature Flag | Named flag, default state, description |
| PrintOS Feature Flag Override | Per-tenant/per-environment override of a flag's state |

## Use Cases for Flags

- Gating a module before general availability (works alongside [02_Module_Manager.md](02_Module_Manager.md)).
- Enabling premium/add-on capabilities per tenant subscription tier.
- Safely rolling out a risky change to a subset of tenants before full release.

## Rules

- Flags are read at the Application layer boundary — Domain logic must not depend on flag state directly (Domain rules are either always true or belong to a variant use case).
- Stale flags (features fully rolled out or fully removed) must be cleaned up — flags are not meant to live forever; each flag should have an intended removal point noted at creation.
- Flag state changes affecting production tenants follow the deployment/promotion process (see [15_Deployment_Model.md](15_Deployment_Model.md)).

## Related Documents

- [02_Module_Manager.md](02_Module_Manager.md)
- [13_Tenant_Customization.md](13_Tenant_Customization.md)
- [15_Deployment_Model.md](15_Deployment_Model.md)

---

# Future Considerations

- Feature flags must be able to gate MachineIQ, Marketplace, and future plugin/feature-pack rollout (per [ADR-008](../decisions/ADR-008-MachineIQ.md), [ADR-009](../decisions/ADR-009-Marketplace.md)) using the same flag model defined here.

---

# Open Questions

- Should flag removal be enforced tooling-side (e.g. a lint check for flags past their intended removal point), or remain a process discipline only?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata, Terminology Note reference, Future Considerations, Open Questions, Revision History/Quality Checklist. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
