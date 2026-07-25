# Database Standards

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-22

---

# Purpose

Define conventions for how PrintOS interacts with and extends the database layer managed by ERPNext/Frappe, without modifying ERPNext core schema.

---

# Scope

Covers naming, migration, and access conventions for data introduced by `printos_core`. Does not cover ERPNext core tables, and does not define specific field-level schemas (see `docs/blueprint/08_Master_Data_Model.md` for business-level master data).

---

# Standards

- All new persistent data is defined through Frappe DocTypes, never through raw SQL table creation, unless a specific documented exception is approved.
- Table and field naming follows Frappe's own conventions (`tab<DocType>`), kept consistent with [Naming_Standards.md](Naming_Standards.md).
- Schema changes are delivered through Frappe's migration/patch mechanism, never manual production database edits.
- Never modify ERPNext core tables directly; extend via Custom Fields, Custom DocTypes, or child tables owned by `printos_core`.
- Foreign-key-like relationships are expressed via Frappe Link fields, not raw joins against unmanaged tables.
- Any raw SQL used for reporting/performance must be reviewed for injection safety and kept read-only where possible.
- Sensitive fields (e.g., anything tax- or payment-related) must never store secrets directly; use environment variables or Frappe's encrypted password fields.

---

# Rationale

Routing all schema changes through Frappe's supported mechanisms is what keeps PrintOS upgrade-safe against future ERPNext releases — a direct requirement from `CLAUDE.md` and `PROJECT_RULES.md` Rule 23. Bypassing DocType-based schema management for convenience creates hidden upgrade risk.

---

# Related Documents

- [DocType_Standards.md](DocType_Standards.md)
- [Naming_Standards.md](Naming_Standards.md)
- [Security_Standards.md](Security_Standards.md)
- `docs/blueprint/08_Master_Data_Model.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
