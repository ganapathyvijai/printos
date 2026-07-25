# 03 — Master Data

## Purpose

Identify PrintOS's core master/reference data entities, their ownership, and the rules governing how they are maintained.

## Core Master Data Entities

| Entity | Description | Owner |
|--------|--------------|-------|
| Print Specification | Defines a printable product's technical spec (size, stock, finish) | `printos_core` |
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
