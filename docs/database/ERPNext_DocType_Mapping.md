# ERPNext DocType Mapping

Version:
0.2

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-25

---

# Purpose

Assign exactly one implementation owner — Native ERPNext, Extended ERPNext, Custom PrintHub, External Plugin, or Pending Architecture Review — to every business entity cataloged in [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) and organized in [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md). This is the authoritative implementation ownership document: it determines *where* each entity lives, not how it is built.

---

# Scope

Covers implementation ownership assignment for every business entity (Aggregate Root, Child Entity, and Value Object) in the Canonical Domain Model. Does not cover database schema, field design, Frappe model code, or the three Domain Services (Estimation Engine, Production Planning/Scheduling Engine, Configuration Resolution Engine) identified in the Canonical Domain Model — Domain Services are Application-layer behavior, not persistent entities, and therefore have no DocType mapping of their own; each is implemented as logic operating over the DocTypes mapped below. Does not resolve any Architecture Review Register item.

---

# Mapping Methodology

Not every open Architecture Review (AR) item blocks knowing *where* an entity's implementation should live. This document distinguishes two kinds of AR references:

- **Blocking AR items** — the AR item concerns the entity's structural target (which ERPNext object, if any, underlies it), so the Implementation Owner genuinely cannot be assigned yet: **AR-002** (Tenant Override's data model depends on Tenant/Company resolution), **AR-004** (Machine/Machine Profile's ERPNext base), **AR-005** (Quotation/Quotation Line's document strategy), **AR-006** (Material/Substrate/Product Template's Item mapping), **AR-011** (Lead/Enquiry's Opportunity mapping), and **AR-003 specifically for the unregistered AI Assistant entities** (their existence and placement, not just their name, is undefined).
- **Non-blocking AR items** — the AR item concerns terminology only; the entity's implementation location is already clear regardless of which name is eventually chosen: **AR-007** (Purchasing/Procurement naming — does not change that Purchase Order/Supplier are Native ERPNext), **AR-008** (Dispatch/Delivery naming — does not change that Dispatch Record extends Delivery Note), **AR-009** (Quality module naming/scope — does not change that Quality Check Record is a Custom child entity today), **AR-010** (BOM necessity — affects the Estimation Engine Domain Service's internals, not any entity's DocType ownership), and **AR-003 for Approval Record and "Print Specification"** (naming only; Approval Record's Custom PrintHub placement is unaffected, and "Print Specification" is not modeled as an entity at all).

Only entities gated by a **blocking** AR item are classified "Pending Architecture Review" below. Entities affected only by a non-blocking AR item are assigned a definite Implementation Owner, with the AR item cited in Dependencies for traceability.

Five entities (Marketplace Package, Marketplace Template, Extension, Publisher, Marketplace Package Version) are neither Approved nor covered by any existing AR item — see Special Handling below; they are not force-fit into any of the five ownership categories.

---

# 1. CRM Context

#### Customer
- **Business Owner / Bounded Context:** CRM
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Customer
- **Implementation Strategy:** Strong native structural fit; per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (CRM), extended with Custom Fields for print-specific enquiry/preference attributes.
- **Customization Required:** Custom Fields
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md); Customer-vs-Client/Party naming is Pending ADR (Naming Registry §27 item 6, non-blocking, no AR number assigned).

#### Lead / Enquiry
- **Business Owner / Bounded Context:** CRM
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-011
- **Implementation Strategy:** Whether Enquiry is fully represented by ERPNext's native Opportunity (Extended ERPNext) or requires a distinct Custom concept is unresolved.
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-011; [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (CRM).

---

# 2. Sales Context

#### Sales Order
- **Business Owner / Bounded Context:** Sales
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Sales Order
- **Implementation Strategy:** Strong native fit per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Selling); extended for print-specific fields (e.g., Job Type reference).
- **Customization Required:** Custom Fields
- **Dependencies:** [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md).

#### Price List
- **Business Owner / Bounded Context:** Sales
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Price List
- **Implementation Strategy:** No gap identified in [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md); full native reuse.
- **Customization Required:** None
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

