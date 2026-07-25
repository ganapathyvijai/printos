# 01 — Configuration Architecture

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

## Purpose

Define how PrintOS's configuration layer fits into the overall Clean Architecture, so "no-code/low-code" designer tools remain consistent with the platform's dependency rules rather than becoming a parallel, undocumented system.

## Configuration as Data, Not Code

Every designer described in this section (Workflow, Approval, Form, Dashboard, Automation, Notification, Role/Permission, Report, Integration) produces **configuration records** — stored as DocTypes — that are read and interpreted by Application-layer use cases at runtime. Designers do not generate or inject executable code.

```
Designer UI (Interface layer)
        │  writes
        ▼
Configuration DocTypes (Infrastructure layer, owned data)
        │  read by
        ▼
Application-layer use cases  ──▶  Domain rules
```

## Why This Matters

- Keeps configuration upgrade-safe: ERPNext/PrintOS upgrades don't break tenant configuration because configuration is data, versioned and migrated like any other record.
- Keeps business rules in the Domain/Application layers, not scattered across designer-generated scripts.
- Enables multi-tenant customization (see [13_Tenant_Customization.md](13_Tenant_Customization.md)) without forking code per tenant.

## Configuration Storage

- Each designer owns its own configuration DocTypes (e.g. Workflow Designer owns `PrintOS Workflow`, `PrintOS Workflow State`).
- Configuration DocTypes live under `printos_core/infrastructure/configuration/<designer>/doctype/`, consistent with [technical/05_Project_Structure.md](../technical/05_Project_Structure.md).
- Configuration is never stored as raw JSON blobs when structured DocType fields/child tables can represent it — this keeps it queryable, permissioned, and auditable.

## Runtime Resolution

At runtime, Application use cases resolve configuration in this order: tenant-specific override → template default → system default. See [13_Tenant_Customization.md](13_Tenant_Customization.md) and [14_Template_Library.md](14_Template_Library.md).

## Terminology Note

"Tenant" is used below and throughout this document set as an informal, forward-looking term for a future multi-tenant SaaS operating mode. It is **not yet an Approved term** in `docs/standards/Naming_Registry.md`; the currently Approved scoping anchor is ERPNext **Company** (Naming Registry Section 8), and full multi-tenant architecture is deferred to `docs/blueprint/25_MultiTenant_Architecture.md` per [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md). This document does not resolve that conflict (Naming Registry Section 27, item 11); it only reserves consistent vocabulary ahead of the ADR.

## Related Documents

- [02_Module_Manager.md](02_Module_Manager.md)
- [12_Feature_Flags.md](12_Feature_Flags.md)
- [13_Tenant_Customization.md](13_Tenant_Customization.md)
- [15_Deployment_Model.md](15_Deployment_Model.md)
- [../technical/02_Clean_Architecture.md](../technical/02_Clean_Architecture.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [ADR-006 — Multi-Tenant Strategy](../decisions/ADR-006-MultiTenant-Strategy.md)

---

# Future Considerations

- Once `docs/blueprint/25_MultiTenant_Architecture.md` is written and the Tenant/Company naming conflict is resolved by ADR, this document and [13_Tenant_Customization.md](13_Tenant_Customization.md) must be updated to use the resolved term consistently.
- MachineIQ, Marketplace, plugin, and industry-template configuration surfaces (per [ADR-008](../decisions/ADR-008-MachineIQ.md), [ADR-009](../decisions/ADR-009-Marketplace.md)) are expected to plug into this same configuration-as-data model without requiring a redesign of the resolution order described above.

---

# Open Questions

- Should the configuration resolution order (tenant → template → system default) be formalized as its own ADR once multi-tenant architecture is defined?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata, Terminology Note flagging "Tenant" vs "Company" as Pending ADR (Naming Registry Section 27, item 11), Future Considerations, Open Questions, and Revision History/Quality Checklist to match the Blueprint documentation template. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
