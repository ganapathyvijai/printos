# ADR-019: Quality and Maintenance Module Status

Status:
Accepted

Date:
2026-09-26

---

## Context

`docs/blueprint/09_PrintOS_Modules.md:130-141` lists "quality checkpoints" and "quality checkpoint recording" as a **Job Cards** responsibility/feature; no standalone "Quality" module section exists anywhere in that document. `docs/blueprint/06_Bounded_Contexts.md:109-110` lists "Quality Check Record" as one of Job Cards' own Owned Business Objects. `docs/architecture/ERPNext_Fit_Analysis.md:426-444` analyzes "Quality Control" as a requested term, classifying it **Customize** (as part of Job Cards) and marking a standalone "Quality"/"Quality Control" module **Future (pending ADR)**. `docs/standards/Naming_Registry.md` Section 27 item 9 and `docs/standards/Naming_Registry.md:214-215` bundle **both** "Quality" and "Maintenance" as new modules "not yet in Blueprint," each **Pending ADR**. `docs/decisions/00_ADR_Index.md`'s "Pending Terminology Not Yet Covered by an ADR" table, item #9, reflects the same bundling. `docs/decisions/Architecture_Review_Register.md` AR-009 ("Quality Module Status") tracks only the Quality half of this question; no register item exists for "Maintenance" as its own subject. The only "Maintenance" references anywhere in the governed documentation corpus are: (a) ERPNext's generic Assets/Maintenance capability, already mapped to Machine and resolved via AR-004 (`ERPNext_Fit_Analysis.md:72`); and (b) "MachineIQ-driven predictive maintenance and scheduling optimization," listed as a Future Enhancement under Machine Scheduling (`09_PrintOS_Modules.md:154`), and a corresponding "Maintenance Alert" entity scoped as an External Plugin/Future MachineIQ concept (`Business_Entity_Inventory.md:587-593`, `ERPNext_DocType_Mapping.md:766`). No standalone "Maintenance" module has ever been proposed, analyzed, or scoped as a Blueprint module in its own right.

## Problem Statement

Whether Quality Check Record should remain a Job Cards sub-feature or be extracted into a standalone "Quality" Blueprint module, and whether a standalone "Maintenance" Blueprint module should be adopted — closing both halves of Naming Registry Section 27 item 9 and Architecture Review Register AR-009.

## Decision Drivers

- No Blueprint content, business requirement, or Architecture/Business Review currently argues for extracting Quality Check Record out of Job Cards.
- Every downstream document (Fit Analysis, Gap Analysis, DocType Mapping, Business Entity Inventory, Module Dependency Matrix) already independently and consistently treats Quality Check Record as a stable Custom Job Cards child entity, and none flags it as a practical implementation blocker.
- No standalone "Maintenance" module has ever been proposed or scoped anywhere in governed Blueprint content; the only existing "maintenance" concept is MachineIQ's predictive-maintenance Future Enhancement, already recorded and unaffected by this decision.
- Naming Registry Section 27's own governing rule requires a Naming Authority decision via ADR before resolution of any item in that Matrix, including item 9.
- Precedent: AR-003 mapped or rejected several requested module names (Print Specification, Approval Management, Production Workflow, Machine Management, Quality Control, Production Orchestration) without adopting any of them as new modules — the same pattern applies here.

## Options Considered

1. Proactively design Quality Check Record as a loosely-coupled capability now, anticipating eventual extraction into a standalone module. Rejected: no Blueprint content or business requirement currently justifies anticipatory decoupling work, and it would itself require a scoped implementation-authorization decision not currently contemplated by the register.
2. Adopt a standalone "Quality" Blueprint module now, extracting Quality Check Record out of Job Cards. Rejected: contradicts every downstream document's existing, consistent treatment of Quality Check Record as a stable Job Cards child entity, and no Architecture or Business Review has been performed to justify a structural extraction.
3. Adopt a standalone "Maintenance" Blueprint module now. Rejected: no Blueprint module, bounded context, or business capability analysis has ever proposed one; the only existing maintenance-adjacent concept (MachineIQ predictive maintenance) is already scoped as a Future Enhancement, not a Phase 1 module.
4. **Selected — confirm current scope for both halves of item 9, reject both standalone modules:**
   - **Quality:** Quality Control remains a Job Cards capability at the current product scope; Quality Check Record remains a Custom PrintOS child entity owned by Job Cards; no standalone Quality Blueprint module is adopted.
   - **Maintenance:** no standalone Maintenance Blueprint module is adopted; MachineIQ predictive maintenance remains a Future Enhancement only, exactly as already recorded in `09_PrintOS_Modules.md:154`.

## Decision

| Question | Disposition | Notes |
|---|---|---|
| Quality Control (label/workflow) | Permitted | Ordinary workflow/process label and embedded Job Card capability, per `Naming_Registry.md` Section 40 — unchanged by this ADR. |
| Quality Check Record (entity) | Custom PrintOS child entity owned by Job Cards | Unchanged — no DocType, ownership, or schema change. |
| Standalone "Quality" Blueprint module | **Not Adopted** | Job Cards continues to own quality-checkpoint functionality; no extraction performed or authorized. |
| Standalone "Maintenance" Blueprint module | **Not Adopted** | No Blueprint module created; predictive maintenance remains a MachineIQ Future Enhancement only, unchanged from existing Blueprint content. |