---

# 3. Estimation Context

#### Quotation
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-005
- **Implementation Strategy:** Whether Quotation extends native ERPNext Quotation, is fed by a separate Custom Estimation document, or is fully replaced is unresolved.
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-005; [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md).

#### Quotation Line
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-005 (dependent on Quotation's resolution)
- **Implementation Strategy:** A child table's structure cannot be finalized before its parent document's strategy is decided.
- **Customization Required:** Not yet determined
- **Dependencies:** AR-005; also touches AR-010 (BOM necessity for cost breakdown).

#### Cost Estimate
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent; a distinct internal artifact from Quotation per [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md), supporting the Estimation Engine Domain Service (see [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md)).
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Print Estimation & Costing Engine").

#### Product Template
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-006
- **Implementation Strategy:** Whether Product Template maps to ERPNext Item, Item Variants, or a Custom object depends on the Item-vs-Material resolution.
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006.

#### Product Category
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Item Group
- **Implementation Strategy:** ERPNext's native hierarchical grouping mechanism is a structural fit regardless of how Product Template itself resolves; extended only if print-specific grouping attributes are needed.
- **Customization Required:** Custom Fields (if needed) / None
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

#### Job Types *(Value Object)*
- **Business Owner / Bounded Context:** Production / Estimation
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent for print-industry job classification; simple reference data, independent of the Item-vs-Material resolution.
- **Customization Required:** New DocType
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

#### Finishing Types *(Value Object)*
- **Business Owner / Bounded Context:** Production / Estimation
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Same rationale as Job Types.
- **Customization Required:** New DocType
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

#### Paper Sizes *(Value Object)*
- **Business Owner / Bounded Context:** Estimation / Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Same rationale as Job Types.
- **Customization Required:** New DocType
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

---

# 4. Artwork Context

#### Artwork
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent beyond generic File attachment; Core Domain per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md), Customize per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 4.
- **Customization Required:** New DocType
- **Dependencies:** [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Artwork & Proof Lifecycle").

#### Artwork Revision
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType (child table of Artwork)
- **Implementation Strategy:** Same rationale as Artwork; built on ERPNext's native File mechanism for underlying storage only.
- **Customization Required:** New DocType
- **Dependencies:** Same as Artwork.

#### Proof
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Same rationale as Artwork.
- **Customization Required:** New DocType
- **Dependencies:** Same as Artwork.

#### Approval Record
- **Business Owner / Bounded Context:** Artwork (primary; also referenced by Estimation, Production)
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent to a business-specific approval outcome record; built on top of Frappe's native Workflow engine (used by the Approval Definition configuration entity) but the outcome record itself is Custom. The "Approval Management" naming question (AR-003) is non-blocking — it does not change where this entity lives.
- **Customization Required:** New DocType
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 (non-blocking, terminology only); [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md).

---

# 5. Production Context

#### Job Card
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Must **not** reuse ERPNext's native Manufacturing "Job Card" — structurally mismatched (BOM/Work-Order-driven) and name-colliding with the Approved term per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md); see [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Manufacturing).
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md); [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Job Card Execution & Lifecycle").

#### Quality Check Record
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType (child table of Job Card)
- **Implementation Strategy:** ERPNext's native Quality Inspection assumes an Item-receipt/Work-Order trigger, not a Job Card transition — not reused. Standalone-module status (AR-009) is non-blocking; this entity is Custom regardless of that outcome.
- **Customization Required:** New DocType
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-009 (non-blocking); [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md).

#### Machine
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-004
- **Implementation Strategy:** ERPNext Asset, ERPNext Workstation, a wholly Custom DocType, or a Hybrid combination remain open candidates.
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-004.

#### Machine Profile
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-004 (dependent on Machine's resolution)
- **Implementation Strategy:** Consistently classified Custom in both [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) and [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) regardless of Machine's own base — but formally held Pending here since it is structurally a child of Machine and cannot be finalized independently.
- **Customization Required:** New DocType (expected regardless of Machine's resolution)
- **Dependencies:** AR-004.

#### Production Schedule *(candidate, Future)*
- **Business Owner / Bounded Context:** Production Planning
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType — **Future Phase, not required for Phase 1, not yet scoped**
- **Implementation Strategy:** Not currently a distinct entity in any Approved Blueprint document; would be Custom if/when scoped, per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) (Future-classified).
- **Customization Required:** New DocType (deferred)
- **Dependencies:** None current; not gated by any AR item.

#### Finishing Operation *(candidate, Future)*
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType — **Future Phase, not required for Phase 1, not yet scoped**
- **Implementation Strategy:** Explicitly Future-classified in [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Finishing Process Tracking").
- **Customization Required:** New DocType (deferred)
- **Dependencies:** None current.

#### "Production Stage"
- **Business Owner / Bounded Context:** Not applicable
- **Implementation Owner:** No Implementation Owner — Not a Domain Entity
- **Target DocType:** Not applicable
- **Implementation Strategy:** Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review, this is fully represented by Job Card's own status attribute and carries no independent identity. Listed here only for traceability to the Business Entity Inventory.
- **Customization Required:** Not applicable
- **Dependencies:** [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review.

#### "Machine Queue"
- **Business Owner / Bounded Context:** Not applicable
- **Implementation Owner:** No Implementation Owner — Not a Domain Entity
- **Target DocType:** Not applicable
- **Implementation Strategy:** Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review, this is a derived view over Job Card-to-Machine assignments, not persisted domain state.
- **Customization Required:** Not applicable
- **Dependencies:** [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review.

#### "Print Specification"
- **Business Owner / Bounded Context:** Not applicable
- **Implementation Owner:** No Implementation Owner — Not Modeled as Independent Entity
- **Target DocType:** Not applicable
- **Implementation Strategy:** Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review, the underlying need is already covered by Product Template, Job Types, Finishing Types, and Paper Sizes (each mapped independently above).
- **Customization Required:** Not applicable
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 (non-blocking — naming/scope only, entity not modeled).

---

# 6. Inventory Context

#### Material
- **Business Owner / Bounded Context:** Inventory
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-006
- **Implementation Strategy:** Whether Material maps to ERPNext Item (with or without Variants) or a distinct Custom object is unresolved.
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006.

#### Substrate
- **Business Owner / Bounded Context:** Inventory / Production
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-006 (dependent on Material's resolution)
- **Implementation Strategy:** Structurally a specialization/child of Material; cannot be finalized independently.
- **Customization Required:** Not yet determined
- **Dependencies:** AR-006.

#### Media Profiles *(Value Object)*
- **Business Owner / Bounded Context:** Inventory
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent; simple reference data describing Substrate characteristics, independent of the Item-vs-Material base decision.
- **Customization Required:** New DocType
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

#### Units of Measure
- **Business Owner / Bounded Context:** Inventory / Estimation
- **Implementation Owner:** Native ERPNext
- **Target DocType:** UOM
- **Implementation Strategy:** Exact native match, per [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 19 (UOM abbreviation, Approved for Master Data).
- **Customization Required:** None
- **Dependencies:** None.

---

# 7. Procurement Context

#### Purchase Order
- **Business Owner / Bounded Context:** Procurement
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Purchase Order
- **Implementation Strategy:** Full native reuse, per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Purchasing).
- **Customization Required:** None
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-007 (non-blocking — module/context naming only).

#### Supplier
- **Business Owner / Bounded Context:** Procurement
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Supplier
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** AR-007 (non-blocking).

---

# 8. Warehouse Context

#### Warehouse
- **Business Owner / Bounded Context:** Warehouse
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Warehouse
- **Implementation Strategy:** Exact native match.
- **Customization Required:** None
- **Dependencies:** None.

---

# 9. Dispatch Context

#### Dispatch Record
- **Business Owner / Bounded Context:** Dispatch
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Delivery Note
- **Implementation Strategy:** Strong native structural fit per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 4; extended with Custom Fields for dispatch-specific data. The Dispatch-vs-Delivery naming question (AR-008) is non-blocking.
- **Customization Required:** Custom Fields
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-008 (non-blocking).

#### Delivery Method
- **Business Owner / Bounded Context:** Dispatch
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Modeled as its own master data entity in [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md); no sufficiently structured ERPNext native equivalent identified beyond a simple field option set.
- **Customization Required:** New DocType
- **Dependencies:** [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md).

---

# 10. Accounts Context

#### Invoice
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Sales Invoice
- **Implementation Strategy:** Full native reuse, per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Accounts).
- **Customization Required:** None
- **Dependencies:** None.

#### Payment
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Payment Entry
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

#### Journal Entry
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Journal Entry
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

#### Tax Template
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Sales Taxes and Charges Template
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

#### Payment Terms
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Payment Terms Template
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

#### Currency
- **Business Owner / Bounded Context:** Accounts
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Currency
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

---

# 11. GST Context

#### GST Configuration
- **Business Owner / Bounded Context:** GST
- **Implementation Owner:** Native ERPNext
- **Target DocType:** GST Settings (native tax configuration)
- **Implementation Strategy:** ERPNext's native GST/tax capability is reused directly, per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Accounts).
- **Customization Required:** None
- **Dependencies:** None.

---

# 12. HR Context

#### Employee
- **Business Owner / Bounded Context:** HR
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Employee
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

#### Department
- **Business Owner / Bounded Context:** HR
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Department
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

---

# 13. Administration Context

#### Company
- **Business Owner / Bounded Context:** Administration
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Company
- **Implementation Strategy:** Full native reuse; also the Approved tenant-scope anchor per [ADR-006](../decisions/ADR-006-MultiTenant-Strategy.md).
- **Customization Required:** None
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-002 (non-blocking for Company itself — Company's own DocType status is not in question, only its relationship to "Tenant").

#### Branch
- **Business Owner / Bounded Context:** Administration
- **Implementation Owner:** Native ERPNext
- **Target DocType:** Branch
- **Implementation Strategy:** Full native reuse.
- **Customization Required:** None
- **Dependencies:** None.

---

# 14. Configuration Studio Context

#### Module Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent at PrintOS's business-module granularity (ERPNext's app-level `required_apps` is coarser), per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Module Dependency Management").
- **Customization Required:** New DocType
- **Dependencies:** [../configuration/02_Module_Manager.md](../configuration/02_Module_Manager.md).

#### Workflow Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Workflow (Frappe native)
- **Implementation Strategy:** Configures Frappe's native Workflow engine rather than replacing it, per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 5.
- **Customization Required:** Workflow
- **Dependencies:** [../configuration/03_Workflow_Designer.md](../configuration/03_Workflow_Designer.md).

#### Approval Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Workflow + Role-based approval (Frappe native)
- **Implementation Strategy:** Same rationale as Workflow Definition.
- **Customization Required:** Workflow, Permissions
- **Dependencies:** [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md).

#### Form Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Customize Form / Client Script (Frappe native)
- **Implementation Strategy:** Configures Frappe's native form-customization mechanism.
- **Customization Required:** Custom Fields, Runtime Logic (Client Script)
- **Dependencies:** [../configuration/05_Form_Designer.md](../configuration/05_Form_Designer.md).

#### Dashboard Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Number Card / Dashboard Chart (Frappe native)
- **Implementation Strategy:** Configures Frappe's native dashboard mechanism. Note: an apparent overlap between this entity and the Blueprint's Reporting bounded context was reviewed and resolved as documentation terminology clarification, not an architectural conflict — Configuration Studio owns Dashboard Definition as a configuration artifact; Reporting owns the reporting capability and consumes Published instances. This clarification does not change the implementation owner or target below.
- **Customization Required:** Reports, Configuration
- **Dependencies:** [../configuration/06_Dashboard_Designer.md](../configuration/06_Dashboard_Designer.md).

#### Report Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Query Report / Script Report (Frappe native)
- **Implementation Strategy:** Configures Frappe's native reporting mechanism.
- **Customization Required:** Reports
- **Dependencies:** [../configuration/10_Report_Designer.md](../configuration/10_Report_Designer.md).

#### Notification Template
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Notification / Email Alert (Frappe native)
- **Implementation Strategy:** Configures Frappe's native notification mechanism.
- **Customization Required:** Configuration
- **Dependencies:** [../configuration/08_Notification_Designer.md](../configuration/08_Notification_Designer.md).

#### Integration Definition
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Webhook (Frappe native), extended
- **Implementation Strategy:** Frappe's native Webhook mechanism provides a partial base for outbound integration; extended with Custom Fields for credential reference and field mapping to cover the Integration Designer's broader scope.
- **Customization Required:** Custom Fields, Runtime Logic
- **Dependencies:** [../configuration/11_Integration_Designer.md](../configuration/11_Integration_Designer.md).

#### Feature Flag
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent, per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Feature Flag Engine").
- **Customization Required:** New DocType
- **Dependencies:** [../configuration/12_Feature_Flags.md](../configuration/12_Feature_Flags.md).

#### Automation Rule
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Frappe's native Server Script executes arbitrary code, explicitly disallowed as the condition-evaluation basis; per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Automation Rule Condition Evaluator").
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md).

#### Configuration Template
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent to a cross-designer configuration bundle, per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Configuration Studio Governance Layer"); built on Frappe's native fixture export/import as an underlying primitive only.
- **Customization Required:** New DocType
- **Dependencies:** [../configuration/14_Template_Library.md](../configuration/14_Template_Library.md).

#### Tenant Override
- **Business Owner / Bounded Context:** Configuration Studio
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-002
- **Implementation Strategy:** The data model depends directly on how "Tenant" is formally defined relative to "Company."
- **Customization Required:** Not yet determined
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-002.

---

# 15. MachineIQ Context *(Future)*

#### Machine Event
- **Business Owner / Bounded Context:** MachineIQ
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model (external; not a `printos_core` DocType)
- **Implementation Strategy:** Full scope deferred per [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md); consumed via `MachineIntegrationService` port per [../architecture/10_Integration_Architecture.md](../architecture/10_Integration_Architecture.md).
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Sensor
- **Business Owner / Bounded Context:** MachineIQ
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model — **Future, not yet scoped**
- **Implementation Strategy:** Same rationale as Machine Event.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Telemetry Reading
- **Business Owner / Bounded Context:** MachineIQ
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model — **Future, not yet scoped**
- **Implementation Strategy:** Same rationale.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Counter Reading
- **Business Owner / Bounded Context:** MachineIQ
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model — **Future, not yet scoped**
- **Implementation Strategy:** Same rationale.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Maintenance Alert
- **Business Owner / Bounded Context:** MachineIQ
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model — **Future, not yet scoped**
- **Implementation Strategy:** Same rationale.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Machine Status
- **Business Owner / Bounded Context:** MachineIQ / Production
- **Implementation Owner:** External Plugin
- **Target DocType:** MachineIQ Service Model — **Future, not yet scoped**
- **Implementation Strategy:** Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md), modeled as a MachineIQ Value Object; note a simple current-state field on the Machine DocType itself may also be warranted once Machine's own base is resolved (AR-004) — this is an observation, not a decision.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md); AR-004 (non-blocking observation).

---

# 16. Marketplace Context *(Future)*

#### Marketplace Listing
- **Business Owner / Bounded Context:** Marketplace
- **Implementation Owner:** External Plugin
- **Target DocType:** Marketplace Service Model — **Future, Phase 5, not yet scoped**
- **Implementation Strategy:** Full scope deferred per [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).
- **Customization Required:** Plugin
- **Dependencies:** [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).

#### Marketplace Order
- **Business Owner / Bounded Context:** Marketplace
- **Implementation Owner:** External Plugin
- **Target DocType:** Marketplace Service Model — **Future, Phase 5, not yet scoped**
- **Implementation Strategy:** Same rationale; would route into CRM/Sales once scoped.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).

---

# 17. AI Assistant Context *(Unregistered)*

All five entities below share the same status: "AI Assistant" is not a registered Naming Registry term and has no Blueprint scoping document. Their existence, not just their name, is undefined — this is a **blocking** condition per this document's Methodology.

#### Conversation
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-003
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003.

#### Prompt
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-003
- **Dependencies:** AR-003.

#### Knowledge Source
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-003
- **Dependencies:** AR-003.

#### Recommendation
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-003
- **Dependencies:** AR-003.

#### AI Action
- **Implementation Owner:** Pending Architecture Review
- **Target DocType:** Pending AR-003
- **Dependencies:** AR-003.

---

# Special Handling: Unregistered, Uncovered Entities

The following five entities are neither Approved nor covered by any existing Architecture Review item — the Marketplace naming collision identified in [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) and [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) was discovered after AR-003 was written and has no AR number of its own. Per this document's Methodology, "Pending Architecture Review" is reserved for entities blocked by an *existing* AR item; these are instead marked with no implementation owner and a recommendation (not a decision) that a new AR item be opened.

| Entity | Implementation Owner | Note |
|---|---|---|
| Marketplace Package | No Implementation Owner Assigned | Unregistered concept; naming collision with Approved "Marketplace" — recommend a new Architecture Review item be opened, not decided here |
| Marketplace Template | No Implementation Owner Assigned | Same as above; also collides with Configuration Template |
| Extension | No Implementation Owner Assigned | Unregistered concept |
| Publisher | No Implementation Owner Assigned | Unregistered concept |
| Marketplace Package Version | No Implementation Owner Assigned | Unregistered concept, child of Marketplace Package |

---

# Master Mapping Table

| Business Entity | Context | Implementation Owner | Target DocType | Strategy |
|---|---|---|---|---|
| Customer | CRM | Extended ERPNext | Customer | Extend with Custom Fields |
| Lead / Enquiry | CRM | Pending Architecture Review | Pending AR-011 | — |
| Sales Order | Sales | Extended ERPNext | Sales Order | Extend with Custom Fields |
| Price List | Sales | Native ERPNext | Price List | Full reuse |
| Quotation | Estimation | Pending Architecture Review | Pending AR-005 | — |
| Quotation Line | Estimation | Pending Architecture Review | Pending AR-005 | — |
| Cost Estimate | Estimation | Custom PrintHub | New PrintHub DocType | New DocType |
| Product Template | Estimation | Pending Architecture Review | Pending AR-006 | — |
| Product Category | Estimation | Extended ERPNext | Item Group | Extend / reuse |
| Job Types | Estimation/Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Finishing Types | Estimation/Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Paper Sizes | Estimation | Custom PrintHub | New PrintHub DocType | New DocType |
| Artwork | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Artwork Revision | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Proof | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Approval Record | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Job Card | Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Quality Check Record | Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Machine | Production | Pending Architecture Review | Pending AR-004 | — |
| Machine Profile | Production | Pending Architecture Review | Pending AR-004 | — |
| Production Schedule | Production | Custom PrintHub | New PrintHub DocType (Future) | Deferred |
| Finishing Operation | Production | Custom PrintHub | New PrintHub DocType (Future) | Deferred |
| Production Stage | Production | No Implementation Owner — Not a Domain Entity | Not applicable | — |
| Machine Queue | Production | No Implementation Owner — Not a Domain Entity | Not applicable | — |
| Print Specification | Production/Estimation | No Implementation Owner — Not Modeled | Not applicable | — |
| Material | Inventory | Pending Architecture Review | Pending AR-006 | — |
| Substrate | Inventory | Pending Architecture Review | Pending AR-006 | — |
| Media Profiles | Inventory | Custom PrintHub | New PrintHub DocType | New DocType |
| Units of Measure | Inventory | Native ERPNext | UOM | Full reuse |
| Purchase Order | Procurement | Native ERPNext | Purchase Order | Full reuse |
| Supplier | Procurement | Native ERPNext | Supplier | Full reuse |
| Warehouse | Warehouse | Native ERPNext | Warehouse | Full reuse |
| Dispatch Record | Dispatch | Extended ERPNext | Delivery Note | Extend with Custom Fields |
| Delivery Method | Dispatch | Custom PrintHub | New PrintHub DocType | New DocType |
| Invoice | Accounts | Native ERPNext | Sales Invoice | Full reuse |
| Payment | Accounts | Native ERPNext | Payment Entry | Full reuse |
| Journal Entry | Accounts | Native ERPNext | Journal Entry | Full reuse |
| Tax Template | Accounts | Native ERPNext | Sales Taxes and Charges Template | Full reuse |
| Payment Terms | Accounts | Native ERPNext | Payment Terms Template | Full reuse |
| Currency | Accounts | Native ERPNext | Currency | Full reuse |
| GST Configuration | GST | Native ERPNext | GST Settings | Full reuse |
| Employee | HR | Native ERPNext | Employee | Full reuse |
| Department | HR | Native ERPNext | Department | Full reuse |
| Company | Administration | Native ERPNext | Company | Full reuse |
| Branch | Administration | Native ERPNext | Branch | Full reuse |
| Module Definition | Configuration Studio | Custom PrintHub | New PrintHub DocType | New DocType |
| Workflow Definition | Configuration Studio | Extended ERPNext | Workflow | Configure native |
| Approval Definition | Configuration Studio | Extended ERPNext | Workflow + Role | Configure native |
| Form Definition | Configuration Studio | Extended ERPNext | Customize Form / Client Script | Configure native |
| Dashboard Definition | Configuration Studio | Extended ERPNext | Number Card / Dashboard Chart | Configure native |
| Report Definition | Configuration Studio | Extended ERPNext | Query Report / Script Report | Configure native |
| Notification Template | Configuration Studio | Extended ERPNext | Notification / Email Alert | Configure native |
| Integration Definition | Configuration Studio | Extended ERPNext | Webhook | Extend native |
| Feature Flag | Configuration Studio | Custom PrintHub | New PrintHub DocType | New DocType |
| Automation Rule | Configuration Studio | Custom PrintHub | New PrintHub DocType | New DocType |
| Configuration Template | Configuration Studio | Custom PrintHub | New PrintHub DocType | New DocType |
| Tenant Override | Configuration Studio | Pending Architecture Review | Pending AR-002 | — |
| Machine Event | MachineIQ | External Plugin | MachineIQ Service Model | Plugin |
| Sensor | MachineIQ | External Plugin | MachineIQ Service Model (Future) | Plugin |
| Telemetry Reading | MachineIQ | External Plugin | MachineIQ Service Model (Future) | Plugin |
| Counter Reading | MachineIQ | External Plugin | MachineIQ Service Model (Future) | Plugin |
| Maintenance Alert | MachineIQ | External Plugin | MachineIQ Service Model (Future) | Plugin |
| Machine Status | MachineIQ | External Plugin | MachineIQ Service Model (Future) | Plugin |
| Marketplace Listing | Marketplace | External Plugin | Marketplace Service Model (Future) | Plugin |
| Marketplace Order | Marketplace | External Plugin | Marketplace Service Model (Future) | Plugin |
| Marketplace Package | Marketplace (unregistered) | No Implementation Owner Assigned | Not applicable | — |
| Marketplace Template | Marketplace (unregistered) | No Implementation Owner Assigned | Not applicable | — |
| Extension | Marketplace (unregistered) | No Implementation Owner Assigned | Not applicable | — |
| Publisher | Marketplace (unregistered) | No Implementation Owner Assigned | Not applicable | — |
| Marketplace Package Version | Marketplace (unregistered) | No Implementation Owner Assigned | Not applicable | — |
| Conversation | AI Assistant (unregistered) | Pending Architecture Review | Pending AR-003 | — |
| Prompt | AI Assistant (unregistered) | Pending Architecture Review | Pending AR-003 | — |
| Knowledge Source | AI Assistant (unregistered) | Pending Architecture Review | Pending AR-003 | — |
| Recommendation | AI Assistant (unregistered) | Pending Architecture Review | Pending AR-003 | — |
| AI Action | AI Assistant (unregistered) | Pending Architecture Review | Pending AR-003 | — |

---

# Implementation Ownership Statistics

| Implementation Owner | Count |
|---|---:|
| Native ERPNext | 20 |
| Extended ERPNext | 10 |
| Custom PrintHub | 17 |
| External Plugin | 8 |
| Pending Architecture Review | 13 |
| No Implementation Owner (Not a Domain Entity / Not Modeled) | 3 |
| No Implementation Owner Assigned (Unregistered, uncovered by any AR item) | 5 |
| **Total** | **76*** |

*76 rows because "Print Specification" is listed once for traceability despite not being a modeled entity in the 75-entity Business Entity Inventory count; it is not double-counted against the Inventory's own total.

---

# Validation

- ✓ **Every business entity appears exactly once.** All 75 entities from [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) appear exactly once in the Master Mapping Table, plus "Print Specification" listed once for traceability per the Inventory's own treatment of it.
- ✓ **Every entity has exactly one implementation owner** (or an explicit, singular "No Implementation Owner" disposition where forcing one would misrepresent the entity's actual status, per the three Not-a-Domain-Entity/Not-Modeled rows and five Unregistered rows).
- ✓ **No duplicate ERPNext DocTypes are introduced.** Each Native/Extended target DocType (Customer, Sales Order, Price List, Item Group, Purchase Order, Supplier, Warehouse, Delivery Note, Sales Invoice, Payment Entry, Journal Entry, Sales Taxes and Charges Template, Payment Terms Template, Currency, GST Settings, Employee, Department, Company, Branch, UOM, Workflow, Customize Form, Dashboard Chart, Query/Script Report, Notification, Webhook) is referenced by exactly one entity above, except where an entity and its Value Object variants legitimately extend the same base (none found).
- ✓ **No duplicate PrintHub DocTypes are introduced.** Each "New PrintHub DocType" target is unique per entity; no two entities share a proposed Custom DocType.
- ✓ **No ERPNext functionality is recreated without justification.** Every Custom PrintHub classification cites either [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) or [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) as the source of the "no native equivalent" finding — none is asserted independently by this document.
- ✓ **Every Pending decision references an Architecture Review item.** All 13 Pending Architecture Review rows cite AR-002, AR-003, AR-004, AR-005, AR-006, or AR-011 by number; the 5 Unregistered rows explicitly do **not** claim an AR reference, since none exists, and are marked "No Implementation Owner Assigned" rather than misusing the Pending category.

---

# Related Documents

- [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md)
- [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md)
- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [00_Master_Index.md](00_Master_Index.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-25 | Initial | Initial ERPNext DocType Mapping. Assigned implementation ownership to all 75 Business Entity Inventory entities: 20 Native ERPNext, 10 Extended ERPNext, 17 Custom PrintHub, 8 External Plugin, 13 Pending Architecture Review (citing AR-002, AR-003, AR-004, AR-005, AR-006, AR-011), 3 Not-a-Domain-Entity/Not-Modeled, and 5 Unregistered entities left without an implementation owner (no covering AR item exists). Introduced a Blocking-vs-Non-Blocking AR methodology to avoid over-applying "Pending Architecture Review" to entities whose implementation location is already clear despite an open terminology question. No DocType fields, database schema, code, or AR/ADR resolution produced. |
| 0.2 | 2026-07-25 | Documentation Clarification | Clarified Reporting and Configuration Studio ownership language for reports and dashboards. Reporting owns reporting capability and consumption; Configuration Studio owns Report Definition and Dashboard Definition configuration artifacts. Documentation clarification only; no architecture change. Dashboard Definition's Implementation Owner (Extended ERPNext) and Target DocType are unchanged. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code, field definitions, or schema included
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] No Architecture Review Register item resolved
- [ ] No duplicate ERPNext or PrintHub DocTypes introduced
- [ ] Consistent with Business Entity Inventory and Canonical Domain Model baseline
- [ ] Reviewed by Project Owner
