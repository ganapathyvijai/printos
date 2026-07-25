# 03 — ERPNext Mapping

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

# Purpose

Map every Approved PrintOS business concept to its ERPNext realization strategy — Reuse (native ERPNext object, unmodified), Extend (native object plus Custom Fields/extensions), or Custom (a new PrintOS DocType) — so implementers know, before writing any code, whether a concept already exists in ERPNext and how it should be approached. This is the single most implementation-critical document in this set.

---

# Scope

Covers architecture-level mapping only: which ERPNext object corresponds to which PrintOS concept, and the customization strategy category. Does not contain DocType field definitions, server scripts, client scripts, or any code — those are downstream implementation artifacts governed by [04_Customization_Strategy.md](04_Customization_Strategy.md) and `docs/standards/DocType_Standards.md`.

---

# Background

ERPNext already implements substantial generic ERP capability (Company, Customer, Item, Sales Order, Purchase Order, Accounts, HR). PrintOS's job is to determine, concept by concept, whether to reuse that capability as-is, extend it, or build something new — per [ADR-002-PrintOS-Core](../decisions/ADR-002-PrintOS-Core.md)'s rule that ERPNext core is never modified, only extended through supported mechanisms (see [../technical/09_Extensibility_Model.md](../technical/09_Extensibility_Model.md)).

---

# Main Content

## Mapping Table

| Business Concept | Blueprint Reference | ERPNext Native Object | Reuse | Extend | Custom | Reason | Customization Strategy | Notes |
|---|---|---|---|---|---|---|---|---|
| Company | `08_Master_Data_Model.md` | Company | ✓ | | | Exact match; also the Approved tenant-scope anchor ([ADR-006](../decisions/ADR-006-MultiTenant-Strategy.md)) | None — native use | — |
| Branch | `08_Master_Data_Model.md` | Branch | ✓ | | | Exact match | None — native use | — |
| Department | `08_Master_Data_Model.md` | Department | ✓ | | | Exact match | None — native use | — |
| Employee | `08_Master_Data_Model.md` | Employee | ✓ | | | Exact match | None — native use | — |
| Customer | `05_Domain_Model.md` | Customer | | ✓ | | Native concept; print-industry attributes (e.g. preferred substrate) may need Custom Fields | Custom Fields via fixtures | Naming Registry: Customer vs. Client/Party is Pending ADR (Section 27, item 6) — mapping uses the Approved term |
| Supplier | `08_Master_Data_Model.md` | Supplier | ✓ | | | Exact match | None — native use | Naming Registry: Supplier vs. Vendor Pending ADR (item 13) — not relevant to Phase 1 ERP scope |
| Sales Order | `05_Domain_Model.md` | Sales Order | | ✓ | | Native concept; print-specific fields (Job Type, Finishing) needed | Custom Fields | — |
| Quotation | `06_Bounded_Contexts.md` | Quotation | | ✓ | | ERPNext has a native Quotation DocType; print-specific pricing detail requires extension | Custom Fields + Custom child table for cost breakdown | Per [ADR-013](../decisions/ADR-013-Quotation-Terminology.md), "Quotation" is the canonical business term — confirm native ERPNext Quotation DocType is the correct target before implementation, or whether a Custom DocType is warranted given Estimation's stated Core Domain status (`05_Domain_Model.md`) |
| Print Job / Job Card | `05_Domain_Model.md`, `09_PrintOS_Modules.md` | None (no ERPNext native equivalent) | | | ✓ | Print-industry-specific; no ERPNext concept covers Job Card execution/tracking | New Custom DocType `Job Card` | Per [ADR-014](../decisions/ADR-014-Production-Terminology.md), "Job Card" is canonical; "Production Order," "Job Ticket," "Work Order" are Rejected/Deprecated and must not appear as DocType names |
| Machine | `05_Domain_Model.md` | Asset (generic) | | ✓ | | ERPNext's generic Asset DocType could represent a Machine, but lacks print-specific capability modeling | Extend Asset via Custom Fields, or Custom DocType if Asset proves insufficient | Naming Registry: "Asset" vs. "Machine" relationship not yet fully defined (Section 6) — Architecture Review needed before choosing Extend vs. Custom |
| Machine Profile | `08_Master_Data_Model.md` | None | | | ✓ | No ERPNext equivalent for capability/constraint modeling | New Custom DocType `Machine Profile` | — |
| Material / Substrate | `05_Domain_Model.md`, `08_Master_Data_Model.md` | Item | | ✓ | | ERPNext's Item DocType is the closest native match | Extend Item via Custom Fields (Substrate attributes) or Item Variants | Naming Registry Section 27, item 10 (ERPNext "Item" vs. PrintOS "Material"/"Product Template") is Pending ADR — this mapping is provisional until resolved |
| Product Template | `08_Master_Data_Model.md` | Item / Item Template | | ✓ | | ERPNext Item Variants mechanism may map to Product Template | Extend via Item Variant Attributes | Same Pending ADR as above applies |
| Warehouse | `08_Master_Data_Model.md` | Warehouse | ✓ | | | Exact match | None — native use | — |
| Inventory / Stock | `05_Domain_Model.md` | Stock Ledger Entry / Bin | ✓ | | | ERPNext's native stock ledger covers this | Extend for Material/Substrate-specific reporting if needed | — |
| Purchase Order | `06_Bounded_Contexts.md` | Purchase Order | ✓ | | | Exact match | None — native use | Naming Registry item 3 (Purchasing vs. Procurement) does not affect this DocType mapping |
| Delivery / Dispatch | `06_Bounded_Contexts.md` | Delivery Note | | ✓ | | ERPNext Delivery Note maps to Dispatch's "Delivery Note (business concept)" | Custom Fields for dispatch-specific data | Naming Registry item 5 (Dispatch vs. Delivery) Pending ADR — mapping uses ERPNext's native term for the DocType, "Dispatch" for the Bounded Context/module name, per existing Blueprint usage |
| Invoice | `05_Domain_Model.md` | Sales Invoice | ✓ | | | Exact match | None — native use | — |
| Payment | `05_Domain_Model.md` | Payment Entry | ✓ | | | Exact match | None — native use | — |
| Tax Template / GST Configuration | `08_Master_Data_Model.md` | Sales Taxes and Charges Template | ✓ | | | ERPNext native tax capability handles GST | None — native use, configured | — |
| Price List | `08_Master_Data_Model.md` | Price List | ✓ | | | Exact match | None — native use | — |
| Payment Terms | `08_Master_Data_Model.md` | Payment Terms Template | ✓ | | | Exact match | None — native use | — |
| Role / Permission | `Naming_Registry.md` Section 20 | Role, Role Permission Manager | ✓ | ✓ | | Native RBAC reused; PrintOS-specific roles added on top | New Role records + Role Permission entries via fixtures; no core modification | See [../configuration/09_Role_Permission_Designer.md](../configuration/09_Role_Permission_Designer.md) |
| Workflow (business process state machine) | `Naming_Registry.md` Section 21 | Workflow (Frappe native) | | ✓ | | Frappe's native Workflow engine is reused; PrintOS-specific guard conditions layered on top | Extend via Workflow DocType configuration + Application-layer guard logic | Note the ERPNext-reserved-term overlap already flagged in `Naming_Registry.md` Section 21 (PrintOS "workflow" business term vs. Frappe's "Workflow" feature) |

