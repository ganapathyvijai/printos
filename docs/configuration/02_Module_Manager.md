# 02 — Module Manager

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

## Purpose

Define how PrintOS modules — as catalogued in [Blueprint 09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (CRM, Sales, Estimation, Artwork, Production Planning, Job Cards, Machine Scheduling, Inventory, Purchasing, Warehouse, Dispatch, Accounts, GST, HR, Reports, Analytics, Administration, and the future MachineIQ and Marketplace modules) — are enabled, disabled, and managed per installation.

## Concept

The Module Manager is a configuration surface that controls which PrintOS modules are active for a given site. Disabling a module hides its menus, forms, and automation without deleting its data. Module names here MUST match the Module Registry in `docs/standards/Naming_Registry.md` (Section 11) exactly — this document introduces no new module names of its own.

## Responsibilities

- Enable/disable a module and its dependent Interface-layer entry points (Desk workspaces, portal pages, API endpoints).
- Enforce module dependency rules (a module cannot be enabled if a module it depends on is disabled).
- Surface module version/compatibility information.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Module | Registry of installable modules, their status, and dependencies |
| PrintOS Module Dependency | Child table declaring required modules |

## Rules

- Module enable/disable state is configuration data, read by the Application layer to decide which use cases/Interface routes are active — it never conditionally compiles code.
- Disabling a module must never delete its data; data remains intact and is simply inaccessible through the UI until re-enabled.
- Module dependency violations must be surfaced to the administrator, not silently ignored.

## Related Documents

- [01_Configuration_Architecture.md](01_Configuration_Architecture.md)
- [12_Feature_Flags.md](12_Feature_Flags.md)
- [../technical/06_Module_Communication.md](../technical/06_Module_Communication.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)

---

# Future Considerations

- MachineIQ and Marketplace are registered as Future modules in the Naming Registry (Section 11) and must be addable to the Module Manager without redesign once their Blueprint documents are written.
- "Quality" and "Maintenance" are proposed but not yet Approved modules (Naming Registry, Section 27, item 9); the Module Manager must not register them until they are formally added to `09_PrintOS_Modules.md`.

---

# Open Questions

- Should module dependency declarations (PrintOS Module Dependency) be validated against the Bounded Context map in `06_Bounded_Contexts.md`, or is dependency purely a Module Manager-level concern?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Replaced illustrative module examples ("Estimating, Production, Materials, Sales") with the Approved Module Registry names from `09_PrintOS_Modules.md`/`Naming_Registry.md` Section 11 — "Estimating" and "Materials" were not Approved terms (the Approved names are "Estimation" and "Inventory"/"Material" respectively). Added header metadata, Future Considerations, Open Questions, and Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
