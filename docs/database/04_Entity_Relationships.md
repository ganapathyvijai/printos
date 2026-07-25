# 04 — Entity Relationships

## Purpose

Describe how PrintOS entities relate to one another and to ERPNext's existing data model.

## Relationship Types

| Type | Frappe mechanism | Use when |
|------|--------------------|----------|
| Association | Link field | Entity references another independently-existing entity (e.g. Print Job → Customer) |
| Composition | Child Table | Entity is owned by and cannot exist without its parent (e.g. Estimate → Estimate Line) |
| Dynamic association | Dynamic Link | Entity may reference one of several DocTypes (used sparingly, only when justified) |

## Core Relationships (Illustrative)

```
Customer (ERPNext) ──1:N──▶ Print Job (PrintOS)
Print Job ──1:N (child table)──▶ Estimate Line
Print Job ──N:1──▶ Print Specification
Estimate Line ──N:1──▶ Material
Production Order ──N:1──▶ Print Job
Production Order ──N:1──▶ Machine
```

This diagram is illustrative and must be kept in sync with `docs/blueprint` as the actual schema evolves.

## Rules

- Every relationship must have a clear owning direction; avoid bidirectional duplication of foreign keys.
- Cross-module relationships must respect [technical/06_Module_Communication.md](../technical/06_Module_Communication.md) — a Link field is a data relationship, not a bypass of Application-layer boundaries for behavior.
- Avoid deep Link chains (Link → Link → Link) inside hot-path queries; consider denormalized reporting reads for performance, decided explicitly and documented.

## Related Documents

- [02_DocType_Strategy.md](02_DocType_Strategy.md)
- [03_Master_Data.md](03_Master_Data.md)
