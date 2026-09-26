# ADR-018: Dispatch and Delivery Terminology

Status:
Accepted

Date:
2026-09-26

---

## Context

`docs/blueprint/09_PrintOS_Modules.md:53,195` names both the module and its Module Summary table entry "Dispatch," with no mismatch at that layer (`docs/standards/Naming_Registry.md:216`, Module Registry table: `| Dispatch | Approved | Dispatch | Match |`). `docs/blueprint/06_Bounded_Contexts.md:150` lists "Owned Business Objects: Dispatch Record, Delivery Note (business concept)" without ever formally distinguishing the two. `docs/architecture/ERPNext_Fit_Analysis.md:451,460,466` classifies the underlying capability as **Extend** against native ERPNext Delivery Note, explicitly recommending "Do not build a parallel Dispatch DocType," and flags the naming conflict as Naming Registry Section 27 item 5, Pending ADR. `docs/database/ERPNext_DocType_Mapping.md:359-365` already maps the business entity "Dispatch Record" to Target DocType "Delivery Note" (Extended ERPNext) — the implementation-facing document has already adopted this disposition in practice, without a formal ADR. `docs/decisions/Architecture_Review_Register.md` AR-008 tracks the same question.

## Problem Statement

How do "Dispatch" (module/context name), "Dispatch Record" (PrintOS business concept), and "Delivery Note" (native ERPNext DocType) relate to one another across the module, business-concept, and ERPNext-DocType layers.

## Decision Drivers

- Single meaning per term at each layer, no synonyms in active use (`Naming_Registry.md` Section 2).
- The Module Registry table (`:216`) already confirms "Dispatch" as Approved and matched at the module/context layer — no ambiguity exists there.
- The Fit Analysis (`:457,466`) already establishes "High" reuse opportunity for Delivery Note and explicitly recommends against a parallel Dispatch DocType.
- The DocType Mapping (`:359-365`) already implements this exact disposition in practice.
- `AGENTS.md`: "Never modify ERPNext core" — the native "Delivery Note" DocType name and structure cannot be altered.

## Options Considered

1. Introduce a distinct "Dispatch Record" DocType separate from Delivery Note. Rejected: contradicts the Fit Analysis's own explicit recommendation (`:466`) and the DocType Mapping's own existing disposition (`:359-365`); would reopen a question already answered without new evidence.
2. Standardize fully on one term across all layers (rename "Delivery Note" itself, or rename "Dispatch" to "Delivery" everywhere). Rejected: ERPNext's native DocType name cannot be renamed without modifying ERPNext core, which `AGENTS.md` prohibits; the "one term across all layers" goal is structurally unachievable.
3. **Selected — three-layer definition, no parallel DocType:**
   - **Module/Bounded-Context layer:** "Dispatch" is the canonical PrintOS module and bounded-context name (already Approved, `Naming_Registry.md:216`).
   - **Business-concept layer:** "Dispatch Record" is the canonical PrintOS business concept — the act and record of delivering finished goods to a Customer.
   - **ERPNext-DocType layer:** native ERPNext "Delivery Note" is the implementation DocType for Dispatch Record, extended only through approved PrintOS Custom Fields, never replaced or duplicated by a parallel DocType.

## Decision

| Layer | Approved Term | Notes |
|---|---|---|
| Module / Bounded Context | Dispatch | Unchanged — already Approved and matched. |
| Business concept | Dispatch Record | The PrintOS-facing name for the delivery-fulfillment record. |
| ERPNext DocType (implementation) | Delivery Note | Native ERPNext object; **no parallel Dispatch DocType is created**; extended only via approved Custom Fields per existing Extend classification. |

**Explicit rejection of a parallel Dispatch DocType.** This ADR affirmatively rejects introducing any Custom "Dispatch" or "Dispatch Record" DocType. Dispatch Record is a business-concept label applied to the native Delivery Note DocType; it is not, and must not become, a separate persisted entity. This matches the Fit Analysis's existing recommendation (`:466`) and the DocType Mapping's existing implementation (`:359-365`) exactly — no reclassification of Implementation Owner, Target DocType, or Customization Required is made or implied.

