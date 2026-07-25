# 06 — Dashboard Designer

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

Define how dashboards and widgets are configured to surface PrintOS data to different roles (management, production floor, sales) without custom-coding a dashboard per installation.

## Concept

The Dashboard Designer composes dashboards from a library of widget types, each backed by a defined data source (report, KPI query, or chart) rather than ad-hoc queries.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Dashboard | Named dashboard, target role/workspace |
| PrintOS Dashboard Widget | Widget instance: type, data source reference, layout position |

## Widget Data Sources

Widgets consume data exclusively through:
- Existing Report Designer reports (see [10_Report_Designer.md](10_Report_Designer.md)), or
- Defined Application-layer query use cases.

Widgets never embed raw SQL directly in configuration — this keeps dashboards consistent with [database/01_Data_Architecture.md](../database/01_Data_Architecture.md)'s rule against unreviewed raw SQL.

## Rules

- Dashboard visibility respects role/permission configuration (see [09_Role_Permission_Designer.md](09_Role_Permission_Designer.md)) — a widget never bypasses the permission a user would have in the underlying report.
- Heavy/aggregate widgets should use cached or scheduled computation where real-time computation would be too slow, documented per widget.

## Related Documents

- [10_Report_Designer.md](10_Report_Designer.md)
- [09_Role_Permission_Designer.md](09_Role_Permission_Designer.md)
- [../blueprint/20_Dashboard_Architecture.md](../blueprint/20_Dashboard_Architecture.md)

---

# Future Considerations

- Widget types should remain extensible so future MachineIQ insight widgets and Marketplace performance widgets can be added as new widget types without changing the Dashboard/Widget data model.

---

# Open Questions

- Should this document be merged into or kept clearly distinct from `Blueprint 20_Dashboard_Architecture.md` (currently a Proposed Blueprint document per Naming Registry Section 27a)? Their scopes currently overlap and should be reconciled once that Blueprint document has content.

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Reworded "per tenant" to "per installation" pending resolution of the Tenant/Company naming conflict. Added header metadata, cross-reference to `Blueprint 20_Dashboard_Architecture.md`, Future Considerations, Open Questions, Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
