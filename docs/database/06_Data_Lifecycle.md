# 06 — Data Lifecycle

## Purpose

Define how PrintOS data is created, mutated, archived, and (rarely) deleted, across its lifetime.

## Lifecycle Stages

1. **Creation** — Records are created only through Application-layer use cases (never directly via Desk form scripting for business-critical data), ensuring Domain validation runs consistently.
2. **Mutation** — Updates go through the same use-case path used for creation where business rules apply; direct field patches are reserved for non-business-critical corrections and must be logged.
3. **Submission (where applicable)** — Transactional DocTypes (Print Job, Production Order) use Frappe's submit/cancel workflow to lock finalized records; amendments create a new version rather than mutating a submitted document.
4. **Archival** — Master data and completed transactional records are disabled (`is_active = 0`, `disabled = 1`) or moved to a closed status rather than deleted, preserving referential integrity and audit history.
5. **Deletion** — Hard deletion is reserved for records that were never referenced elsewhere (e.g. a draft created in error) and requires explicit confirmation per the project's destructive-operation policy.

## Data Retention

- Transactional history (jobs, orders, estimates) is retained indefinitely unless a specific retention policy is documented in `docs/decisions`.
- Soft-delete/disable is the default; hard delete is the exception and must never be automated without confirmation.

## Auditability

- Rely on Frappe's built-in version/audit trail (`Version` doctype) for change history — do not build a parallel audit mechanism unless a documented gap requires it.
- Any background job that mutates data at scale (bulk update/migration) must be logged and reviewed before running against production.

## Related Documents

- [03_Master_Data.md](03_Master_Data.md)
- [../technical/08_Error_Handling.md](../technical/08_Error_Handling.md)
