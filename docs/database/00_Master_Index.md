# Database Documentation — Master Index

Master index for PrintOS data and database architecture documentation. These documents define how PrintOS models, stores, and manages data on top of the ERPNext/Frappe data layer.

## Contents

| # | Document | Purpose |
|---|----------|---------|
| 01 | [Data Architecture](01_Data_Architecture.md) | How PrintOS structures data on the Frappe/ERPNext data layer |
| 02 | [DocType Strategy](02_DocType_Strategy.md) | When and how to create/extend DocTypes |
| 03 | [Master Data](03_Master_Data.md) | Core reference/master data entities and ownership |
| 04 | [Entity Relationships](04_Entity_Relationships.md) | Relationships between PrintOS entities |
| 05 | [Naming](05_Naming.md) | Naming conventions for doctypes, fields, and records |
| 06 | [Data Lifecycle](06_Data_Lifecycle.md) | Creation, mutation, archival, and deletion rules |
| — | [Business Entity Inventory](Business_Entity_Inventory.md) | Canonical, ERPNext-agnostic catalog of every business entity; primary input to future ERPNext DocType Mapping |
| — | [ERPNext DocType Mapping](ERPNext_DocType_Mapping.md) | Authoritative implementation ownership assignment (Native/Extended ERPNext, Custom PrintHub, External Plugin, or Pending Architecture Review) for every business entity |

## Governing Principles

- ERPNext is the framework; PrintOS is the product. Data modeling never modifies ERPNext core doctypes.
- All custom data structures live inside `printos_core`.
- Every data architecture decision must be reflected in `docs/blueprint` before implementation.
- Development follows: **Plan → Verify → Execute → Review**.

## Related

- [Technical documentation](../technical/00_Master_Index.md) — layer architecture, module communication, and implementation guidelines that data design must remain consistent with.
