# 05 — Naming

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

Define naming conventions for DocTypes, fields, and records so the data model stays consistent and predictable.

## DocTypes

- `PascalCase` with spaces, per Frappe convention: `Print Job`, `Estimate Line`, `Product Template`.
- Singular nouns: `Print Job`, not `Print Jobs`.
- Child tables are named `<Parent> <Detail>`, e.g. `Estimate Line`, `Job Material Line`.

## Fields (fieldnames)

- `snake_case`: `customer_name`, `print_specification`, `estimated_cost`.
- Link fields are named after the target entity in singular form: `material` (Link to Material), `machine` (Link to Machine).
- Boolean fields are prefixed `is_`/`has_`: `is_active`, `has_finishing`.
- Date/time fields are suffixed `_date` / `_on`: `due_date`, `submitted_on`.

## Naming Series (Record IDs)

- PrintOS transactional documents use descriptive naming series, e.g. `PJ-.YYYY.-.#####` for Print Job, `PO-.YYYY.-.#####` for Production Order.
- Naming series prefixes are documented in `docs/blueprint` when introduced, to avoid collisions across modules.

## Code-Level Naming (cross-reference)

Consistent with [technical/05_Project_Structure.md](../technical/05_Project_Structure.md):
- Python: `snake_case`.
- DocTypes: `PascalCase`.
- JavaScript: `camelCase`.

## Related Documents

- [02_DocType_Strategy.md](02_DocType_Strategy.md)
- [../technical/10_Implementation_Guidelines.md](../technical/10_Implementation_Guidelines.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-26 | Metadata Normalization | Added document-control metadata (Version, Status, Owner, Last Updated, Revision History). No architectural or technical content changed. |
| 1.1 | 2026-09-20 | AR-003 Disposition Synchronization | Replaced the DocType naming example "Print Specification" with **Product Template**, following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003's Resolved disposition (2026-09-19): "Print Specification" is Not Adopted; its underlying need maps to Product Template, Job Types, Finishing Types, and Paper Sizes. No other naming convention, field example, or naming series was changed. |
