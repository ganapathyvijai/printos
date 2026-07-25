# 10 — Report Designer

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

Define how reports are configured in PrintOS, so reporting stays consistent, permissioned, and performant rather than a collection of ad-hoc queries.

## Concept

The Report Designer builds on Frappe's native Report (Query Report / Script Report) framework, adding PrintOS-specific report categories and permission scoping.

## Report Types

| Type | Use case | Notes |
|------|----------|-------|
| Standard Report | Simple filter/list-based reporting on a single DocType | No custom query needed |
| Query Report | Parameterized read-only SQL, reviewed and version-controlled | Must use parameterized queries only |
| Script Report | Python-driven report requiring computation beyond SQL | Business logic delegated to Application-layer use cases, not embedded ad hoc |

## Rules

- Any Query/Script Report using SQL must use parameterized queries — never string-interpolated SQL (security requirement, see [../database/01_Data_Architecture.md](../database/01_Data_Architecture.md)).
- Reports respect the same role/permission rules as the underlying DocTypes — a report is never a way to bypass permission scoping (see [09_Role_Permission_Designer.md](09_Role_Permission_Designer.md)).
- Reports feeding dashboards (see [06_Dashboard_Designer.md](06_Dashboard_Designer.md)) should be designed with caching in mind if they aggregate large datasets.

## Related Documents

- [06_Dashboard_Designer.md](06_Dashboard_Designer.md)
- [09_Role_Permission_Designer.md](09_Role_Permission_Designer.md)
- [../database/01_Data_Architecture.md](../database/01_Data_Architecture.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)

---

# Future Considerations

- Report categories should be extensible to Analytics/MachineIQ-driven reports (per `09_PrintOS_Modules.md`'s Analytics module) without requiring a new report type beyond Script Report.

---

# Open Questions

- Should a custom report builder (noted as a Future Enhancement in `09_PrintOS_Modules.md`'s Reports module) be delivered as an extension of this Report Designer, or as a separate future designer?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist to match the Blueprint documentation template. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
