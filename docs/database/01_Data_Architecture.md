# 01 — Data Architecture

## Purpose

Describe how PrintOS structures its data on top of the Frappe/ERPNext data layer, and how this fits the Clean Architecture layering defined in the technical documentation.

## Foundation

- **Storage engine:** MariaDB/MySQL, managed entirely through the Frappe ORM. No raw schema edits outside Frappe's migration mechanisms.
- **Unit of data:** The DocType — Frappe's combined schema + permission + behavior definition.
- **PrintOS data** lives in DocTypes owned by `printos_core`; ERPNext-owned DocTypes are referenced, never altered in place.

## Data Ownership Model

| Data category | Owner | Notes |
|----------------|-------|-------|
| ERPNext standard data (Customer, Item, Sales Order, etc.) | ERPNext core | Referenced via standard links; extended only via Custom Fields |
| PrintOS domain data (Print Job, Estimate, Production Order, etc.) | `printos_core` | Fully owned custom DocTypes |
| Shared/master data (materials, machines, print specifications) | `printos_core` | See [03_Master_Data.md](03_Master_Data.md) |

## Layer Alignment

Persistence access is confined to the Infrastructure layer (see [technical/03_Layer_Architecture.md](../technical/03_Layer_Architecture.md)). Domain and Application layers work with plain objects/DTOs; repository implementations translate to/from Frappe documents.

## Data Access Rules

- All reads/writes go through Frappe's ORM (`frappe.get_doc`, `frappe.db.*` APIs) — never raw SQL unless a documented performance exception exists.
- Any raw SQL usage must be reviewed and recorded as a decision in `docs/decisions`.
- Bulk/reporting queries may use `frappe.db.sql` with parameterized queries only (never string-interpolated SQL, to prevent injection).

## Related Documents

- [02_DocType_Strategy.md](02_DocType_Strategy.md)
- [04_Entity_Relationships.md](04_Entity_Relationships.md)
- [../technical/09_Extensibility_Model.md](../technical/09_Extensibility_Model.md)
