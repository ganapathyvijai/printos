# 05 — Form Designer

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

Define how form layout, field visibility, and field-level behavior are configured for PrintOS documents, on top of Frappe's native form/field customization mechanisms.

## Concept

The Form Designer is a controlled layer over Frappe's Customize Form / Client Script capabilities, scoped so that per-installation form customization doesn't drift into uncontrolled scripting.

## Capabilities

- Show/hide fields and sections per role or condition.
- Reorder fields within existing sections (structural layout changes still go through standard DocType/Customize Form definitions).
- Mark fields as mandatory/read-only conditionally.
- Attach configuration-driven field-level validation messages (informational only — actual validation enforcement remains in Domain/Application layers).

## Rules

- The Form Designer never becomes a place to embed business logic; it only controls presentation and visibility.
- Any validation exposed through the Form Designer must mirror a rule already enforced server-side in the Domain layer — client-side-only rules are never authoritative (see [../technical/08_Error_Handling.md](../technical/08_Error_Handling.md)).
- Form customizations are stored as configuration records, exported/imported via fixtures for consistent deployment across environments (see [15_Deployment_Model.md](15_Deployment_Model.md)).

## Related Documents

- [06_Dashboard_Designer.md](06_Dashboard_Designer.md)
- [09_Role_Permission_Designer.md](09_Role_Permission_Designer.md)
- [../database/05_Naming.md](../database/05_Naming.md)

---

# Future Considerations

- Form Designer configuration must support Industry Templates / Configuration Packages (pre-built form layouts per print-industry vertical) as an additive layer without changing how field visibility rules are evaluated.

---

# Open Questions

- Should field-level validation messages configured here be required to reference a specific Domain-layer rule identifier, to guarantee they can never drift out of sync with server-side enforcement?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Reworded "tenant-level form customization" to "per-installation form customization" pending resolution of the Tenant/Company naming conflict (see [01_Configuration_Architecture.md](01_Configuration_Architecture.md)). Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
