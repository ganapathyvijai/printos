# 02 — DocType Strategy

Version:
1.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-20

---

## Purpose

Define when to create a new DocType, when to extend an existing ERPNext DocType, and how PrintOS DocTypes are organized.

## Decision Rules

1. **Does ERPNext already model this concept?**
   - Yes, and PrintOS only needs extra fields → add a **Custom Field**, not a new DocType.
   - Yes, but PrintOS needs a distinct lifecycle/behavior → create a **new PrintOS DocType** that links to the ERPNext DocType rather than duplicating its data.
   - No → create a **new PrintOS DocType** inside `printos_core`.
2. **Never** edit an ERPNext core DocType's JSON definition directly.

## DocType Categories in PrintOS

| Category | Example | Notes |
|----------|---------|-------|
| Transactional | Print Job, Production Order | Submittable, workflow-driven |
| Master/Reference | Product Template, Material, Machine | See [03_Master_Data.md](03_Master_Data.md) |
| Child Table | Estimate Line, Job Material Line | Always owned by a parent transactional doctype |
| Configuration/Settings | PrintOS Settings | Single doctype, site-wide configuration |

## Structural Rules

- Every custom DocType lives under `printos_core/infrastructure/<module>/doctype/`, consistent with [technical/05_Project_Structure.md](../technical/05_Project_Structure.md).
- Business logic does not live in the DocType controller (`.py` file) beyond thin delegation — controllers call into Application-layer use cases.
- Child tables model composition (owned data); Link fields model association (referenced data). Do not use a Link where the data is truly owned/composed.

## Naming

DocType names use `PascalCase` with spaces (Frappe convention), e.g. `Print Job`, `Estimate Line`. See [05_Naming.md](05_Naming.md) for full conventions.

## Related Documents

- [01_Data_Architecture.md](01_Data_Architecture.md)
- [03_Master_Data.md](03_Master_Data.md)
- [../technical/09_Extensibility_Model.md](../technical/09_Extensibility_Model.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-26 | Metadata Normalization | Added document-control metadata (Version, Status, Owner, Last Updated, Revision History). No architectural or technical content changed. |
| 1.1 | 2026-09-20 | AR-003 Disposition Synchronization | Replaced "Print Specification" with **Product Template** in the Master/Reference DocType Category example row, following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003's Resolved disposition (2026-09-19): "Print Specification" is Not Adopted; its underlying need maps to Product Template, Job Types, Finishing Types, and Paper Sizes. No other example, category, decision rule, or structural rule was changed. |
