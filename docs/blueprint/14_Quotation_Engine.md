# 14 — Quotation Engine

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-26

---

# Purpose

Records the Architecture Review Register's disposition of AR-005 (Quotation Strategy): Quotation is delivered via ERPNext's native Quotation document, fed by a governed handoff from the Custom Cost Estimate artifact. This is the Blueprint-layer record of that architecture decision, required before implementation per `AGENTS.md`. It does not define DocType fields, schema, workflow configuration, or any implementation detail.

---

# Scope

Covers only the boundary between Cost Estimate and native Quotation. Does not redefine either artifact's business meaning (`05_Domain_Model.md`; `06_Bounded_Contexts.md`) or their canonical naming (`ADR-013-Quotation-Terminology.md`). Does not resolve AR-010 (BOM necessity for Estimation's cost-breakdown modeling). AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework.

---

# Decision

Per Project Owner decision dated 2026-09-26 (AR-005, Option B, recorded in `../decisions/Architecture_Review_Register.md`, Version 0.7):

- **Cost Estimate** and the Estimation pricing/costing logic remain **Custom PrintOS artifacts** inside `printos_core` — unchanged; this was already the case prior to this decision (`../database/ERPNext_DocType_Mapping.md`).
- **ERPNext Quotation remains the customer-facing quotation document.**
- **Custom Cost Estimate and pricing logic feed ERPNext's native customer-facing Quotation through a governed handoff.**

The handoff's mechanics — field mappings, triggers, validation rules, Custom Fields, child-table structure, and Quotation Line's specific target treatment — are explicitly deferred to a separate, downstream DocType-design task and are not specified by this record.

---

# Coupled Open Item — AR-010 (BOM Necessity)

AR-010 remains **Open** and is not resolved by this record. AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework. This document does not treat AR-010 as resolving Quotation's own strategy, and does not treat it as a new blocker beyond its existing Register classification (Priority: Medium; Blocking Status: Medium; Blocks Development: Partial). AR-010 is not a condition on this AR-005 decision.

---

# Consequences

- **Native Quotation remains the customer-facing document** — ERPNext's Quotation continues to be the document a Customer sees and approves. Any extension of that document required to receive the governed handoff from Cost Estimate — Custom Fields, child-table structure, or otherwise — remains **deferred to downstream DocType design** and is not specified here.
- No new DocType field, schema, workflow transition, migration, or runtime behavior is authorized or specified by this record.

---

# What This Document Does Not Do

- Does not authorize implementation, DocType creation, Custom Field creation, or Publication of any coding specification.
- Does not resolve AR-010.
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
| 0.1 | 2026-09-26 | AR-005 Option B Disposition Record | Initial population of this previously empty Placeholder (no new file created). Records the Project Owner's AR-005 decision (Option B): Custom Cost Estimate and pricing logic feed ERPNext's native customer-facing Quotation through a governed handoff; native Quotation remains the customer-facing document, with any required extension deferred to downstream DocType design. Handoff mechanics deferred to a separate downstream DocType-design task. AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework. No implementation authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
