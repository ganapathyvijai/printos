# 04 — Dependency Rules

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-26

---

## Purpose

Codify the allowed and forbidden dependency directions between layers and modules, so architecture erosion is caught early (in review, not in production).

## The Dependency Rule

Dependencies always point inward: **Interface → Application → Domain**, with **Infrastructure** implementing Application-defined ports.

## Allowed Dependencies

| From | May depend on |
|------|----------------|
| Domain | Nothing (standard library only) |
| Application | Domain |
| Infrastructure | Application, Domain |
| Interface | Application |

## Forbidden Dependencies

- Domain → Application, Infrastructure, or Interface (never).
- Application → Infrastructure or Interface directly (must go through ports).
- Any layer → ERPNext core internals (customization only, never core modification).
- Circular dependencies between modules within `printos_core`.

## Enforcement

- Code review must reject imports that violate the table above.
- `frappe` and `erpnext` imports are permitted only in Infrastructure and Interface layers.
- New third-party dependencies require a decision recorded in `docs/decisions` before use.

## Module-Level Rules

- Cross-module communication happens through defined interfaces/events, not direct reach-into-internals (see [06_Module_Communication.md](06_Module_Communication.md)).
- Shared kernel code (common value objects, constants) lives in a clearly named shared module, not duplicated per module.

## Related Documents

- [02_Clean_Architecture.md](02_Clean_Architecture.md)
- [03_Layer_Architecture.md](03_Layer_Architecture.md)
- [06_Module_Communication.md](06_Module_Communication.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-26 | Metadata Normalization | Added document-control metadata (Version, Status, Owner, Last Updated, Revision History). No architectural or technical content changed. |
