# 05 — Naming

## Purpose

Define naming conventions for DocTypes, fields, and records so the data model stays consistent and predictable.

## DocTypes

- `PascalCase` with spaces, per Frappe convention: `Print Job`, `Estimate Line`, `Print Specification`.
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
