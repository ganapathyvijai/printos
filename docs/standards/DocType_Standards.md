# DocType Standards

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

Define conventions for designing ERPNext DocTypes within `printos_core` so that they remain consistent, upgrade-safe, and aligned with the domain model.

---

# Scope

Covers naming, structure, and lifecycle conventions for DocTypes created for PrintOS. Does not cover the business meaning of entities (see `docs/blueprint/05_Domain_Model.md` and `08_Master_Data_Model.md`) or database internals (see [Database_Standards.md](Database_Standards.md)).

---

# Standards

- DocType names use PascalCase with spaces, matching business entity names from the Domain Model (e.g., `Job Card`, `Machine Profile`).
- Every new DocType belongs to a Module that maps to a bounded context defined in `docs/blueprint/06_Bounded_Contexts.md`.
- Custom Fields on ERPNext core DocTypes are used only when extending an existing core concept (e.g., adding print-specific fields to Customer); new business concepts get their own DocType inside `printos_core`.
- Child Tables are used for compositions (e.g., Job Card line items), not for independent entities that need their own lifecycle.
- Every DocType must define appropriate Permissions/Roles consistent with [Security_Standards.md](Security_Standards.md).
- Naming of fields follows snake_case internally, with human-readable labels for UI display.
- DocTypes must not directly reference ERPNext core internals beyond supported Link fields and standard hooks.

---

# Rationale

Aligning DocType boundaries with bounded contexts keeps the technical structure of `printos_core` traceable back to the business architecture in the Blueprint, preventing DocType sprawl or duplicated concepts across modules.

---

# Related Documents

- [Database_Standards.md](Database_Standards.md)
- [Naming_Standards.md](Naming_Standards.md)
- `docs/blueprint/05_Domain_Model.md`
- `docs/blueprint/06_Bounded_Contexts.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