**Explicit non-creation of a Quality module, Quality bounded context, or Quality DocType.** This ADR creates none of these. It grants no implementation authorization, and claims no milestone completion, runtime validation, or Publication.

## Boundaries

This ADR resolves the standalone-module-status question for both "Quality" and "Maintenance" only (Naming Registry Section 27 item 9, both halves). It does not resolve, and explicitly leaves separate and Pending ADR, the distinct entity-naming question of "Quality Check Record" versus "Quality Record" (`Naming_Registry.md:309`, Business Vocabulary cross-reference) — that question concerns the entity's exact name, not whether Quality is a standalone module, and is outside this ADR's Dependencies. It does not modify `09_PrintOS_Modules.md` or `06_Bounded_Contexts.md`, since no module, context, or owned-object membership changes as a result of this decision.

## Consequences

- Resolves `Naming_Registry.md` Naming Decision Matrix item #9 (both halves).
- Resolves `Architecture Review Register` AR-009.
- Requires downstream terminology synchronization in `ERPNext_Fit_Analysis.md`, `ERPNext_Gap_Analysis.md`, `ERPNext_DocType_Mapping.md`, `Business_Entity_Inventory.md`, `Module_Dependency_Matrix.md`, `Architecture_Freeze.md`, `01_Development_Roadmap.md`, `Documentation_Status.md`, and `Documentation_Map.md` — performed as a separate documentation-synchronization task, not by this ADR itself, per the ADR-016/ADR-017/ADR-018 precedent.
- Unlike ADR-017 and ADR-018, this decision requires **no** bounded terminology synchronization to any Group A Frozen document — `06_Bounded_Contexts.md` and `09_PrintOS_Modules.md` are both unaffected, since Quality Check Record's ownership and Job Cards' feature list do not change.
- The "Quality Check Record" vs. "Quality Record" naming question remains separate and Pending ADR, unaffected by this decision.

## Alternatives Rejected

- Option 1 (proactive loosely-coupled design) — rejected per Options Considered, item 1.
- Option 2 (adopt standalone Quality module now) — rejected per Options Considered, item 2.
- Option 3 (adopt standalone Maintenance module now) — rejected per Options Considered, item 3.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #9 and Architecture Review Register AR-009.
2. In this same documentation-synchronization task: `Naming_Registry.md` Section 27 item #9 is marked Resolved, citing this ADR, along with its comparison-table "Quality"/"Maintenance" rows and its Section 40 "Quality Control" disposition row; `00_ADR_Index.md` registers this ADR and removes item #9 from its Pending table; `Architecture_Review_Register.md` AR-009 moves to Resolved; downstream architecture, mapping, dependency, freeze, roadmap, and documentation-status/map documents are synchronized to reflect the Resolved status.
3. This ADR does not perform those edits itself.

## Related Documents

- `docs/blueprint/09_PrintOS_Modules.md` (Job Cards module, quality checkpoints)
- `docs/blueprint/06_Bounded_Contexts.md` (Job Cards, Owned Business Objects — Quality Check Record)
- `docs/architecture/ERPNext_Fit_Analysis.md` (Section 4, Quality Control)
- `docs/architecture/ERPNext_Gap_Analysis.md` (Quality Check Processing)
- `docs/database/ERPNext_DocType_Mapping.md` (Quality Check Record entry)
- `docs/database/Business_Entity_Inventory.md` (Quality Check Record entry)
- `docs/decisions/Architecture_Review_Register.md` (AR-009)
- `docs/standards/Naming_Registry.md` (Section 27, item #9; Section 40, "Quality Control" row)

## Related ADRs

- [ADR-003-Documentation-First.md](ADR-003-Documentation-First.md) (precedent for the "Permitted label, not Approved module" pattern applied to Production Workflow and Approval Management)
- [ADR-014-Production-Terminology.md](ADR-014-Production-Terminology.md) (Job Card ownership and module-boundary precedent)
- [ADR-017-Purchasing-Procurement-Terminology.md](ADR-017-Purchasing-Procurement-Terminology.md) and [ADR-018-Dispatch-Delivery-Terminology.md](ADR-018-Dispatch-Delivery-Terminology.md) (precedent for Section 27 closure via a numbered ADR; this ADR differs in requiring no companion Group A Frozen document correction)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-09-26|Initial|Initial Version — Project Owner acceptance confirming Quality Control remains a Job Cards capability, Quality Check Record remains a Custom PrintOS child entity owned by Job Cards, no standalone Quality Blueprint module is adopted, and no standalone Maintenance Blueprint module is adopted (MachineIQ predictive maintenance remains a Future Enhancement only), formally resolving Naming Registry Section 27 item #9 and Architecture Review Register AR-009.|

---

# Quality Checklist

- [x] Problem Statement clearly scoped
- [x] Decision Drivers stated
- [x] Options Considered documented, including rejected options
- [x] Decision is unambiguous, with explicit per-question table
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Blueprint/Registry edits)
- [x] Related Documents and Related ADRs cross-referenced
- [x] Reviewed by Project Owner
