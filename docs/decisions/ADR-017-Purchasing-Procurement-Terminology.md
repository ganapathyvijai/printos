# ADR-017: Purchasing and Procurement Terminology

Status:
Accepted

Date:
2026-09-26

---

## Context

`docs/blueprint/09_PrintOS_Modules.md` names the module "Purchasing" (`:51,169`); `docs/blueprint/06_Bounded_Contexts.md` names the corresponding bounded context "Procurement" (`:126`). `docs/standards/Naming_Registry.md`'s Module Registry table (`:204-211`) records "Purchasing" as the current Blueprint-Approved term, with "Procurement" as a competing requested term, status Pending ADR; the same conflict is tracked at the Naming Decision Matrix item #3 (`:706`) and the Synonym Registry (`:675`). `docs/architecture/ERPNext_Fit_Analysis.md:159` independently flagged this as a pre-existing, unresolved conflict, recommending ADR resolution before further propagation into implementation artifacts. `docs/decisions/Architecture_Review_Register.md` AR-007 tracks the same question.

## Problem Statement

Which single term — Purchasing or Procurement — is canonical at both the module and bounded-context level, and how do the two documents' internal cross-references (Inventory's and Warehouse's Inputs/Outputs, which name the other context as an upstream/downstream partner) get reconciled to it.

## Decision Drivers

- Single meaning per term, no synonyms in active use (`Naming_Registry.md` Section 2).
- The Module Registry table (`:204-211`) already records "Purchasing" as the Blueprint-Approved module name; "Procurement" is recorded there only as a competing requested term.
- ADR-012's established module-naming rule: "A module's name matches its bounded context's name exactly," already applied to every sibling module (CRM, Sales, Inventory, Accounts, HR, Administration, Dispatch).
- ERPNext native object names (Purchase Order, Purchase Receipt, Request for Quotation, Supplier) are unaffected by this decision at any layer.

## Options Considered

1. **Selected — "Purchasing" canonical** at both module and bounded-context level, updating `06_Bounded_Contexts.md` and its internal cross-references.
2. Standardize on "Procurement," updating `09_PrintOS_Modules.md` instead. Rejected: would require flipping the Naming Registry Module Registry table's own Approved-column value, which currently already reads "Purchasing," not "Procurement."

## Decision

| Layer | Approved Term |
|---|---|
| Module name | Purchasing (unchanged) |
| Bounded Context name | Purchasing (supersedes "Procurement") |
| ERPNext native objects | Purchase Order, Purchase Receipt, Request for Quotation, Supplier — **unaffected, unchanged** |

## Boundaries

This ADR resolves the module/context label only. It does not rename, restructure, or reclassify any ERPNext DocType, and does not authorize implementation, runtime validation, or Publication.

## Consequences

- Resolves `Naming_Registry.md` Naming Decision Matrix item #3.
- Resolves `Architecture Review Register` AR-007.
- Requires downstream terminology synchronization in `06_Bounded_Contexts.md` (context header and cross-references) and `09_PrintOS_Modules.md` (removing its own stray "Procurement" cross-references) — performed as a separate documentation-synchronization task, not by this ADR itself, per the ADR-012/ADR-014/ADR-016 precedent.
- `06_Bounded_Contexts.md` is a Group A Frozen document under `Architecture_Freeze.md`. The Project Owner has authorized this terminology correction as a bounded terminology synchronization, with a Consistency Review and Documentation Governance check applied, without a fresh Architecture Review or Business Review, on the basis that renaming a label does not introduce new normative content.

## Alternatives Rejected

- Option 2 (standardize on "Procurement") — rejected per Options Considered, item 2.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #3 and Architecture Review Register AR-007.
2. In this same documentation-synchronization task: `Naming_Registry.md` Section 27 item #3 is marked Resolved, citing this ADR; `00_ADR_Index.md` registers this ADR and removes item #3 from its Pending table; `Architecture_Review_Register.md` AR-007 moves to Resolved; `06_Bounded_Contexts.md` and `09_PrintOS_Modules.md` are corrected to use "Purchasing" consistently; downstream architecture, mapping, and roadmap documents are synchronized to reflect the Resolved status.
3. This ADR does not perform those edits itself.

## Related Documents

- `docs/blueprint/06_Bounded_Contexts.md` (Procurement context, corrected to Purchasing)
- `docs/blueprint/09_PrintOS_Modules.md` (Purchasing module)
- `docs/architecture/ERPNext_Fit_Analysis.md` (Section 3, Purchasing)
- `docs/database/ERPNext_DocType_Mapping.md` (Purchase Order, Supplier entries)
- `docs/decisions/Architecture_Review_Register.md` (AR-007)
- `docs/standards/Naming_Registry.md` (Section 27, item #3)

## Related ADRs

- [ADR-012-Estimating-Terminology.md](ADR-012-Estimating-Terminology.md) (precedent for a Level 2 Naming Decision Matrix item resolved via its own numbered ADR; established the module-name-matches-context-name convention)
- [ADR-016-Item-Material-Product-Template-Mapping.md](ADR-016-Item-Material-Product-Template-Mapping.md) (immediately preceding ADR; same precedent for Section 27 closure requiring a numbered ADR)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-09-26|Initial|Initial Version — Project Owner acceptance of Option A ("Purchasing" canonical at both module and bounded-context level), formally resolving Naming Registry Section 27 item #3 and Architecture Review Register AR-007.|

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
