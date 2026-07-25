# Coding Standards

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

Define how code inside `printos_core` should be structured and written so it remains readable, testable, and upgrade-safe against ERPNext.

---

# Scope

Covers Python and JavaScript code written for `printos_core`. Does not cover ERPNext core code, which must never be modified.

---

# Standards

- Follow **PEP8** for all Python code (formatting, line length, imports).
- Follow **SOLID** principles: single responsibility per class/module, dependency on abstractions rather than concrete ERPNext internals where feasible.
- Follow **Clean Architecture** layering: Domain logic must not import Frappe/ERPNext modules directly; Infrastructure-layer adapters mediate that dependency (see `docs/blueprint/04_System_Architecture.md`).
- Follow **Domain-Driven Design** vocabulary consistently — code identifiers should match the terminology defined in `docs/blueprint/05_Domain_Model.md`.
- Prefer **composition over inheritance**.
- Keep modules **small and cohesive**; a module should map to one bounded context or a clear sub-part of one (see `docs/blueprint/06_Bounded_Contexts.md`).
- Avoid duplicating business logic — shared rules belong in one place in the Domain layer, not repeated per caller.
- Write **self-documenting code**; comments explain WHY, never WHAT.
- No dead code, commented-out code blocks, or speculative abstractions for hypothetical future needs.

---

# Rationale

These standards exist to protect the ERPNext/PrintOS boundary defined in the Blueprint. Business logic that leaks Frappe dependencies into the Domain layer becomes brittle across ERPNext upgrades — the single greatest long-term risk identified in `CLAUDE.md` and `DECISIONS.md` (ADR-002).

---

# Related Documents

- [Naming_Standards.md](Naming_Standards.md)
- [DocType_Standards.md](DocType_Standards.md)
- [Testing_Standards.md](Testing_Standards.md)
- `docs/blueprint/04_System_Architecture.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
