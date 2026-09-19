# 04 — Multi-Tenant Architecture (Working Draft)

Version:
0.3

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-19

---

# Scope Note

`docs/blueprint/25_MultiTenant_Architecture.md` is an Owner-approved Approval-stage architecture record, not Published, authored per [ADR-010-Blueprint-Numbering-Strategy](../decisions/ADR-010-Blueprint-Numbering-Strategy.md) and [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md). Accepted [ADR-015-Tenant-Company-Multi-Tenancy-Model](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) records the binding Tenant/Company decision that Blueprint 25 elaborates. This document is **a pre-decision, non-authoritative Draft retained for traceability, pending formal reconciliation** with `25_MultiTenant_Architecture.md` — it does not claim authority over that document and has not been formally reconciled with it. Do not treat this document as the final word on multi-tenancy, and do not treat Blueprint 25 as a current implementation basis either, since it is not yet Published.

---

# Purpose

Capture the pre-decision state of multi-tenant architectural thinking for PrintOS, so it is not lost. Accepted ADR-015 has since recorded the binding Tenant/Company decision, and `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, not Published) is its Owner-approved Approval-stage design elaboration. This document is retained as historical, non-authoritative input pending formal reconciliation with Blueprint 25.

---

# Background

Per [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md), PrintOS Phase 1 targets single print shops but must not preclude future multi-tenant SaaS operation. At the time this document was written, ERPNext's **Company** DocType was the current Approved anchor for tenant-scoping (`docs/standards/Naming_Registry.md`, Section 8), while "Tenant" itself was an informal, forward-looking term whose relationship to Company was recorded as Pending ADR (Naming Registry Section 27, item 11). That relationship has since been formally resolved by Accepted [ADR-015-Tenant-Company-Multi-Tenancy-Model](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) (Naming Registry Section 27, item 11 — Resolved); this document's own candidate-model analysis below has not been updated to reflect that resolution and remains pre-decision historical material.

---

# Main Content

## Candidate Models

| Model | Description | Status |
|---|---|---|
| Single-Tenant-per-Instance | One PrintOS/ERPNext instance per print shop; simplest, matches Phase 1 deployment | Current de facto Phase 1 model |
| Single Instance, Multi-Company | One instance, multiple ERPNext Companies, each representing a tenant | Candidate — leverages Company as tenant anchor per ADR-006 |
| Fully Isolated Multi-Instance SaaS | One instance per tenant, centrally provisioned/managed | Candidate — highest isolation, highest operational overhead |
| Hybrid | Small tenants share a multi-company instance; larger tenants get dedicated instances | Candidate — not yet evaluated |

## Company as Tenant-Scope Anchor (Historical Context)

At the time this Draft was written, ADR-006 required every Phase 1 data access pattern in `printos_core` to scope by Company (and Branch, where applicable) rather than assuming a single global dataset. Accepted ADR-015 subsequently retained and formalized Company as the ERPNext legal/accounting and business-scoping entity within a Tenant; ADR-015 is the binding decision. `docs/blueprint/25_MultiTenant_Architecture.md` is the Owner-approved Approval-stage design elaboration of that decision — it remains not Published and is not an implementation basis. This paragraph is preserved as historical context only; it is not a current implementation basis and grants no implementation authorization.

## Original Open Items — Current Disposition

- Tenant registry / provisioning workflow — still open; see `docs/blueprint/25_MultiTenant_Architecture.md` Section 13, required future operational work.
- Detailed operational enforcement of cross-tenant data isolation — still open; the isolation principles themselves are now settled, see `docs/blueprint/25_MultiTenant_Architecture.md` Sections 6, 8, 9.
- The shared-versus-dedicated operational database topology question is resolved: Tenants do not share an operational database. Detailed capacity scaling, provisioning automation, and operational mechanics remain future work.
- The Tenant vs Company naming resolution is Resolved — see Accepted ADR-015 and Naming Registry Section 27, item 11.

---

# Architecture Notes

This document must not be used as a basis for any current architecture or implementation decision.

- Accepted ADR-015 records the binding Tenant/Company decision.
- `docs/blueprint/25_MultiTenant_Architecture.md` is its Owner-approved Approval-stage design elaboration.
- Blueprint 25 is not Published and grants no implementation authorization.
- This document (the old Draft) is not a current architecture or implementation basis; everything in it is pre-decision and exploratory, not binding.

---

# Future Considerations

- Configuration Studio's tenant-scoped surfaces ([../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md)) should be designed against the model recorded in Accepted ADR-015 and elaborated in `docs/blueprint/25_MultiTenant_Architecture.md` (Section 15), not against this document's own pre-decision candidate models.

---

# Original Open Questions — Current Disposition

- (Resolved 2026-07-28 by Accepted ADR-015 and `docs/blueprint/25_MultiTenant_Architecture.md`, after this document was written) Which candidate model above should be brought to the Project Owner for a Level 3 ADR decision, and on what timeline relative to Phase 2 planning?
- (Resolved 2026-07-28 by Accepted ADR-015; Naming Registry Section 27, item 11) Should the Tenant/Company naming conflict be resolved before or after the model itself is chosen?

---

# Related Documents

- [../decisions/ADR-006-MultiTenant-Strategy.md](../decisions/ADR-006-MultiTenant-Strategy.md)
- [../decisions/ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md)
- `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, not Published — an Owner-approved Approval-stage architecture record; formal reconciliation with this pre-decision working draft remains pending)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-23 | Initial | Initial working draft, pending reconciliation with the reserved `docs/blueprint/25_MultiTenant_Architecture.md`. |
| 0.2 | 2026-09-19 | Multi-Tenant Reconciliation — Pre-Decision Status Correction (MT-R1) | Corrected active statements that described `docs/blueprint/25_MultiTenant_Architecture.md` as reserved/not-yet-written and the Tenant/Company relationship as Pending ADR. Recorded that Blueprint 25 is an Owner-approved Approval-stage architecture record, not Published, and that Accepted ADR-015 records the binding Tenant/Company decision Blueprint 25 elaborates; renamed "What Remains Open" to "Original Open Items — Current Disposition" since the list now contains both resolved and unresolved items; labeled this document's own candidate-model analysis (unchanged) as pre-decision, non-authoritative historical material pending formal reconciliation. Reference-only correction; no candidate-model content changed; no architecture decision made; document remains Draft; no implementation authorization granted. |
| 0.3 | 2026-09-19 | Company-Scoping Historical Reframing and Open-Questions Heading Rename | Reframed the "Company as Tenant-Scope Anchor" section as historical context: at the time this Draft was written, ADR-006 required Company/Branch scoping; Accepted ADR-015 subsequently retained and formalized Company as the ERPNext legal/accounting and business-scoping entity within a Tenant and is the binding decision; `docs/blueprint/25_MultiTenant_Architecture.md` is the Owner-approved Approval-stage design elaboration, remains not Published, and is not an implementation basis. Renamed the "Open Questions" heading to "Original Open Questions — Current Disposition" for consistency with the prior "Original Open Items" rename, since both listed questions are marked Resolved. Grants no implementation authorization; document remains Draft. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
