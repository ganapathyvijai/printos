# ERPNext Fit Analysis

Version:
0.2

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-24

---

# Purpose

Classify every PrintHub business capability against ERPNext's native functionality — Native, Extend, Customize, Plugin, or Future — so that implementation maximizes ERPNext reuse, minimizes customization inside `printos_core`, and preserves ERPNext core immutability and upgrade safety. This document is the primary implementation reference for Phase 1 and is intended for review at Implementation Decision Review (Architecture Freeze).

---

# Scope

Covers capability-level classification and ERPNext asset mapping (DocTypes, reports, workflows, permissions, integrations) for Core ERP modules, PrintHub Print Domain modules, Configuration Studio, and the Intelligence/Plugin layer (MachineIQ, AI Assistant, Marketplace). Does not contain DocType field design, server/client script content, or any implementation code. Does not redefine architecture, business rules, or naming — every classification below is checked against existing Blueprint, Business, Architecture, Configuration, Database, Standards, and ADR documentation; where no such source exists or a conflict is found, it is flagged, not resolved.

---

# Conflicts Flagged Before Analysis (Not Resolved Here)

Per this task's instruction to flag rather than resolve documentation conflicts, the following are surfaced before any classification work, because they materially affect how the rest of this document should be read:

| # | Conflict | Governing Reference | Disposition |
|---|---|---|---|
| 1 | This task specifies **ERPNext 15/Frappe 15**. Blueprint documentation ([../blueprint/07_Technology_Stack.md](../blueprint/07_Technology_Stack.md), currently Draft) specifies **ERPNext v16**, and [ADR-001-ERPNext-Framework](../decisions/ADR-001-ERPNext-Framework.md) accepts ERPNext v16 as the framework choice. | `07_Technology_Stack.md`, ADR-001 | Flagged. This document proceeds using ERPNext v16/Frappe capability as the basis for classification (per the aligned Draft Technology Stack Blueprint and the binding Accepted ADR-001 decision), while noting every capability claim should be re-verified against whichever version is actually targeted before implementation. This conflict must be resolved by the Project Owner (ADR amendment or new ADR), not by this document. |
| 2 | This task states a **settled** multi-tenant strategy: one ERPNext site, one database, one backend instance per tenant, shared app code/Docker image/CI-CD, no shared database. [ADR-006-MultiTenant-Strategy](../decisions/ADR-006-MultiTenant-Strategy.md) is "Accepted (strategy); Implementation Deferred" and explicitly reserves the concrete model decision for `docs/blueprint/25_MultiTenant_Architecture.md` (not yet written). "Tenant" itself remains a **Pending ADR** term against "Company" ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 11). | ADR-006, `Naming_Registry.md` §27 item 11 | Flagged. This document adopts the one-site-per-tenant model **as a working assumption for this analysis only**, consistent with it being one of the candidate models already recorded in [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md) (working draft). It is not treated as ratified architecture until `25_MultiTenant_Architecture.md` and the Tenant/Company ADR are formally accepted. |
| 3 | This task's **PRINT DOMAIN** module list includes several names not present in the Approved Blueprint Module Registry ([../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 11): "Print Specification," "Approval Management," "Production Workflow," "Machine Management," "Finishing," and "Quality Control." | `09_PrintOS_Modules.md`, `Naming_Registry.md` §11, §27 items 9 | Flagged per-item in Section 4 below. Each is analyzed under its closest Approved module or explicitly marked Proposed/Pending ADR — none is treated as an Approved module name in this document. |
| 4 | "Production Orchestration" (used in this task's Customize examples) is not a registered term in `Naming_Registry.md`. | `Naming_Registry.md` Section 38 (Mandatory Registration) | Flagged. Not used as a capability name below; the underlying capability is analyzed under "Production Planning" and "Job Cards" (Approved module names) instead. |

---

# 1. Executive Summary

PrintOS Phase 1 is best delivered by treating ERPNext as a mature generic ERP substrate and `printos_core` as a thin, well-bounded layer adding only what is genuinely print-industry-specific. Across the capabilities analyzed:

- **Native reuse is the default and dominant strategy** for Core ERP (Accounts, CRM, Selling, Purchasing, Inventory, HR) — these are Generic or Supporting Domains per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) and ERPNext already covers them maturely.
- **Extension (Custom Fields/Workflows/Reports on native DocTypes)** is the correct strategy wherever ERPNext has the right *shape* of object (Quotation, Sales Order, Item) but lacks print-specific attributes.
- **Customization inside `printos_core`** is justified only where ERPNext has no native equivalent at all — concretely: Job Card (production execution), Machine Profile (capability modeling), and the Configuration Studio itself. This matches the Core Domain classification in `05_Domain_Model.md` (Estimation & Quotation, Production Planning & Job Cards, Artwork Management, Machine Scheduling).
- **Plugin boundary** cleanly separates MachineIQ, Marketplace, AI Assistant, payment gateways, messaging (WhatsApp/SMS), and any external system integration from `printos_core` itself — all consumed through the Ports & Adapters pattern already established in [10_Integration_Architecture.md](10_Integration_Architecture.md).
- **Manufacturing** (ERPNext's native BOM/Work Order module) is a partial, not full, fit for print production — see Section 3 for why Job Card remains a Custom DocType rather than an extended ERPNext Work Order.
- Several requested capability names ("Print Specification," "Quality Control," "Finishing," "Machine Management," "Approval Management") are not yet Approved Blueprint/Naming Registry terms; this document analyzes their underlying business need without inventing or ratifying new terminology.

No ERPNext core modification is recommended anywhere in this analysis, consistent with [ADR-002-PrintOS-Core](../decisions/ADR-002-PrintOS-Core.md).

---

# 2. ERPNext 15/Frappe 15 Capability Overview

**Version note:** see Conflict #1 above. The capability overview below reflects ERPNext/Frappe's generally stable module set across v15/v16; where a specific DocType or feature is version-sensitive, this is called out.

| ERPNext Module | Native Capability | Maturity | Relevant PrintHub Domain |
|---|---|---|---|
| Accounts | General Ledger, Payment Entry, Sales/Purchase Invoice, Tax Templates, multi-currency | Mature, core ERPNext strength | Accounts, GST (per `06_Bounded_Contexts.md`) |
| CRM | Lead, Opportunity, Contact | Mature but generic | CRM |
| Selling | Quotation, Sales Order, Customer, Price List | Mature | Sales, Estimation (partial) |
| Buying | Purchase Order, Supplier, Request for Quotation | Mature | Purchasing/Procurement |
| Stock (Inventory) | Item, Warehouse, Stock Ledger Entry, Batch, Serial No | Mature | Inventory, Warehouse |
| Manufacturing | BOM, Work Order, Job Card, Workstation, Operation | Mature for discrete/repetitive manufacturing; **not print-industry-shaped** | Production (partial fit only — see Section 3/4) |
| HR | Employee, Department, Attendance, Payroll | Mature | HR |
| Assets | Asset, Asset Category, Maintenance | Moderate | Machine (candidate mapping, unresolved — see [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions) |
| Frappe Framework (cross-cutting) | Workflow engine, Role/Permission Manager, Custom Field/DocType, Print Format, Report Builder, Notification, Webhook | Mature | Consumed throughout Configuration Studio ([../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)) |

**Important nuance on Manufacturing:** ERPNext's Job Card (Manufacturing module) is a *make-to-stock/discrete-manufacturing* concept tied to a Work Order and BOM Operation. It is **not** the same concept as PrintHub's Approved "Job Card" business entity ([05_Domain_Model.md](../blueprint/05_Domain_Model.md), canonical per [ADR-014](../decisions/ADR-014-Production-Terminology.md)), which tracks a print/production job against a Sales Order directly, without assuming a BOM-driven manufacturing flow. This is a **terminology collision, not a reuse opportunity** — see Section 3 (Manufacturing) and Section 6 (Risks) for detail. PrintHub's Job Card must not be implemented as ERPNext's native Manufacturing "Job Card" DocType.

---

# 3. ERPNext Core Capability Analysis

### Accounts

#### ERPNext Capability
Full double-entry accounting, GL, Payment Entry, Sales/Purchase Invoicing, Tax Templates. Per [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md), Accounts is described as "Generic Domain, largely handled by ERPNext core capability."

#### Existing ERPNext DocTypes
GL Entry, Payment Entry, Sales Invoice, Purchase Invoice, Journal Entry, Sales Taxes and Charges Template, Payment Terms Template, Price List, Currency.

#### Reuse Opportunity
Full reuse. No print-industry-specific accounting need exists in Phase 1.

#### Gap Analysis
None identified for Phase 1. GST-specific configuration is handled via ERPNext's native tax capability, per `06_Bounded_Contexts.md`'s GST Context description.

#### Classification
**Native.**

#### Recommendation
ERPNext owns Accounts entirely. PrintHub owns nothing here beyond configuration (Tax Templates, Price Lists) via standard ERPNext setup. Do not build any parallel accounting capability.

---

### CRM

#### ERPNext Capability
Lead, Opportunity, Contact management.

#### Existing ERPNext DocTypes
Lead, Opportunity, Contact, Communication.

#### Reuse Opportunity
High. Per `06_Bounded_Contexts.md`, CRM's owned business objects are Lead, Enquiry, Customer Contact — these map closely to ERPNext's native Lead/Opportunity/Contact.

#### Gap Analysis
"Enquiry" as a distinct concept from ERPNext's "Opportunity" is not fully reconciled — Blueprint uses "Lead / Enquiry" together in `05_Domain_Model.md`'s Core Business Entities without a 1:1 ERPNext mapping decision. Minor Custom Field extension may be needed to capture print-specific enquiry detail (e.g., initial job specification notes).

#### Classification
**Extend.**

#### Recommendation
ERPNext owns Lead/Opportunity/Contact structure. PrintHub extends via Custom Fields for print-specific enquiry attributes only. Do not build a parallel Lead/Enquiry DocType.

---

### Selling (Sales, Estimation/Quotation)

#### ERPNext Capability
Quotation, Sales Order, Customer, Price List, Sales Taxes and Charges.

#### Existing ERPNext DocTypes
Quotation, Sales Order, Customer, Price List, Payment Terms Template.

#### Reuse Opportunity
Sales Order and Customer are strong native fits. Quotation is a partial fit — see Gap Analysis.

#### Gap Analysis
Per [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) (already flagged there as an open implementation-blocking question), Estimation is a **Core Domain** per `05_Domain_Model.md` ("Industry-specific pricing logic... is PrintOS's key differentiator"), requiring substrate/finishing/machine-time-based costing and multi-version quoting per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md). ERPNext's native Quotation DocType has no concept of substrate, finishing, machine-time, or a distinct pricing *engine* — it is a generic priced-line-item document. Whether to extend native Quotation heavily or introduce a Custom pricing/estimation engine feeding into Quotation is **not yet decided** (same open question as `03_ERPNext_Mapping.md`).

#### Classification
Sales Order, Customer: **Native/Extend** (Extend for print-specific fields, e.g. Job Type). Quotation/Estimation Engine: **Extend (document) + Customize (pricing engine)** — split classification, not resolved to a single category.

#### Recommendation
ERPNext owns the Sales Order/Customer/Quotation *document* structure and submission/approval mechanics. PrintHub owns the **pricing/estimation calculation logic** (substrate, finishing, machine-time costing) as an Application-layer capability inside `printos_core`, which populates the native Quotation rather than replacing it — unless Architecture Review determines the native Quotation DocType is structurally insufficient, in which case a Custom DocType would be required (this decision is open, see [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions). Do not build a parallel "Sales Order" or "Customer" DocType.

---

### Purchasing

#### ERPNext Capability
Purchase Order, Supplier, Request for Quotation, Purchase Receipt.

#### Existing ERPNext DocTypes
Purchase Order, Supplier, Purchase Receipt, Request for Quotation.

#### Reuse Opportunity
Full reuse. Per `06_Bounded_Contexts.md`, Procurement's owned objects (Purchase Order, Supplier Record usage) map directly to ERPNext native objects.

#### Gap Analysis
None substantive for Phase 1. Note: the Blueprint module is named **"Purchasing"** ([09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)) while the Bounded Context is named **"Procurement"** ([06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)) — this is an existing, unresolved Naming Registry conflict (Section 27, item 3, Pending ADR), not something this document introduces or resolves.

#### Classification
**Native.**

#### Recommendation
ERPNext owns Purchase Order/Supplier/Receipt entirely. Do not build a parallel procurement DocType. Flag the Purchasing/Procurement naming conflict for ADR resolution before it propagates further into implementation artifacts.

---

### Inventory (Stock)

#### ERPNext Capability
Item, Warehouse, Stock Ledger Entry, Batch, Serial No, Bin.

#### Existing ERPNext DocTypes
Item, Warehouse, Stock Entry, Stock Ledger Entry, Batch.

#### Reuse Opportunity
High for Warehouse (exact match, per [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md)) and stock ledger mechanics. Item is a partial fit for Material/Substrate — see Gap Analysis.

#### Gap Analysis
Naming Registry Section 27, item 10 (ERPNext "Item" vs. PrintOS "Material"/"Product Template") is **Pending ADR** and directly affects this classification. Substrate-specific attributes (GSM, media profile) have no native ERPNext equivalent and would require either Item Variant Attributes (Extend) or a distinct Substrate DocType (Customize) — not yet decided.

#### Classification
Warehouse, Stock Ledger: **Native.** Item/Material/Substrate: **Extend** (provisional — pending the Item vs. Material ADR).

#### Recommendation
ERPNext owns Warehouse and stock movement mechanics entirely. PrintHub extends Item via Custom Fields/Item Variants for Substrate/Media attributes, pending Architecture Review confirming Item Variants are structurally sufficient for print-industry substrate modeling. Do not build a parallel stock ledger or warehouse concept.

---

### Manufacturing

#### ERPNext Capability
BOM, Work Order, Job Card (Manufacturing), Workstation, Operation — designed for discrete/repetitive manufacturing with bill-of-materials-driven production.

#### Existing ERPNext DocTypes
BOM, Work Order, Job Card (Manufacturing module), Workstation, Operation.

#### Reuse Opportunity
**Low, as a direct 1:1 mapping.** Workstation is a plausible partial reuse candidate for Machine (see Section 4, Machine Management). BOM could be a partial reuse candidate for expressing a print job's material/finishing requirements, but this is unconfirmed.

#### Gap Analysis
This is the single most important gap in this entire analysis. ERPNext's native Manufacturing "Job Card" DocType is tied to a Work Order and BOM Operation sequence — a fundamentally different execution model than print production, which is driven by a confirmed Sales Order, approved Artwork, and Machine Scheduling rather than a BOM explosion. Per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md), PrintHub's "Job Card" is the canonical, Approved term for its own distinct concept — and "Production Order" (which would be the closer analogue to ERPNext's Work Order) was explicitly **Rejected** by that same ADR. Reusing ERPNext's Manufacturing Job Card would either force print production into an ill-fitting BOM/Work-Order model, or create a confusing terminology collision where "Job Card" means two different things in the same system.

#### Classification
**Customize** (for PrintHub's Job Card, Production Planning, Machine Scheduling). Workstation → Machine mapping: **Extend** (candidate, unconfirmed). BOM: **Future** (not confirmed as needed for Phase 1; revisit only if Estimation's material/finishing breakdown genuinely needs BOM-style structure).

#### Recommendation
ERPNext's Manufacturing module should **not** be adopted wholesale. PrintHub owns Job Card, Production Planning, and Machine Scheduling as Custom DocTypes/capability inside `printos_core`, per their Core Domain status in `05_Domain_Model.md`. Workstation may be evaluated as a partial base for Machine, but only after the Machine vs. Asset question ([../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions) also considers Workstation as a third candidate — this document adds that candidate rather than deciding among the three. **Do not** attempt to reuse ERPNext's native "Job Card" DocType or rename it to fit PrintHub's Job Card — this would either violate ADR-014 or create a same-name/different-meaning collision inside the same system, which is worse than two different names.

---

### HR

#### ERPNext Capability
Employee, Department, Attendance, Payroll.

#### Existing ERPNext DocTypes
Employee, Department, Attendance.

#### Reuse Opportunity
Full reuse. Per `06_Bounded_Contexts.md`, HR is a "Generic Domain; supports Production's operator assignment needs" — exactly ERPNext's native strength.

#### Gap Analysis
None for Phase 1. Operator-to-Machine/Job-Card assignment is a Production-side concern (consumes Employee data), not an HR gap.

#### Classification
**Native.**

#### Recommendation
ERPNext owns Employee/Department entirely. PrintHub's Production capability *references* Employee (for operator assignment) but does not extend or customize HR itself.

---

# 4. PrintHub Domain Capability Analysis

### Print Specification *(Requested term — not yet Approved; see Conflict #3)*

#### ERPNext Capability
None natively. No ERPNext DocType represents a print job's technical specification (size, stock, finish) as a first-class object.

#### Existing ERPNext DocTypes
Item Variant Attributes could partially represent some specification attributes if Product Template is mapped to Item (see Inventory section above), but this is unconfirmed.

#### Reuse Opportunity
Low — this capability most closely corresponds to `08_Master_Data_Model.md`'s Product Template, Job Types, Finishing Types, and Paper Sizes entities considered together, rather than any single ERPNext object.

#### Gap Analysis
"Print Specification" is not a term used in `05_Domain_Model.md`, `08_Master_Data_Model.md`, or `Naming_Registry.md`. Its underlying business need appears already covered by the combination of Product Template + Job Types + Finishing Types + Paper Sizes (all Approved master data entities). Introducing "Print Specification" as a new umbrella term or DocType requires Naming Registry registration (Section 38) before use.

#### Classification
**Customize** (the underlying combination of master data entities, already planned) — but the *name* "Print Specification" is **not adopted** by this document.

#### Recommendation
Do not create a "Print Specification" DocType under that name. Implement the underlying capability as the already-Approved Product Template / Job Types / Finishing Types / Paper Sizes master data entities per [08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md). If a single umbrella object proves genuinely necessary, propose the term to the Naming Registry first.

---

### Estimation

See Section 3 (Selling) — Estimation's pricing-engine gap is analyzed there since it is directly entangled with the native Quotation DocType decision. Summary: **Customize** (pricing/estimation calculation logic), feeding a **Native/Extend** Quotation document.

---

### Quotation

#### ERPNext Capability
Native Quotation DocType with line items, pricing, and submission workflow.

#### Existing ERPNext DocTypes
Quotation.

#### Reuse Opportunity
High for document structure and submission mechanics; low for print-specific pricing detail (see Estimation above).

#### Gap Analysis
Same as Selling/Estimation above — not restated.

#### Classification
**Extend.**

#### Recommendation
Reuse native Quotation as the document of record; feed it from PrintHub's Estimation calculation logic. Per [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md), "Quotation" is the sole canonical term — do not introduce "Quote," "Estimate," or "Proposal" as DocType or field names.

---

### Artwork Management

#### ERPNext Capability
None. ERPNext has generic File attachment capability but no concept of Artwork, Proof, or Approval Record as distinct business objects.

#### Existing ERPNext DocTypes
File (generic attachment mechanism, reusable as an underlying storage primitive only).

#### Reuse Opportunity
Low — only the generic file-attachment/versioning primitive is reusable; the business object model (Artwork, Proof, Approval Record) is entirely print-industry-specific.

#### Gap Analysis
None — this is squarely a Core Domain per `05_Domain_Model.md` ("Approval and proofing workflows are specific to print production").

#### Classification
**Customize.**

#### Recommendation
PrintHub owns Artwork, Proof, and Approval Record as Custom DocTypes inside `printos_core`, built on ERPNext's native File attachment mechanism for underlying storage. Approval workflow itself should be configured via the Approval Designer ([../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)) rather than hardcoded. Do not attempt to force this into a generic ERPNext document type.

---

### Approval Management *(Requested term — see Conflict #3)*

#### ERPNext Capability
Frappe's native Workflow engine supports approval-style state transitions generically.

#### Existing ERPNext DocTypes / Framework Features
Workflow, Workflow State, Workflow Action.

#### Reuse Opportunity
High, as a **platform mechanism** — not as a standalone PrintHub "module."

#### Gap Analysis
"Approval Management" is not a Blueprint business module in `09_PrintOS_Modules.md`; it corresponds to the Configuration Studio's **Approval Designer** ([../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)), which is a configuration surface, not a business capability module. This is a category mismatch in the requested module list, not a business gap.

#### Classification
**Extend** (Frappe's native Workflow engine, configured, not replaced) — analyzed further under Configuration Studio (Section 5).

#### Recommendation
Do not build a standalone "Approval Management" module. The capability already exists as the Approval Designer, which configures Frappe's native Workflow/approval mechanics per-document-type. See Section 5.

---

### Production Workflow *(Requested term — see Conflict #3)*

#### ERPNext Capability
See Manufacturing (Section 3) — partial, structurally mismatched fit.

#### Gap Analysis
"Production Workflow" is not itself a Blueprint module; it corresponds to the combination of the **Production Planning**, **Job Cards**, and **Machine Scheduling** modules (all Approved, per `09_PrintOS_Modules.md`) plus the "Production Workflow (End-to-End)" narrative in [../blueprint/10_Business_Workflows.md](../blueprint/10_Business_Workflows.md). It is not a distinct capability requiring separate classification.

#### Classification
**Customize** (via its constituent Approved modules — see Production Planning, Job Card, Machine Management below).

#### Recommendation
Do not create a standalone "Production Workflow" module or DocType. Implement via Production Planning + Job Cards + Machine Scheduling, each analyzed individually below, orchestrated by the workflow narrative already defined in `10_Business_Workflows.md`.

---

### Production Planning

#### ERPNext Capability
Partial, via Work Order scheduling (Manufacturing) — mismatched, per Section 3.

#### Existing ERPNext DocTypes
None directly reusable without forcing the BOM/Work Order model.

#### Reuse Opportunity
Low.

#### Gap Analysis
Core Domain per `05_Domain_Model.md`. Capacity planning, job prioritization, and schedule coordination (per `09_PrintOS_Modules.md`) have no ERPNext equivalent that doesn't assume BOM-driven manufacturing.

#### Classification
**Customize.**

#### Recommendation
PrintHub owns Production Planning as an Application-layer capability inside `printos_core`, orchestrating Job Cards and Machine Scheduling. Do not adopt ERPNext's Work Order-based planning.

---

### Job Card

#### ERPNext Capability
See Manufacturing (Section 3) — name collision, not a reuse opportunity.

#### Classification
**Customize.**

#### Recommendation
See Section 3, Manufacturing, for the full rationale. PrintHub's Job Card is a Custom DocType, entirely distinct from and never merged with ERPNext's Manufacturing "Job Card." This is the most upgrade-risk-sensitive decision in this document — see Section 6, Risks.

---

### Machine Management *(Requested term — Blueprint's Approved module name is "Machine Scheduling"; see Conflict #3)*

#### ERPNext Capability
ERPNext Assets module (Asset, Asset Category) provides generic equipment lifecycle tracking; Manufacturing's Workstation provides a production-capacity concept.

#### Existing ERPNext DocTypes
Asset, Asset Category, Workstation.

#### Reuse Opportunity
Moderate — two plausible partial candidates (Asset, Workstation), neither confirmed. This is the same open question already logged in [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions; this document adds Workstation as a third candidate not previously considered there.

#### Gap Analysis
Machine Profile (capability/constraint modeling per [08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)) has no ERPNext equivalent regardless of which base (Asset or Workstation) is chosen for Machine itself.

#### Classification
Machine: **Extend** (candidate, unresolved — Asset vs. Workstation). Machine Profile: **Customize.**

#### Recommendation
Do not decide Asset vs. Workstation in this document — escalate to Architecture Review as already flagged in `03_ERPNext_Mapping.md`, now with Workstation added as a candidate. Machine Profile is built as a Custom DocType regardless of which base is chosen for Machine. Also flag: the Blueprint's Approved module name is **"Machine Scheduling,"** not "Machine Management" — use the Approved name in all implementation artifacts.

---

### Finishing *(Requested term — not a Blueprint module; see Conflict #3)*

#### ERPNext Capability
None. Finishing (lamination, cutting, binding, mounting) is an Approved *business term* (`05_Domain_Model.md` Domain Terminology) but not a distinct Blueprint module — it is one of several attributes tracked via the "Finishing Types" master data entity, consumed by Estimation and Production.

#### Existing ERPNext DocTypes
None directly applicable; possibly BOM Operation if Manufacturing were adopted (not recommended, per Section 3).

#### Reuse Opportunity
None as a standalone module.

#### Gap Analysis
Requesting "Finishing" as a standalone module conflates an Approved *attribute/master-data* concept with a *module*. No Blueprint document currently models Finishing as process-tracking distinct from Job Card status.

#### Classification
**Customize** (as master data — Finishing Types) — not classified as a standalone module.

#### Recommendation
Do not create a "Finishing" module or DocType. Finishing Types already exist as Approved master data ([08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)) consumed by Estimation and Job Card. If Finishing genuinely needs its own process-tracking (e.g. a finishing sub-station queue), that is a new capability requiring Blueprint scoping and Naming Registry proposal first.

---

### Quality Control *(Requested term — Blueprint's approved concept is "Quality Check Record," embedded in Job Cards; standalone "Quality" module is Pending ADR)*

#### ERPNext Capability
None natively for print-industry quality checkpoints; ERPNext has generic Quality Inspection (Manufacturing/Stock module) but it is BOM/Item-centric.

#### Existing ERPNext DocTypes
Quality Inspection (partial, unconfirmed fit).

#### Reuse Opportunity
Low to moderate, unconfirmed — Quality Inspection assumes an Item-receipt or Work-Order-completion trigger, not a Job Card status transition.

#### Gap Analysis
Per `09_PrintOS_Modules.md`, Quality Check Record is currently embedded within the **Job Cards** module ("quality checkpoint recording" is a Job Cards feature, not a separate module). A standalone "Quality" module is recorded in `Naming_Registry.md` Section 27, item 9 as **Pending ADR** — not yet an Approved module.

#### Classification
**Customize** (as part of Job Cards) — standalone "Quality Control"/"Quality" module: **Future** (pending ADR).

#### Recommendation
Implement quality checkpoints as part of the Job Card capability now, per current Blueprint scope. Do not build a standalone "Quality Control" module until the Naming Registry Pending ADR (item 9) is resolved in favor of a distinct module.

---

### Dispatch

#### ERPNext Capability
Delivery Note.

#### Existing ERPNext DocTypes
Delivery Note.

#### Reuse Opportunity
High, per [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md), which already classified this as Extend.

#### Gap Analysis
Dispatch-specific fields (delivery method, dispatch documentation) require Custom Fields on Delivery Note. Note the existing Dispatch vs. Delivery naming conflict (`Naming_Registry.md` Section 27, item 5, Pending ADR) — not resolved here.

#### Classification
**Extend.**

#### Recommendation
Reuse native Delivery Note, extended with Custom Fields for print-specific dispatch data. Do not build a parallel Dispatch DocType.

---

# 5. Configuration Studio Fit Analysis

Configuration Studio (`docs/configuration/`) is, in aggregate, a **Customize** capability — ERPNext has no native equivalent to a designer-driven, tenant-scoped configuration layer sitting above its own native Workflow/Report/Permission engines. Its individual designers, however, are largely **Extend** in nature: each configures a native Frappe mechanism rather than replacing it.

| Designer | ERPNext Native Mechanism Configured | Classification |
|---|---|---|
| Module Designer / Manager | Frappe Module/Desk visibility | Extend |
| Workflow Designer | Frappe Workflow engine | Extend |
| Approval Designer | Frappe Workflow + Role-based approval | Extend |
| Form Designer | Customize Form, Client Script | Extend |
| Dashboard Designer | Frappe Dashboard/Number Card/Chart | Extend |
| Report Designer | Frappe Query/Script Report | Extend |
| Notification Designer | Frappe Notification, Email Alert | Extend |
| Feature Flags | None native — configuration-as-data concept is PrintHub-original | Customize |
| Tenant Customization | None native (ties to unresolved Tenant/Company question, Conflict #2) | Customize (provisional pending multi-tenant ADR) |

## Designer Ownership
Each designer owns its own configuration DocTypes exclusively — no two designers share a configuration DocType — per [../configuration/01_Configuration_Architecture.md](../configuration/01_Configuration_Architecture.md).

## Versioning Requirements
Configuration Templates ([../configuration/14_Template_Library.md](../configuration/14_Template_Library.md)) are explicitly versioned; applying a new template version to an existing tenant/installation is an explicit, reviewed action, never a silent overwrite.

## Rollback Requirements
Per [../configuration/15_Deployment_Model.md](../configuration/15_Deployment_Model.md), configuration is fixture-based and version-controlled; rollback means reapplying a previous fixture version, not manual undo. This is a Native/Extend reuse of Frappe's fixture export/import mechanism, not a new rollback engine.

## Import/Export
Native Frappe fixture export/import is reused for configuration promotion across environments — **Extend**, not Customize.

## Validation
Automation Rules ([../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md)) require a constrained, safely-evaluated condition-expression syntax — this evaluator itself is **Customize** (no native Frappe equivalent to a sandboxed condition DSL for configuration records).

## Dependency Management
Module Manager's dependency declarations (PrintOS Module Dependency) are **Customize** — ERPNext has no native app-level module dependency graph at this granularity.

## Audit Requirements
Approval decisions, workflow transitions, and automation runs must be logged and retained per [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md) and [../database/06_Data_Lifecycle.md](../database/06_Data_Lifecycle.md) — reuses Frappe's native Version/audit-trail DocType (**Extend/Native**) rather than a parallel audit mechanism.

---

# 6. Plugin Boundary Analysis

All capabilities below sit **outside** `printos_core`'s Domain/Application layers, consumed exclusively through the Ports & Adapters pattern in [10_Integration_Architecture.md](10_Integration_Architecture.md). None is native or extendable ERPNext capability; all are **Plugin** or **Future**.

| Capability | Classification | Governing ADR/Reference | Notes |
|---|---|---|---|
| MachineIQ | Plugin (Future scope) | [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md) | Consumed via `MachineIntegrationService` port ([Naming_Registry.md](../standards/Naming_Registry.md) Section 14); scope deferred |
| Marketplace | Plugin (Future scope, Phase 5) | [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md) | Consumed via a Marketplace integration port; scope deferred |
| AI Assistant | Plugin (Future) — **term not yet registered** | None — flagged in [../implementation/01_Phase_1_Roadmap.md](../implementation/01_Phase_1_Roadmap.md) as an unregistered Naming Registry term | Must be formally proposed to the Naming Registry and scoped in a Blueprint document before any classification beyond "Plugin, Future" can be made with confidence |
| Payment Gateways | Plugin | `Naming_Registry.md` Section 9 (Payment Gateway, Approved Integration term) | Adapter per gateway; credentials via Frappe encrypted fields, never embedded (per [../configuration/11_Integration_Designer.md](../configuration/11_Integration_Designer.md)) |
| WhatsApp / Messaging | Plugin | `Naming_Registry.md` Section 9 (WhatsApp Channel, Approved) | Consumed via Notification Designer channel abstraction ([../configuration/08_Notification_Designer.md](../configuration/08_Notification_Designer.md)) |
| SMS | Plugin | Not yet a registered Integration term; treated analogously to WhatsApp Channel pending registration | Same adapter pattern as WhatsApp |
| Storage (external file/object storage) | Plugin (Future, if beyond Frappe's native file storage) | None yet | Frappe's native File/attachment mechanism is Native/Extend for Phase 1; external object storage only becomes Plugin-relevant at scale not yet required |
| Shipping / Delivery Carriers | Plugin | Dispatch context (`06_Bounded_Contexts.md`) | Adapter per carrier; Dispatch (Section 4) remains the Extend-classified ERPNext-facing document, carrier integration is the Plugin-classified external call |
| IoT / Machine Telemetry | Plugin (Future) | `Naming_Registry.md` Section 36 (Telemetry, Sensor, PLC — Proposed, MachineIQ-scoped) | No Phase 1 requirement; explicitly Future |
| External Accounting/Payroll Exports | Plugin (Future, if required beyond native ERPNext Accounts/HR) | None yet | Not required while Accounts/HR remain Native (Section 3) |

**Plugin Boundary Rule (derived, not new):** A capability belongs in this section if and only if it (a) has no ERPNext native equivalent, and (b) is consumed via an external service call rather than owning `printos_core` Domain/Application logic directly — consistent with [10_Integration_Architecture.md](10_Integration_Architecture.md)'s Ports-and-Adapters pattern. This rule is descriptive of existing architecture documentation, not a new decision introduced here.

---

# 7. ERPNext vs PrintHub Ownership Boundary

| Capability | Owner | Strategy |
|---|---|---|
| Accounts (GL, Payment Entry, Invoicing, GST) | ERPNext | Native |
| CRM (Lead, Opportunity, Contact) | ERPNext | Extend |
| Customer | ERPNext | Extend |
| Supplier | ERPNext | Native |
| Sales Order | ERPNext | Extend |
| Quotation (document) | ERPNext | Extend |
| Estimation (pricing/costing engine) | printos_core | Customize |
| Purchase Order / Purchasing | ERPNext | Native |
| Warehouse / Stock Ledger | ERPNext | Native |
| Item / Material / Substrate | ERPNext | Extend (provisional — Pending ADR, `Naming_Registry.md` §27 item 10) |
| HR (Employee, Department) | ERPNext | Native |
| Manufacturing (BOM, Work Order, native Job Card) | ERPNext | **Not adopted** — see Section 3 |
| Artwork, Proof, Approval Record | printos_core | Customize |
| Job Card (PrintHub) | printos_core | Customize |
| Production Planning | printos_core | Customize |
| Machine Scheduling | printos_core | Extend (Machine base DocType, unresolved) + Customize (Machine Profile) |
| Dispatch (Delivery Note) | ERPNext | Extend |
| Configuration Studio (all designers) | printos_core | Extend (per-designer native mechanism) + Customize (aggregate platform) |
| MachineIQ | External service | Plugin (Future) |
| Marketplace | External service | Plugin (Future) |
| AI Assistant | External service (unregistered term) | Plugin (Future) — pending Naming Registry proposal |
| Payment Gateways, WhatsApp/SMS, Shipping Carriers | External services | Plugin |
| "Print Specification" (requested term) | N/A | **Not adopted** — see Section 4 |
| "Quality Control" (standalone module) | N/A | Future — Pending ADR (`Naming_Registry.md` §27 item 9) |
| "Finishing" (standalone module) | N/A | **Not adopted** — see Section 4 |
| "Approval Management" (standalone module) | N/A | **Not adopted** — see Configuration Studio, Approval Designer |

---

# 8. Final Recommendations

1. **Adopt Native reuse as the default posture** for Accounts, CRM, Purchasing/Procurement, Inventory's Warehouse/stock-ledger mechanics, and HR. No custom development is justified here.
2. **Reject wholesale adoption of ERPNext's Manufacturing module.** Job Card, Production Planning, and Machine Scheduling are correctly Custom capabilities inside `printos_core`; attempting to reuse ERPNext's BOM/Work-Order/Job-Card model would create both an architectural mismatch and a direct terminology collision with the Approved "Job Card" term (ADR-014).
3. **Escalate two implementation-blocking decisions to Architecture Review before Phase 2/3 implementation begins** (both already partially flagged in [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md), now sharpened by this analysis):
   - Machine: extend ERPNext Asset, extend Workstation, or build Custom.
   - Quotation/Estimation: extend native Quotation, or introduce a Custom pricing-engine-fed document.
4. **Do not adopt five requested module names as-is**: "Print Specification," "Approval Management," "Production Workflow," "Machine Management" (Approved name is "Machine Scheduling"), "Finishing," and "Quality Control" (standalone). Each maps to existing Approved concepts or Configuration Studio designers already documented — building new modules under these names would create duplicate/parallel structures.
5. **Treat MachineIQ, Marketplace, AI Assistant, and all external integrations uniformly as Plugin-boundary capabilities**, consumed via the Ports & Adapters pattern already established in [10_Integration_Architecture.md](10_Integration_Architecture.md) — no special-casing per capability.
6. **Resolve "AI Assistant" as a Naming Registry gap before further planning references it** — it appears in project instructions but is registered nowhere in Blueprint or Naming Registry.
7. **Do not finalize the multi-tenant (one-site-per-tenant) strategy as binding architecture from this document.** It is used here only as a working assumption; formal ratification requires `docs/blueprint/25_MultiTenant_Architecture.md` and resolution of the Tenant/Company Pending ADR.
8. **Route the ERPNext version conflict (v15/Frappe 15 vs. Published v16) to the Project Owner** before any environment is provisioned against a specific version.

---

# Architecture Notes

This document does not modify, supersede, or re-decide any existing ADR, Blueprint domain model, bounded context, or Naming Registry entry. Every "Customize" classification here is justified by an existing Core Domain designation in [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) or an existing gap already identified in [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md); no new business capability is introduced. Where this task's requested module list used non-Approved terminology, this document analyzed the underlying business need under its correct Approved name rather than silently adopting the new name.

---

# Future Considerations

- Once the Machine (Asset/Workstation/Custom) and Quotation (Extend/Customize) decisions are resolved by Architecture Review, this document's Section 3/4/7 entries should be updated to remove "unresolved"/"provisional" qualifiers.
- Once `docs/blueprint/25_MultiTenant_Architecture.md` and the Tenant/Company ADR are accepted, Section 5's Tenant Customization classification and Conflict #2 should be revisited.
- If "AI Assistant," "Quality Control," or "Finishing" are formally scoped and registered in the future, this document should be revised to reclassify them from "Not adopted"/"Future" to their resolved classification.

---

# Open Questions

- Should Machine be built on ERPNext Asset, Workstation, or as a wholly Custom DocType? (Escalated to Architecture Review.)
- Should Quotation/Estimation extend the native Quotation DocType or introduce a Custom pricing-engine-fed document? (Escalated to Architecture Review.)
- Is BOM genuinely needed for Estimation's material/finishing breakdown, or is Product Template + Job Types + Finishing Types sufficient without it?
- Which ERPNext/Frappe version (15 or 16) is actually the implementation target, and who resolves the conflict between this task's instructions and the Draft Technology Stack Blueprint/ADR-001?

---

# Related Documents

- [00_Architecture_Index.md](00_Architecture_Index.md)
- [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md)
- [10_Integration_Architecture.md](10_Integration_Architecture.md)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md)
- [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [../decisions/00_ADR_Index.md](../decisions/00_ADR_Index.md)
- [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-24 | Initial | Initial ERPNext Fit Analysis. Classified Core ERP, Print Domain, Configuration Studio, and Plugin-boundary capabilities as Native/Extend/Customize/Plugin/Future. Flagged four documentation conflicts (ERPNext version, multi-tenant strategy ratification status, non-Approved module names, unregistered "Production Orchestration"/"AI Assistant" terms) without resolving them. |
| 0.2 | 2026-07-27 | Source-Authority Correction | Corrected Conflict #1's source-authority description and the matching Open Questions reference, both of which inaccurately characterized `07_Technology_Stack.md` as "Published Blueprint documentation" when its live Status is Draft. Replaced with wording accurately describing the aligned Draft Technology Stack Blueprint and the binding, Accepted ADR-001-ERPNext-Framework decision as this document's basis for proceeding with v16. v16 remains a Working Assumption/conditional analysis basis, not a resolved decision; AR-001 remains unresolved. No capability classification, hooks/events/DocType finding, or compatibility conclusion was revalidated or changed by this correction. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated (none required for this document's tabular format)
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] All naming/version/strategy conflicts flagged, not resolved
- [ ] Reviewed by Project Owner
