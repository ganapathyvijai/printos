# 13 — Tenant Customization

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

## Terminology Note

"Tenant" throughout this document is used informally to describe a future multi-tenant SaaS operating mode (per `CHATGPT.md`'s "Future Multi-Tenant SaaS" design principle and [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md)). It is **not** an Approved term in `docs/standards/Naming_Registry.md` — the Registry's currently Approved scoping anchor is ERPNext **Company** (Section 8), and the relationship between "Tenant" and "Company" is explicitly recorded as **Pending ADR** (Naming Registry Section 27, item 11). This document does not resolve that conflict; it uses "tenant" only as a placeholder for whatever scoping unit the eventual ADR defines (most likely Company, or a construct built on top of Company). No Naming Registry entry is being created or bypassed by this usage.

## Purpose

Define how per-tenant configuration overrides work across all the designers (Workflow, Form, Dashboard, Automation, etc.), so multiple print businesses can run distinct configurations on a shared PrintOS codebase.

## Concept

Every configuration surface supports a resolution order:

```
Tenant-specific override  →  Template default (see 14_Template_Library.md)  →  System default
```

Tenant customization changes configuration data only — never code. This is what allows one PrintOS codebase to serve multiple tenants with different workflows, forms, and branding.

## Scope of Tenant Customization

| Customizable | Not customizable per tenant |
|---------------|-------------------------------|
| Workflows, approval chains, form layout, dashboards, automation rules, notification templates, feature flags | Domain business rules, Clean Architecture layering, ERPNext core behavior |

## Rules

- Tenant customization must never require a separate code branch/fork per tenant — if a requested customization can't be expressed as configuration, it is either generalized into the configuration model or rejected and recorded as a decision.
- Tenant-specific configuration is isolated so that one tenant's customization cannot affect another (relevant in both single-tenant-per-site and multi-tenant deployment models — see [15_Deployment_Model.md](15_Deployment_Model.md)).
- Sensitive tenant configuration (credentials, integration secrets) follows the same secure-storage rules as [11_Integration_Designer.md](11_Integration_Designer.md).

## Related Documents

- [01_Configuration_Architecture.md](01_Configuration_Architecture.md)
- [14_Template_Library.md](14_Template_Library.md)
- [15_Deployment_Model.md](15_Deployment_Model.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [ADR-006 — Multi-Tenant Strategy](../decisions/ADR-006-MultiTenant-Strategy.md)
- [../blueprint/01_Project_Vision.md](../blueprint/01_Project_Vision.md)

---

# Future Considerations

- This document must be revised once `docs/blueprint/25_MultiTenant_Architecture.md` is written and the Tenant/Company naming conflict is resolved by ADR — at that point "tenant" here should be replaced with whatever term that ADR approves.
- Tenant-level customization must remain compatible with Industry Templates ([14_Template_Library.md](14_Template_Library.md)) and future Configuration Packages without redesign.

---

# Open Questions

- Should tenant isolation for configuration data be enforced at the Company/tenant-scope field level (per ERPNext's existing Company mechanism) or via a separate, PrintOS-specific isolation mechanism once `25_MultiTenant_Architecture.md` is scoped?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata and an explicit Terminology Note flagging "Tenant" as a Pending-ADR term (Naming Registry Section 27, item 11) rather than an Approved one, per [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md). Added Future Considerations, Open Questions, Revision History/Quality Checklist. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
