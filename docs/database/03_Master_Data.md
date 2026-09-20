# 03 — Master Data

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

Identify PrintOS's core master/reference data entities, their ownership, and the rules governing how they are maintained.

## Core Master Data Entities

| Entity | Description | Owner |
|--------|--------------|-------|
| Product Template | A sellable product definition that references applicable Job Types, Finishing Types, and Paper Sizes | `printos_core` |
| Material | Paper, ink, substrate, and other consumable definitions | `printos_core` |
| Machine | Production equipment and its capabilities | `printos_core` |
| Process | Standard production process/operation definitions | `printos_core` |
| Pricing Rule (Print) | Print-specific pricing/estimation rules | `printos_core` |

ERPNext already provides master data such as Customer, Supplier, Item, Warehouse, UOM — PrintOS reuses these via Link fields rather than duplicating them.

## Ownership Rules

- Each master data DocType has exactly one owning module; other modules reference it via Link fields, never copy its data.
- Master data changes that affect multiple modules must be recorded as a decision in `docs/decisions`.

## Data Quality Rules

- Master data DocTypes require unique, human-readable naming (see [05_Naming.md](05_Naming.md)).
- Master data is never deleted once referenced by transactional records — it is disabled/archived instead (see [06_Data_Lifecycle.md](06_Data_Lifecycle.md)).
- Master data changes go through standard ERPNext permissions; direct database edits are never used.

## Related Documents

- [02_DocType_Strategy.md](02_DocType_Strategy.md)
- [04_Entity_Relationships.md](04_Entity_Relationships.md)
- [06_Data_Lifecycle.md](06_Data_Lifecycle.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-26 | Metadata Normalization | Added document-control metadata (Version, Status, Owner, Last Updated, Revision History). No architectural or technical content changed. |
| 1.1 | 2026-09-20 | AR-003 Disposition Synchronization | Replaced the "Print Specification" Core Master Data Entity row with **Product Template**, following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003's Resolved disposition (2026-09-19): "Print Specification" is Not Adopted; its underlying need maps to Product Template, Job Types, Finishing Types, and Paper Sizes. Product Template's Description records it as "a sellable product definition that references applicable Job Types, Finishing Types, and Paper Sizes," consistent with [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md); ownership remains within `printos_core`. No new field or schema was invented; no other entity row, Ownership Rule, or Data Quality Rule was changed. |
