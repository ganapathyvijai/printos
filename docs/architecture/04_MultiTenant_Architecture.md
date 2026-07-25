# 04 — Multi-Tenant Architecture (Working Draft)

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

# Scope Note

`docs/blueprint/25_MultiTenant_Architecture.md` is the path formally **reserved** for PrintOS's authoritative Multi-Tenant Architecture document, per [ADR-010-Blueprint-Numbering-Strategy](../decisions/ADR-010-Blueprint-Numbering-Strategy.md) and [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md). That document does not yet exist. This document is a **working draft** occupying that gap — it does not claim authority over the reserved path and must be reconciled with (merged into, or explicitly superseded by) `25_MultiTenant_Architecture.md` once that document is written. Do not treat this document as the final word on multi-tenancy; treat it as input to that future document.

---

# Purpose

Capture the current state of multi-tenant architectural thinking for PrintOS, so it is not lost between now and the formal authoring of `docs/blueprint/25_MultiTenant_Architecture.md`.

---

# Background

Per [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md), PrintOS Phase 1 targets single print shops but must not preclude future multi-tenant SaaS operation. ERPNext's **Company** DocType is the current Approved anchor for tenant-scoping (`docs/standards/Naming_Registry.md`, Section 8), while "Tenant" itself remains an informal, forward-looking term whose relationship to Company is recorded as **Pending ADR** (Naming Registry Section 27, item 11). This document does not resolve that conflict.

---

# Main Content

## Candidate Models

| Model | Description | Status |
|---|---|---|
| Single-Tenant-per-Instance | One PrintOS/ERPNext instance per print shop; simplest, matches Phase 1 deployment | Current de facto Phase 1 model |
| Single Instance, Multi-Company | One instance, multiple ERPNext Companies, each representing a tenant | Candidate — leverages Company as tenant anchor per ADR-006 |
| Fully Isolated Multi-Instance SaaS | One instance per tenant, centrally provisioned/managed | Candidate — highest isolation, highest operational overhead |
| Hybrid | Small tenants share a multi-company instance; larger tenants get dedicated instances | Candidate — not yet evaluated |

## Company as Tenant-Scope Anchor

Every Phase 1 data access pattern in `printos_core` must scope by Company (and Branch, where applicable) rather than assuming a single global dataset, per ADR-006's stated consequence. This is the one concrete, actionable rule this document carries forward regardless of which candidate model above is eventually chosen.

## What Remains Open

- Tenant registry / provisioning workflow.
- Cross-tenant data isolation guarantees beyond what Company-scoping already provides.
- Scaling strategy (shared vs. dedicated database per tenant).
- The Tenant vs Company naming resolution itself (Pending ADR).

---

# Architecture Notes

This document must not be used as a basis for implementation decisions beyond the Company-scoping rule already established by ADR-006 — everything else here is exploratory, not binding.

---

# Future Considerations

- Configuration Studio's tenant-scoped surfaces ([../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md)) are designed against whichever model this document eventually settles on being built atop Company; no redesign of the Configuration layer is expected regardless of which candidate model is chosen.

---

# Open Questions

- Which candidate model above should be brought to the Project Owner for a Level 3 ADR decision, and on what timeline relative to Phase 2 planning?
- Should the Tenant/Company naming conflict be resolved before or after the model itself is chosen?

---

# Related Documents

- [../decisions/ADR-006-MultiTenant-Strategy.md](../decisions/ADR-006-MultiTenant-Strategy.md)
- [../decisions/ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md)
- `docs/blueprint/25_MultiTenant_Architecture.md` (reserved, not yet written)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-23 | Initial | Initial working draft, pending reconciliation with the reserved `docs/blueprint/25_MultiTenant_Architecture.md`. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
