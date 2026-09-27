# 17 — Inventory Model

Version:
0.2

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-27

---

# Purpose

Record the ERPNext-ownership boundary for PrintOS's Material, Substrate, and Product Template concepts, resolving [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006 (Item vs. Material/Product Template Mapping) via Project Owner selection of **Option C**, formalized in [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md). This document does not perform DocType-level design; it records the boundary decision only.

---

# Scope

Covers the ownership boundary between native ERPNext Item and PrintOS's Custom Material, Substrate, and Product Template concepts. Does not cover DocType field lists, schema, hooks, Custom Fields, or migration mechanics — those remain a separate downstream DocType-design task. [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-010 (BOM necessity for Estimation) is Resolved (Option A, 2026-09-27), recorded separately in [14_Quotation_Engine.md](14_Quotation_Engine.md); Material, Substrate, and Product Template ownership as decided by AR-006/ADR-016 are unaffected by, and unrelated to, AR-010's disposition.

---

# Decision

- **Material** is a Custom PrintOS master record, linked to native ERPNext Item. Material is not an Item Variant and does not extend Item via Custom Fields; it is a distinct `printos_core` DocType referencing Item.
- **Substrate is a specialization of Material**, holding print-specific properties such as GSM and media profile. Substrate's exact DocType structure — a child table of Material's Custom DocType, or a standalone DocType linked to Material — is **deferred to a separate downstream DocType-design task** and is not decided by this document.
- **Product Template is its own Custom PrintOS artifact** in the Estimation context, structurally independent of Material and Item. It is not linked to Material by this disposition; no Blueprint relationship-map support exists for such a coupling, and different Product Templates commonly share the same Material/Substrate.
- **Native ERPNext Item remains unchanged** as the native inventory, purchasing, unit-of-measure, and accounting integration record. Item's own native role is not altered, extended, or diminished by this disposition.

---

# AR-010 Cross-Reference (BOM Necessity)

AR-010 (BOM necessity for Estimation) is **Resolved** — Option A, 2026-09-27, recorded in [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md), Version 0.12, and formalized by updating [14_Quotation_Engine.md](14_Quotation_Engine.md), Version 0.1 → 0.2: Product Template, Job Types, Finishing Types, Paper Sizes, Material, and Item are confirmed sufficient for current Estimation requirements; BOM is not adopted in the current Cost Estimate scope. This disposition does not alter, condition, or reopen this document's own AR-006 boundary decision — Material's ownership, Substrate's specialization, and Product Template's independence, as decided by AR-006/ADR-016, remain exactly as previously recorded.

---

# What This Document Does Not Do

- Does not define any DocType schema, field, hook, validation, or migration.
- Does not decide Substrate's child-table-vs-standalone-DocType structure.
- Does not define a BOM schema, calculations, hooks, or migrations (AR-010's own resolution is recorded in `14_Quotation_Engine.md`, not by this document's normative content).
- Does not authorize implementation, runtime validation, or Publication.
- Does not modify any ERPNext core DocType.

---

# Related Documents

- [Architecture Review Register](../decisions/Architecture_Review_Register.md) — AR-006 disposition
- [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md)
- [08_Master_Data_Model.md](08_Master_Data_Model.md) — Substrate-specialization Open Question, resolved
- [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) — Material Aggregate Root, Substrate Child Entity, Product Template Aggregate Root
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md) — Material, Substrate, Product Template implementation ownership

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.2 | 2026-09-27 | AR-010 Cross-Reference Correction | Corrected the Scope section and the "Coupled Open Item — AR-010" section (retitled "AR-010 Cross-Reference (BOM Necessity)"), which still stated AR-010 remains Open, following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-010's Resolved disposition (Option A, 2026-09-27), formalized by updating [14_Quotation_Engine.md](14_Quotation_Engine.md), Version 0.1 → 0.2. This is a bounded, reference-only correction: AR-006's own decision boundary — Material is a Custom PrintOS master record linked to native Item; Substrate is a specialization of Material, its exact DocType structure deferred to downstream design; Product Template is its own Custom PrintOS artifact, independent of Material and Item — is preserved entirely unchanged. This document does not itself resolve, perform, or claim authority over AR-010's own resolution, which is recorded in `14_Quotation_Engine.md`. No implementation authorized. |
| 0.1 | 2026-09-26 | AR-006 Resolved — Blueprint 17 Population | Populated this existing, previously empty Placeholder to record AR-006's Resolved disposition (Option C, 2026-09-26; ADR-016). No implementation authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