## Boundaries

This ADR resolves the three-layer terminology question only. It does not define Custom Field names, does not authorize implementation, and does not resolve Naming Registry Section 27 item 15 ("Delivery Partner" vs. "Dispatch" context), which is a distinct, Marketplace-scoped, future question outside this ADR's Dependencies and remains separately Pending ADR.

## Consequences

- Resolves `Naming_Registry.md` Naming Decision Matrix item #5.
- Resolves `Architecture Review Register` AR-008.
- Requires downstream terminology synchronization in `06_Bounded_Contexts.md` (Owned Business Objects wording), `ERPNext_DocType_Mapping.md`, `Canonical_Domain_Model.md`, `Business_Entity_Inventory.md`, `ERPNext_Fit_Analysis.md`, `ERPNext_Gap_Analysis.md`, `Module_Dependency_Matrix.md`, `Architecture_Freeze.md`, `01_Development_Roadmap.md`, `Documentation_Status.md`, and `Documentation_Map.md` — performed as a separate documentation-synchronization task, not by this ADR itself, per the ADR-012/ADR-016/ADR-017 precedent.
- `06_Bounded_Contexts.md` is a Group A Frozen document under `Architecture_Freeze.md`. The Project Owner has authorized this terminology correction as a bounded terminology synchronization, with a Consistency Review and Documentation Governance check applied, without a fresh Architecture Review or Business Review, on the basis that clarifying which layer "Delivery Note" belongs to does not introduce new normative content.
- Naming Registry Section 27 item 15 ("Delivery Partner" vs. "Dispatch" context) remains separate and Pending ADR, unaffected by this decision.

## Alternatives Rejected

- Option 1 (separate Dispatch Record DocType) — rejected per Options Considered, item 1.
- Option 2 (standardize fully on one term across all layers) — rejected per Options Considered, item 2.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #5 and Architecture Review Register AR-008.
2. In this same documentation-synchronization task: `Naming_Registry.md` Section 27 item #5 is marked Resolved, citing this ADR; `00_ADR_Index.md` registers this ADR and removes item #5 from its Pending table; `Architecture_Review_Register.md` AR-008 moves to Resolved; `06_Bounded_Contexts.md`'s Owned Business Objects wording is corrected to an explicit business-layer-vs.-ERPNext-layer statement; downstream architecture, mapping, and roadmap documents are synchronized to reflect the Resolved status.
3. This ADR does not perform those edits itself.

## Related Documents

- `docs/blueprint/06_Bounded_Contexts.md` (Dispatch context, Owned Business Objects)
- `docs/blueprint/09_PrintOS_Modules.md` (Dispatch module)
- `docs/architecture/ERPNext_Fit_Analysis.md` (Section 4, Dispatch)
- `docs/database/ERPNext_DocType_Mapping.md` (Dispatch Record, Delivery Method entries)
- `docs/decisions/Architecture_Review_Register.md` (AR-008)
- `docs/standards/Naming_Registry.md` (Section 27, item #5)

## Related ADRs

- [ADR-016-Item-Material-Product-Template-Mapping.md](ADR-016-Item-Material-Product-Template-Mapping.md) (precedent for a business-concept term implemented on a native ERPNext object without a parallel DocType)
- [ADR-017-Purchasing-Procurement-Terminology.md](ADR-017-Purchasing-Procurement-Terminology.md) (immediately preceding ADR; same precedent for Section 27 closure requiring a numbered ADR and a Group A Frozen document's bounded terminology synchronization)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-09-26|Initial|Initial Version — Project Owner acceptance of the three-layer definition (Dispatch module/context; Dispatch Record business concept; native Delivery Note as the implementation DocType, no parallel Custom DocType), formally resolving Naming Registry Section 27 item #5 and Architecture Review Register AR-008.|

---

# Quality Checklist

- [x] Problem Statement clearly scoped
- [x] Decision Drivers stated
- [x] Options Considered documented, including rejected options
- [x] Decision is unambiguous, with explicit per-layer table
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Blueprint/Registry edits)
- [x] Related Documents and Related ADRs cross-referenced
- [x] Reviewed by Project Owner
