# 09 — Role & Permission Designer

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

Define how roles, permissions, and access rules are configured for PrintOS, building on Frappe's native Role/Permission system rather than replacing it.

## Concept

The Role & Permission Designer provides a PrintOS-friendly UI over Frappe's Role Permission Manager and adds PrintOS-specific access concepts (e.g. module-level access, workflow-step-level access) as configuration.

## Data Model (Illustrative)

Built primarily on ERPNext/Frappe native constructs:
- **Role** — standard Frappe Role.
- **Role Permission** — standard Frappe DocType-level permission (read/write/create/delete/submit/cancel).
- **PrintOS Field Permission** (custom, where needed) — field-level visibility beyond what Customize Form covers.

## Rules

- Permission configuration never modifies ERPNext core permission definitions directly — PrintOS-specific roles and permissions are added, ERPNext's own roles remain untouched.
- Every new PrintOS role must have documented intent (who it represents, what it's for) recorded alongside the role definition.
- Sensitive actions (approval steps, financial adjustments) must be gated by role, not by convention alone.
- Permission changes affecting production environments follow the same environment promotion path as other configuration (see [15_Deployment_Model.md](15_Deployment_Model.md)).

## Relationship to Other Designers

- Approval Designer steps reference roles defined here (see [04_Approval_Designer.md](04_Approval_Designer.md)).
- Dashboard visibility and Form Designer field visibility both key off role/permission data defined here.

## Related Documents

- [04_Approval_Designer.md](04_Approval_Designer.md)
- [05_Form_Designer.md](05_Form_Designer.md)
- [06_Dashboard_Designer.md](06_Dashboard_Designer.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)

---

# Future Considerations

- Role definitions must remain extensible to the future user groups already registered in the Naming Registry (Section 10): Freelancer (G3), Supplier (G4), Service Engineer (G5), and Public Customer (G1, Marketplace) — without a redesign of the Role & Permission Designer's data model.

---

# Open Questions

- Should PrintOS-specific roles introduced through this designer require Architecture Review (Level 2, per Naming Registry Section 3) before being marked Approved, given their cross-cutting security impact?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Reworded "production tenants" to "production environments" pending resolution of the Tenant/Company naming conflict. Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
