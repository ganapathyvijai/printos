# 02 — Clean Architecture

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

# Purpose

Consolidate the cross-cutting rationale for Clean Architecture in PrintOS — why it was chosen, how it is enforced across the whole system, and how it interacts with DDD and multi-tenancy — complementing the layer-by-layer definitions already published in `docs/technical/`.

---

# Scope

Covers the system-wide enforcement mechanisms and trade-offs of Clean Architecture. Does not redefine the layers themselves (see [../technical/02_Clean_Architecture.md](../technical/02_Clean_Architecture.md) and [../technical/03_Layer_Architecture.md](../technical/03_Layer_Architecture.md), which remain authoritative) or dependency direction rules (see [../technical/04_Dependency_Rules.md](../technical/04_Dependency_Rules.md)).

---

# Background

`docs/technical/02_Clean_Architecture.md` establishes what each layer is. This document exists to answer a different question: how do we know, at any point as the codebase grows, that the architecture hasn't quietly eroded? That is an ongoing, cross-cutting concern rather than a one-time layer definition.

---

# Main Content

## Enforcement Mechanisms

| Mechanism | What it catches |
|---|---|
| Code review against [../technical/04_Dependency_Rules.md](../technical/04_Dependency_Rules.md) | Illegal imports (e.g. Domain importing `frappe`) |
| Project structure convention ([../technical/05_Project_Structure.md](../technical/05_Project_Structure.md)) | Misplaced files that make violations easy to spot |
| Test structure (Domain/Application unit tests without a Frappe site; Infrastructure integration tests with one) | A Domain/Application test that unexpectedly requires a live site signals a boundary violation |
| Configuration-as-data model ([../configuration/01_Configuration_Architecture.md](../configuration/01_Configuration_Architecture.md)) | Prevents "no-code" designers from becoming a second, undocumented logic layer outside Clean Architecture |

## Why Clean Architecture Over Alternatives

ERPNext/Frappe is a large, opinionated framework. Without an explicit boundary, business logic tends to accumulate in DocType controllers and client scripts because that is the path of least resistance in Frappe. Clean Architecture is chosen specifically to counteract that gravity, not as an abstract preference — see [ADR-002-PrintOS-Core](../decisions/ADR-002-PrintOS-Core.md).

## Relationship to DDD

Clean Architecture provides the *structural* boundaries (Domain/Application/Infrastructure/Interface); DDD provides the *content* organization within those boundaries (bounded contexts, entities, aggregates). See [03_DDD_Architecture.md](03_DDD_Architecture.md) for how the two combine in `printos_core`.

---

# Architecture Notes

Any perceived need to bend a Clean Architecture rule for convenience (e.g., a quick Domain-layer call into `frappe.db`) is a signal to revisit the design, not a justification for an exception — consistent with [../technical/04_Dependency_Rules.md](../technical/04_Dependency_Rules.md)'s enforcement stance.

---

# Future Considerations

- As the codebase grows, consider whether automated import-linting (e.g., a CI check that fails on `frappe`/`erpnext` imports inside `domain/` or `application/`) should be adopted to make enforcement mechanical rather than review-dependent.

---

# Open Questions

- Should import-linting be introduced now (Phase 1) or deferred until `printos_core` has enough code for the risk of drift to be material?

---

# Related Documents

- [../technical/02_Clean_Architecture.md](../technical/02_Clean_Architecture.md)
- [../technical/03_Layer_Architecture.md](../technical/03_Layer_Architecture.md)
- [../technical/04_Dependency_Rules.md](../technical/04_Dependency_Rules.md)
- [03_DDD_Architecture.md](03_DDD_Architecture.md)
- [../decisions/ADR-002-PrintOS-Core.md](../decisions/ADR-002-PrintOS-Core.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
