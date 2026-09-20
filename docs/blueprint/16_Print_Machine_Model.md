# 16 — Print Machine Model

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-20

---

# Purpose

Records the Architecture Review Register's disposition of AR-004 (Machine Domain Ownership): which ERPNext object, if any, the Approved "Machine" business term (`05_Domain_Model.md`, `08_Master_Data_Model.md`) is built upon. This is the Blueprint-layer record of that architecture decision, required before implementation per `AGENTS.md`. It does not define DocType fields, schema, hooks, or any implementation detail — those remain downstream tasks.

---

# Scope

Covers only the ERPNext-object-base decision for Machine and its relationship to Machine Profile. Does not redefine Machine's business meaning (`05_Domain_Model.md`), its master-data ownership (`08_Master_Data_Model.md`), or Machine Scheduling's status as a Core Domain / Approved module (`05_Domain_Model.md`; `../standards/Naming_Registry.md` Section 11). Does not resolve the separate, still-open Blueprint question of whether Machine Scheduling should be its own bounded context distinct from Production (`06_Bounded_Contexts.md`) — that question is unaffected by this decision.

---

# Decision

Per Project Owner decision dated 2026-09-20 (AR-004, Option C, recorded in `../decisions/Architecture_Review_Register.md`, Version 0.6):

- **Machine** is a **wholly Custom PrintOS DocType**, owned inside `printos_core`. It is not built by extending ERPNext's native Asset or Workstation DocTypes.
- **Machine Profile** remains **Custom**, unaffected by this decision — consistent with its classification prior to this decision.

---

# Consequences

- Machine has no ERPNext-native upgrade-path coupling; it is unaffected by future changes to ERPNext's Assets or Manufacturing modules.
- Machine forgoes any native ERPNext reuse (asset lifecycle tracking, depreciation, or Workstation capacity concepts are not inherited).
- No DocType field, schema, hook, permission, migration, or other implementation-level design is authorized or specified by this record.

---

# What This Document Does Not Do

- Does not authorize implementation, DocType creation, or Publication of any coding specification.
- Does not resolve the Machine Scheduling bounded-context question.
- Does not modify Machine's or Machine Profile's business definition in `05_Domain_Model.md` or `08_Master_Data_Model.md`.
- Does not select or approve a technical DocType name — Naming Registry review remains separate and unperformed by this document.

---

# Related Documents

- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) — AR-004
- [05_Domain_Model.md](05_Domain_Model.md)
- [08_Master_Data_Model.md](08_Master_Data_Model.md)
- [06_Bounded_Contexts.md](06_Bounded_Contexts.md)
- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md)
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-09-20 | AR-004 Option C Disposition Record | Initial population of this previously empty Placeholder (no new file created). Records the Project Owner's AR-004 decision (Option C): Machine is a wholly Custom PrintOS DocType inside `printos_core`; Machine Profile remains Custom, unaffected. No DocType schema, field, hook, or implementation detail is defined. Does not resolve the separate Machine Scheduling bounded-context question. No implementation authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