## Explicit Classification Summary

| Classification | Concepts |
|---|---|
| Native ERPNext (Reuse, unmodified) | Company, Branch, Department, Employee, Supplier, Warehouse, Stock Ledger/Bin, Purchase Order, Sales Invoice, Payment Entry, Sales Taxes and Charges Template, Price List, Payment Terms Template |
| Custom DocTypes | Job Card, Machine Profile (and possibly Machine, pending Architecture Review) |
| Extensions (Custom Fields / Custom Field on native DocType) | Customer, Sales Order, Quotation, Item (Material/Substrate/Product Template), Delivery Note |
| Server Scripts | None mandated by this mapping; any use must follow [04_Customization_Strategy.md](04_Customization_Strategy.md) governance |
| Client Scripts | None mandated by this mapping; same governance applies |
| Custom Fields | See Extend rows above — exact field lists are a downstream DocType-design task, out of scope here |
| Workspaces | One per module, per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), following ERPNext's native Workspace mechanism |

---

# Architecture Notes

Two mapping rows above (Material/Substrate vs. Item, and Machine vs. Asset) are marked provisional and depend on Pending ADR resolution or Architecture Review, per [Naming_Registry.md](../standards/Naming_Registry.md) Section 27, items 10 and the un-numbered Machine/Asset question in Section 6. This document does not resolve those; it flags exactly where implementation must pause for a decision.

---

# Future Considerations

- Once ERPNext "Item" vs. "Material"/"Product Template" (Naming Registry item 10) is resolved by ADR, this mapping table must be updated to reflect the resolved strategy before Inventory/Estimation implementation begins in earnest.
- MachineIQ's future Machine Event/Telemetry concepts ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 36) will require their own ERPNext mapping once scoped — likely Custom, given ERPNext has no native telemetry concept.

---

# Open Questions

- Should Machine be modeled as an extended ERPNext Asset, or a wholly Custom DocType? This blocks Machine Scheduling implementation (see [02_Module_Implementation_Order.md](02_Module_Implementation_Order.md)) and should be an Architecture Review decision before Phase 3 begins.
- Should Quotation be extended from ERPNext's native Quotation DocType, or replaced with a Custom DocType given Estimation's Core Domain status? This is a real implementation-blocking decision, not a naming question.

---

# Related Documents

- [00_Implementation_Index.md](00_Implementation_Index.md)
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../database/02_DocType_Strategy.md](../database/02_DocType_Strategy.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [04_Customization_Strategy.md](04_Customization_Strategy.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-23 | Initial | Initial working draft mapping Approved business concepts to ERPNext native objects; two rows flagged as pending Architecture Review/ADR resolution rather than decided unilaterally. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
