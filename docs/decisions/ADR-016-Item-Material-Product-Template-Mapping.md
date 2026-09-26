# ADR-016: Item, Material, and Product Template Mapping

Status:
Accepted

Date:
2026-09-26

---

## Context

`docs/architecture/ERPNext_Fit_Analysis.md` Section 3 (Inventory) provisionally classified Material/Substrate as "Extend" against ERPNext Item, citing `docs/standards/Naming_Registry.md` Section 27, item #10 ("ERPNext 'Item' vs PrintOS 'Material'/'Product Template'") as Pending ADR. `docs/decisions/Architecture_Review_Register.md` AR-006 tracks the same unresolved question across three distinct concepts — Material, Substrate, and Product Template — none of which shares a documented relationship in the Frozen `docs/blueprint/08_Master_Data_Model.md` relationship map: Product Template descends from Product Category toward Job Types/Finishing Types/Paper Sizes/Price List, while Material descends toward Substrate/Media Profiles/Units of Measure, on two structurally separate branches. `08_Master_Data_Model.md` Open Questions also separately asked whether Substrate is a specialization of Material or an independent entity referencing Material.

## Problem Statement

Whether PrintOS's Material, Substrate, and Product Template concepts map to native ERPNext Item (directly or via Variants) or require distinct Custom objects, and if Custom, how each relates to Item and to one another.

## Decision Drivers

- Single meaning per term, no forced coupling between structurally independent concepts (`Naming_Registry.md` Section 2).
- Preserve the existing, already-modeled Aggregate/Child-Entity structure (`docs/architecture/Canonical_Domain_Model.md`) rather than introduce a relationship the Blueprint's own relationship map does not show.
- GSM/media-profile attributes have no native ERPNext equivalent (`ERPNext_Fit_Analysis.md` Section 3, Inventory).
- Naming Registry Section 27's own governing rule: every conflict in that Decision Matrix requires a Naming Authority decision via ADR before resolution (Section 27 introductory sentence); no Level 2 item in that table has ever closed without a numbered ADR.

## Options Considered

1. **Map Material/Substrate/Product Template to native Item with Item Variant Attributes.** Rejected — Item Variants are generic key-value attributes, not first-class links to Job Types/Finishing Types/Paper Sizes, and Product Template has no Blueprint-documented relationship to Item at all.
2. **Extend Item with Custom Fields, no Variants.** Rejected — insufficient for GSM/media-profile specialization and does not address Product Template's separate catalog role.
3. **Selected — Material is a Custom PrintOS master record linked to native Item; Substrate is a specialization of Material holding GSM/media-profile properties (exact structure deferred); Product Template is its own Custom PrintOS artifact in the Estimation context, independent of Material and Item.** Consistent with Product Template's existing Aggregate Root status and the Blueprint relationship map's own separation of the two branches.

An additional alternative — linking Product Template through Material — was considered and rejected: no Blueprint relationship-map support exists for this coupling (`08_Master_Data_Model.md`'s relationship map shows no Product-Template-to-Material edge), and different Product Templates commonly share the same Material/Substrate, making a direct link structurally inappropriate.

## Decision

| Concept | Disposition | ERPNext Relationship |
|---|---|---|
| Material | Custom PrintOS master record | Linked to native Item |
| Substrate | A specialization of Material | Indirect via Material; exact DocType structure (child table vs. standalone) deferred to downstream design |
| Product Template | Its own Custom PrintOS artifact, Estimation context | Independent of Item and of Material — no Link field assumed by this ADR |
| Item (native) | Unchanged | Remains the native inventory, purchasing, unit-of-measure, and accounting integration record |

## Boundaries

This ADR records the boundary decision only. It does not specify: DocType field lists, the child-table-vs-standalone-DocType structure for Substrate, Custom Field schema on Item, migration mechanics, or hooks. AR-010 (BOM necessity for Estimation) is **not** resolved, referenced as a condition, or reclassified by this ADR; it remains Open and unchanged, tracked separately in the Architecture Review Register.

## Consequences

- Resolves `docs/standards/Naming_Registry.md` Naming Decision Matrix item #10.
- Resolves `docs/blueprint/08_Master_Data_Model.md`'s Open Question on Substrate's specialization of Material, as a business-model matter only; that document's own Frozen classification under `docs/implementation/Architecture_Freeze.md` is unaffected.
- Removes item #10 from `docs/decisions/00_ADR_Index.md`'s "Pending Terminology Not Yet Covered by an ADR" table.
- Does not itself update `Naming_Registry.md`, `Architecture_Review_Register.md`, `08_Master_Data_Model.md`, or any other Blueprint document — those are separate, subsequent documentation-synchronization edits, consistent with the ADR-012/ADR-014 precedent of not performing migration edits inside the ADR itself.
- Does not authorize implementation, runtime validation, Publication, or any milestone completion.

## Alternatives Rejected

- Option 1 (native Item/Variants) — rejected per Options Considered, item 1.
- Option 2 (Extend Item, Custom Fields only) — rejected per Options Considered, item 2.
- Linking Product Template through Material — rejected per Options Considered, additional alternative discussion above.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #10.
2. In a subsequent documentation-synchronization task: `Naming_Registry.md` Section 27 item #10 is marked Resolved, citing this ADR; `00_ADR_Index.md` registers this ADR and removes item #10 from its Pending table; `Architecture_Review_Register.md` AR-006 moves to Resolved; `docs/blueprint/17_Inventory_Model.md` (an existing, previously empty Placeholder) is populated to record the ERPNext-ownership disposition; `08_Master_Data_Model.md`'s Open Question is corrected as a bounded, reference-only update; downstream architecture, mapping, and roadmap documents are synchronized to reflect the Resolved status.
3. This ADR does not perform those edits itself.

## Related Documents

- `docs/blueprint/08_Master_Data_Model.md` (Substrate/Material relationship, Open Questions)
- `docs/blueprint/17_Inventory_Model.md` (ERPNext-ownership disposition record)
- `docs/architecture/Canonical_Domain_Model.md` (Material Aggregate Root, Substrate Child Entity, Product Template Aggregate Root)
- `docs/architecture/ERPNext_Fit_Analysis.md` (Section 3, Inventory)
- `docs/database/ERPNext_DocType_Mapping.md` (Material, Substrate, Product Template entries)
- `docs/decisions/Architecture_Review_Register.md` (AR-006)
- `docs/standards/Naming_Registry.md` (Section 27, item #10)

## Related ADRs

- [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md) (ERPNext core never modified; all customization in `printos_core` — the governing constraint for Material's Custom PrintOS status)
- [ADR-012-Estimating-Terminology.md](ADR-012-Estimating-Terminology.md) (precedent for a Level 2 Naming Decision Matrix item resolved via its own numbered ADR)
- [ADR-014-Production-Terminology.md](ADR-014-Production-Terminology.md) (precedent for a single ADR resolving multiple related Naming Decision Matrix items)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-09-26|Initial|Initial Version — Project Owner acceptance of Option C (Material Custom, linked to Item; Substrate a specialization of Material; Product Template its own Custom PrintOS artifact), formally resolving Naming Registry Section 27 item #10 and Architecture Review Register AR-006.|

---

# Quality Checklist

- [x] Problem Statement clearly scoped
- [x] Decision Drivers stated
- [x] Options Considered documented, including rejected options
- [x] Decision is unambiguous, with explicit per-concept table
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Blueprint/Registry edits)
- [x] Related Documents and Related ADRs cross-referenced
- [x] Reviewed by Project Owner
