# 14 — Quotation Engine

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

Records the Architecture Review Register's disposition of AR-005 (Quotation Strategy): Quotation is delivered via ERPNext's native Quotation document, fed by a governed handoff from the Custom Cost Estimate artifact. This is the Blueprint-layer record of that architecture decision, required before implementation per `AGENTS.md`. It does not define DocType fields, schema, workflow configuration, or any implementation detail.

---

# Scope

Covers the boundary between Cost Estimate and native Quotation, and AR-010's Resolution (BOM necessity for Estimation's cost-breakdown modeling). Does not redefine either artifact's business meaning (`05_Domain_Model.md`; `06_Bounded_Contexts.md`) or their canonical naming (`ADR-013-Quotation-Terminology.md`). Does not define Cost Estimate fields, a Quotation Line target, a BOM schema, calculations, hooks, or migrations.

---

# Decision

Per Project Owner decision dated 2026-09-26 (AR-005, Option B, recorded in `../decisions/Architecture_Review_Register.md`, Version 0.7):

- **Cost Estimate** and the Estimation pricing/costing logic remain **Custom PrintOS artifacts** inside `printos_core` — unchanged; this was already the case prior to this decision (`../database/ERPNext_DocType_Mapping.md`).
- **ERPNext Quotation remains the customer-facing quotation document.**
- **Custom Cost Estimate and pricing logic feed ERPNext's native customer-facing Quotation through a governed handoff.**

The handoff's mechanics — field mappings, triggers, validation rules, Custom Fields, child-table structure, and Quotation Line's specific target treatment — are explicitly deferred to a separate, downstream DocType-design task and are not specified by this record.

---

# AR-010 Resolution (BOM Necessity)

AR-010 is **Resolved** by this record (Option A, Project Owner decision dated 2026-09-27, recorded in `../decisions/Architecture_Review_Register.md`, Version 0.12): **Product Template, Job Types, Finishing Types, Paper Sizes, Material, and Item are confirmed sufficient for current Estimation requirements.** **BOM is not adopted** in the current Cost Estimate scope, in any form — neither ERPNext's native BOM DocType nor a Custom BOM-like structure — and **ERPNext Manufacturing, Work Order, and native Manufacturing Job Card are not adopted**, consistent with `../architecture/ERPNext_Fit_Analysis.md` Section 3's existing Recommendation. **BOM consideration may be reopened only if a specific Product Template category cannot express its required cost breakdown through the existing Product Template/Job Types/Finishing Types/Paper Sizes/Material/Item structures.** This record does not define Cost Estimate fields, Quotation Line's exact Target DocType, a BOM schema, calculations, hooks, migrations, or implementation authorization — Quotation Line's ERPNext target remains deferred to a separate downstream DocType-design task, now unblocked at the conceptual-sufficiency level only.

---

# Consequences

- **Native Quotation remains the customer-facing document** — ERPNext's Quotation continues to be the document a Customer sees and approves. Any extension of that document required to receive the governed handoff from Cost Estimate — Custom Fields, child-table structure, or otherwise — remains **deferred to downstream DocType design** and is not specified here.
- No new DocType field, schema, workflow transition, migration, or runtime behavior is authorized or specified by this record.
- **BOM is not adopted** for Estimation's cost-breakdown modeling; existing Product Template/Job Types/Finishing Types/Paper Sizes/Material/Item master data is confirmed sufficient, with reopening reserved for the named trigger above.

---

# What This Document Does Not Do

- Does not authorize implementation, DocType creation, Custom Field creation, or Publication of any coding specification.
- Does not define Cost Estimate fields, a Quotation Line target, a BOM schema, calculations, hooks, migrations, or implementation authorization.
- Does not modify Quotation's or Cost Estimate's business definition or naming.
- Does not define the handoff's field mapping, trigger, validation logic, or Quotation Line's ERPNext target.

---

# Related Documents

- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) — AR-005, AR-010
- [05_Domain_Model.md](05_Domain_Model.md)
- [06_Bounded_Contexts.md](06_Bounded_Contexts.md)
- [../decisions/ADR-013-Quotation-Terminology.md](../decisions/ADR-013-Quotation-Terminology.md)
- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md)
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.2 | 2026-09-27 | AR-010 Option A Disposition Record | Records the Project Owner's AR-010 decision (Option A, recorded in `../decisions/Architecture_Review_Register.md`, Version 0.12): Product Template, Job Types, Finishing Types, Paper Sizes, Material, and Item are confirmed sufficient for current Estimation requirements; BOM (native ERPNext BOM or a Custom BOM-like structure) is not adopted in the current Cost Estimate scope; ERPNext Manufacturing, Work Order, and native Manufacturing Job Card are not adopted. BOM consideration may be reopened only if a specific Product Template category cannot express its required cost breakdown through the existing structures. Retitled and rewrote the former "Coupled Open Item — AR-010" section to "AR-010 Resolution (BOM Necessity)," updated the Scope section, and added one Consequences bullet and one "What This Document Does Not Do" bullet accordingly. Does not define Cost Estimate fields, a Quotation Line target, a BOM schema, calculations, hooks, migrations, or implementation authorization; Quotation Line's exact ERPNext target remains deferred to a separate downstream DocType-design task. No implementation authorized. |
| 0.1 | 2026-09-26 | AR-005 Option B Disposition Record | Initial population of this previously empty Placeholder (no new file created). Records the Project Owner's AR-005 decision (Option B): Custom Cost Estimate and pricing logic feed ERPNext's native customer-facing Quotation through a governed handoff; native Quotation remains the customer-facing document, with any required extension deferred to downstream DocType design. Handoff mechanics deferred to a separate downstream DocType-design task. AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework. No implementation authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
