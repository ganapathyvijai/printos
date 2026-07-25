# Configuration Documentation — Master Index

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

Master index for PrintOS's configuration layer — the set of designer/builder tools and mechanisms that let PrintOS behavior be configured without modifying code or ERPNext core.

## Contents

| # | Document | Purpose |
|---|----------|---------|
| 01 | [Configuration Architecture](01_Configuration_Architecture.md) | How configuration fits into the overall PrintOS architecture |
| 02 | [Module Manager](02_Module_Manager.md) | Enabling/disabling and managing PrintOS modules |
| 03 | [Workflow Designer](03_Workflow_Designer.md) | Configuring document workflows and state transitions |
| 04 | [Approval Designer](04_Approval_Designer.md) | Configuring approval chains and conditions |
| 05 | [Form Designer](05_Form_Designer.md) | Configuring form layout and fields |
| 06 | [Dashboard Designer](06_Dashboard_Designer.md) | Configuring dashboards and widgets |
| 07 | [Automation Rules](07_Automation_Rules.md) | Configuring trigger-based automation |
| 08 | [Notification Designer](08_Notification_Designer.md) | Configuring alerts and notifications |
| 09 | [Role & Permission Designer](09_Role_Permission_Designer.md) | Configuring roles, permissions, and access rules |
| 10 | [Report Designer](10_Report_Designer.md) | Configuring reports |
| 11 | [Integration Designer](11_Integration_Designer.md) | Configuring external system integrations |
| 12 | [Feature Flags](12_Feature_Flags.md) | Toggling features per tenant/environment |
| 13 | [Tenant Customization](13_Tenant_Customization.md) | Per-tenant configuration overrides |
| 14 | [Template Library](14_Template_Library.md) | Reusable configuration templates |
| 15 | [Deployment Model](15_Deployment_Model.md) | How configuration is deployed and promoted across environments |
| — | [Configuration Studio Architecture](Configuration_Studio_Architecture.md) | Cross-cutting platform architecture (lifecycle, versioning, validation, dependency management, deployment, audit, tenant resolution, security, runtime) spanning all designers |

## Governing Principles

- Configuration is data, not code. Configuring PrintOS must never require editing ERPNext core or PrintOS source.
- Every configurable surface is built on top of the Application layer defined in [technical/03_Layer_Architecture.md](../technical/03_Layer_Architecture.md) — designers produce configuration data consumed by use cases, they do not embed business logic themselves.
- Configuration changes with architectural impact must be recorded in `docs/blueprint` and `docs/decisions`.

## Terminology Note

This document set uses "tenant" informally to describe a future multi-tenant SaaS operating mode. The currently Approved anchor for data/configuration scoping is ERPNext **Company** (`docs/standards/Naming_Registry.md`, Section 8), per [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md). The relationship between "Tenant" and "Company" is an open, Pending-ADR item (Naming Registry Section 27, item 11) and is **not** resolved by this document set. See [13_Tenant_Customization.md](13_Tenant_Customization.md) for the full note.

## Related

- [Technical documentation](../technical/00_Master_Index.md)
- [Database documentation](../database/00_Master_Index.md)
- [Blueprint — Bounded Contexts](../blueprint/06_Bounded_Contexts.md)
- [Blueprint — PrintOS Modules](../blueprint/09_PrintOS_Modules.md)
- [Naming Registry](../standards/Naming_Registry.md)
- [ADR-006 — Multi-Tenant Strategy](../decisions/ADR-006-MultiTenant-Strategy.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-25 | Configuration Studio Architecture | Added `Configuration_Studio_Architecture.md` — the cross-cutting architecture layer defining lifecycle, versioning, validation, dependency management, deployment, audit, tenant resolution, security, and runtime behavior shared by all designers. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
