# ERPNext DocType Mapping

Version:
0.9

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-26

---

# Purpose

Assign exactly one implementation owner — Native ERPNext, Extended ERPNext, Custom PrintHub, External Plugin, or Pending Architecture Review — to every business entity cataloged in [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) and organized in [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md). This is the authoritative implementation ownership document: it determines *where* each entity lives, not how it is built.

---

# Scope

Covers implementation ownership assignment for every business entity (Aggregate Root, Child Entity, and Value Object) in the Canonical Domain Model. Does not cover database schema, field design, Frappe model code, or the three Domain Services (Estimation Engine, Production Planning/Scheduling Engine, Configuration Resolution Engine) identified in the Canonical Domain Model — Domain Services are Application-layer behavior, not persistent entities, and therefore have no DocType mapping of their own; each is implemented as logic operating over the DocTypes mapped below. Does not resolve any Architecture Review Register item.

---

# Mapping Methodology

Not every open Architecture Review (AR) item blocks knowing *where* an entity's implementation should live. This document distinguishes two kinds of AR references:

- **Blocking AR items** — the AR item concerns the entity's structural target (which ERPNext object, if any, underlies it), so the Implementation Owner genuinely cannot be assigned yet: **AR-002** (Tenant Override's data model depends on Tenant/Company resolution), **AR-011** (Lead/Enquiry's Opportunity mapping). AR-004 (Machine/Machine Profile's ERPNext base) is Resolved (Option C, 2026-09-20), AR-005 (Quotation/Quotation Line's document strategy) is Resolved (Option B, 2026-09-26), and AR-006 (Material/Substrate/Product Template's Item mapping) is Resolved (Option C, 2026-09-26; ADR-016); none of these three blocks any entity any longer. The five candidate AI Assistant sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) have no covering Architecture Review item at all — not AR-003, which is Resolved (2026-09-19) and covers only "AI Assistant" itself as a Proposed name — so they are not "blocked by an AR item" in this document's sense; see the Special Handling section for their treatment as Unregistered, Uncovered Entities.
- **Non-blocking AR items** — the AR item concerns terminology only; the entity's implementation location is already clear regardless of which name is eventually chosen: **AR-007** (Purchasing/Procurement naming — does not change that Purchase Order/Supplier are Native ERPNext), **AR-008** (Dispatch/Delivery naming — does not change that Dispatch Record extends Delivery Note), **AR-009** (Quality module naming/scope — does not change that Quality Check Record is a Custom child entity today), **AR-010** (BOM necessity — affects the Estimation Engine Domain Service's internals, not any entity's DocType ownership), and **AR-003 for "Print Specification"** (Resolved — Not Adopted; "Print Specification" is not modeled as an entity at all). Approval Record's own naming/classification was never one of AR-003's eight terms and is outside AR-003 entirely — it is a separate, currently unassigned governance question, not an AR-003 non-blocking citation.

Only entities gated by a **blocking** AR item are classified "Pending Architecture Review" below. Entities affected only by a non-blocking AR item are assigned a definite Implementation Owner, with the AR item cited in Dependencies for traceability. Entities with no covering AR item at all (the five AI Assistant sub-entities, and the five Marketplace-collision entities) are marked "No Implementation Owner Assigned," not "Pending Architecture Review."

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
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Quotation (Extended)
- **Implementation Strategy:** Resolved — AR-005, Option B (2026-09-26): Custom Cost Estimate and pricing logic feed ERPNext's native Quotation through a governed handoff. Native Quotation remains the customer-facing document; any extension needed for the handoff is deferred to downstream DocType design.
- **Customization Required:** Not yet determined — deferred to downstream DocType-design task
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-005 (Resolved); [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md); [14_Quotation_Engine.md](../blueprint/14_Quotation_Engine.md).

#### Quotation Line
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Extended ERPNext
- **Target DocType:** Not yet determined — deferred to downstream DocType-design task
- **Implementation Strategy:** Follows from Quotation's now-Resolved document strategy; exact child-table target and field structure not specified here.
- **Customization Required:** Not yet determined — deferred to downstream DocType-design task
- **Dependencies:** AR-005 (Resolved); also touches AR-010. AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework.

#### Cost Estimate
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** No ERPNext native equivalent; a distinct internal artifact from Quotation per [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md), supporting the Estimation Engine Domain Service (see [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md)). The handoff from this artifact to native Quotation is governed per [14_Quotation_Engine.md](../blueprint/14_Quotation_Engine.md), Version 0.1 (AR-005, Option B).
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Print Estimation & Costing Engine").

#### Product Template
- **Business Owner / Bounded Context:** Estimation
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Resolved — AR-006, Option C (2026-09-26): Product Template is its own Custom PrintOS artifact in the Estimation context, structurally independent of Material and Item.
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006 (Resolved); [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md); [17_Inventory_Model.md](../blueprint/17_Inventory_Model.md).

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
- **Target DocType:** New custom PrintHub DocType — technical name `PrintHub Artwork`, **Naming Registry status: Proposed, not Approved** (per Project Owner decision dated 2026-08-22; `../standards/Naming_Registry.md` Section 13a).
- **Implementation Strategy:** No ERPNext native equivalent beyond generic File attachment; Core Domain per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md), Customize per [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 4. **ERPNext core remains immutable** — no ERPNext DocType is modified to support Artwork.
- **Customization Required:** New DocType
- **Dependencies:** [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Artwork & Proof Lifecycle").

#### Artwork Revision
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** **New standalone custom PrintHub DocType in `printos_core`** — technical name `PrintHub Artwork Revision`, **Naming Registry status: Proposed, not Approved**. **Corrected 2026-07-31: this entity was previously mapped as a child table of Artwork. That mapping is withdrawn.**
- **Standalone rationale:** independently approvable; requires its own state; requires independent permissions; must be lockable; requires file-integrity evidence; may be referenced by Production Artwork Set membership; may require unique database constraints; must preserve immutable approval evidence. A child table can satisfy none of these.
- **Implementation Strategy:** Same rationale as Artwork; built on ERPNext's native File mechanism **for underlying storage only — File is never the approval authority**.
- **Customization Required:** New DocType
- **Dependencies:** Same as Artwork.

#### Customer Approval Evidence
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** **New standalone, Artwork-internal custom PrintHub DocType** — technical name `PrintHub Customer Approval Evidence`, **Naming Registry status: Proposed, not Approved** (added 2026-08-22 as the first documented technical-name candidate for this already-governed entity; the entity and its business rules, defined in `../blueprint/18_Artwork_Management.md` §6.2a and `../database/Artwork_Authority_DocType_Specification.md` §7.1, are unchanged).
- **Implementation Strategy:** No ERPNext native equivalent. The durable, mandatory record of customer approval for one exact Artwork Revision; not itself approval authority for Job Card release.
- **Customization Required:** New DocType
- **Dependencies:** Artwork; Artwork Revision; Proof.

#### Production Artwork Set
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New custom PrintHub DocType — technical name `PrintHub Production Artwork Set`, **Naming Registry status: Proposed, not Approved**.
- **Implementation Strategy:** No ERPNext native equivalent. This is the **final production-release authority consumed by the Job Card**: it binds one exact approved Artwork Revision per required Artwork for a Sales Order. At most one set is Approved for Production per Sales Order.
- **Customization Required:** New DocType
- **Dependencies:** Artwork; Artwork Revision; Sales Order (Submitted); Company.

#### Production Artwork Set Item
- **Business Owner / Bounded Context:** Artwork
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New custom PrintHub DocType — **child table of Production Artwork Set** — technical name `PrintHub Production Artwork Set Item`, **Naming Registry status: Proposed, not Approved**.
- **Implementation Strategy:** Immutable membership rows binding one Artwork to one exact Artwork Revision. Acceptable as a child table **because its rows are immutable aggregate membership values — it is not the approval authority itself**.
- **Customization Required:** New DocType (child table)
- **Dependencies:** Production Artwork Set; Artwork; Artwork Revision.

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
- **Implementation Strategy:** No ERPNext native equivalent to a business-specific approval outcome record; built on top of Frappe's native Workflow engine (used by the Approval Definition configuration entity) but the outcome record itself is Custom. The "Approval Management" module-name question (AR-003, Resolved 2026-09-19, mapped to Approval Designer) is non-blocking and distinct from Approval Record's own naming — it does not change where this entity lives. Approval Record's own naming/classification was never one of AR-003's eight terms and remains a separate, currently unassigned governance question, outside AR-003.
- **Customization Required:** New DocType
- **Dependencies:** [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md).

**Supporting infrastructure note — Frappe `File` (updated 2026-08-22):** Artwork evidence storage relies on Frappe's native `File` primitive. `File` is **not a Business Entity Inventory entity** — it does not appear in `../database/Business_Entity_Inventory.md` or `../architecture/Canonical_Domain_Model.md` — and is therefore **intentionally excluded from the entity-scoped Master Mapping Table above**, which assigns ownership only to cataloged business entities. `File` is **storage only and is never approval authority**; this rule is normatively governed by `../blueprint/18_Artwork_Management.md` Section 10 (File Integrity and Authority), not by this document. This note is documentation-scope clarification only: it introduces no new File behavior, implementation mechanism, or coding decision, and does not modify the Artwork System Design's governing rule.

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
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Resolved — AR-004, Option C (2026-09-20): Machine is a wholly Custom PrintOS DocType, avoiding ERPNext Asset/Workstation upgrade-path coupling and forgoing native reuse, per [Architecture Review Register](../decisions/Architecture_Review_Register.md) and [16_Print_Machine_Model.md](../blueprint/16_Print_Machine_Model.md), Version 0.1.
- **Customization Required:** New DocType
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-004 (Resolved); [16_Print_Machine_Model.md](../blueprint/16_Print_Machine_Model.md).

#### Machine Profile
- **Business Owner / Bounded Context:** Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType
- **Implementation Strategy:** Consistently classified Custom in both [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) and [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md), and now formally definite following Machine's own Resolution (AR-004, Option C, 2026-09-20).
- **Customization Required:** New DocType
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-004 (Resolved).

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
- **Implementation Strategy:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 has Resolved (2026-09-19) that "Print Specification" is **Not Adopted** as a module, DocType, entity, or umbrella object — this is a resolved disposition, not a pending one. Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) Special Review, the underlying need is already covered by Product Template, Job Types, Finishing Types, and Paper Sizes (each mapped independently above).
- **Customization Required:** Not applicable
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 (Resolved — Not Adopted; entity not modeled).

---

# 6. Inventory Context

#### Material
- **Business Owner / Bounded Context:** Inventory
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** New PrintHub DocType, linked to native Item
- **Implementation Strategy:** Resolved — AR-006, Option C (2026-09-26): Material is a Custom PrintOS master record linked to native ERPNext Item.
- **Customization Required:** New DocType, Runtime Logic
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006 (Resolved); [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md); [17_Inventory_Model.md](../blueprint/17_Inventory_Model.md).

#### Substrate
- **Business Owner / Bounded Context:** Inventory / Production
- **Implementation Owner:** Custom PrintHub
- **Target DocType:** Not yet determined — deferred to downstream DocType-design task
- **Implementation Strategy:** Substrate is a specialization of Material, holding print-specific properties (GSM, media profile). Its exact structure (child table of Material's Custom DocType vs. standalone linked DocType) is deferred to downstream DocType design.
- **Customization Required:** Not yet determined — deferred to downstream DocType-design task
- **Dependencies:** [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006 (Resolved); [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md).

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
- **Implementation Strategy:** Per [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md), modeled as a MachineIQ Value Object; note a simple current-state field on the Machine DocType itself may also be warranted, now that Machine's own base is Resolved as a wholly Custom PrintOS DocType (AR-004, Option C, 2026-09-20) — this remains an observation, not a decision, and does not itself add any field to Machine.
- **Customization Required:** Plugin
- **Dependencies:** [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md); AR-004 (Resolved — non-blocking observation, unchanged in substance).

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

# 17. AI Assistant Context *(Proposed name only)*

"AI Assistant" itself is registered as a **Proposed name only** ([Naming Registry](../standards/Naming_Registry.md) Section 40; [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-003**, Resolved 2026-09-19), with no Approved Bounded Context, architecture, provider, model, plugin design, implementation owner, or implementation authorization. The five entities below were never among AR-003's eight named terms and have no covering Architecture Review item of their own; their existence, not just their name, remains undefined, pending separate future governance. Per this document's Methodology, "Pending Architecture Review" is reserved for entities blocked by an *existing* AR item — since no AR item covers these five, they are marked "No Implementation Owner Assigned" rather than "Pending Architecture Review," consistent with the Special Handling treatment below.

#### Conversation
- **Implementation Owner:** No Implementation Owner Assigned
- **Target DocType:** Not applicable
- **Dependencies:** None — unregistered, outside AR-003's scope, no covering Architecture Review item; pending separate future governance.

#### Prompt
- **Implementation Owner:** No Implementation Owner Assigned
- **Target DocType:** Not applicable
- **Dependencies:** None — unregistered, outside AR-003's scope, no covering Architecture Review item; pending separate future governance.

#### Knowledge Source
- **Implementation Owner:** No Implementation Owner Assigned
- **Target DocType:** Not applicable
- **Dependencies:** None — unregistered, outside AR-003's scope, no covering Architecture Review item; pending separate future governance.

#### Recommendation
- **Implementation Owner:** No Implementation Owner Assigned
- **Target DocType:** Not applicable
- **Dependencies:** None — unregistered, outside AR-003's scope, no covering Architecture Review item; pending separate future governance.

#### AI Action
- **Implementation Owner:** No Implementation Owner Assigned
- **Target DocType:** Not applicable
- **Dependencies:** None — unregistered, outside AR-003's scope, no covering Architecture Review item; pending separate future governance.

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
| Quotation | Estimation | Extended ERPNext | Quotation (Extended) | Deferred |
| Quotation Line | Estimation | Extended ERPNext | Not yet determined | Deferred |
| Cost Estimate | Estimation | Custom PrintHub | New PrintHub DocType | New DocType |
| Product Template | Estimation | Custom PrintHub | New PrintHub DocType | New DocType |
| Product Category | Estimation | Extended ERPNext | Item Group | Extend / reuse |
| Job Types | Estimation/Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Finishing Types | Estimation/Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Paper Sizes | Estimation | Custom PrintHub | New PrintHub DocType | New DocType |
| Artwork | Artwork | Custom PrintHub | New PrintHub DocType (`PrintHub Artwork` — Naming Registry: Proposed, not Approved) | New DocType |
| Artwork Revision | Artwork | Custom PrintHub | **New standalone PrintHub DocType** (`PrintHub Artwork Revision` — Naming Registry: Proposed, not Approved) — *corrected 2026-07-31 from child table of Artwork* | New DocType |
| Customer Approval Evidence | Artwork | Custom PrintHub | **New standalone, Artwork-internal PrintHub DocType** (`PrintHub Customer Approval Evidence` — Naming Registry: Proposed, not Approved) — *added 2026-08-22 as first documented technical-name candidate for this already-governed entity* | New DocType |
| Production Artwork Set | Artwork | Custom PrintHub | New PrintHub DocType (`PrintHub Production Artwork Set` — Naming Registry: Proposed, not Approved) | New DocType |
| Production Artwork Set Item | Artwork | Custom PrintHub | New PrintHub DocType — child table of Production Artwork Set (`PrintHub Production Artwork Set Item` — Naming Registry: Proposed, not Approved) | New DocType |
| Proof | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Approval Record | Artwork | Custom PrintHub | New PrintHub DocType | New DocType |
| Job Card | Production | Custom PrintHub | New PrintHub DocType (`PrintHub Job Card` — Naming Registry: Proposed, not Approved; ADR-014 is rationale only for the business term `Job Card`, not approval of this exact prefixed name) | New DocType |
| Quality Check Record | Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Machine | Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Machine Profile | Production | Custom PrintHub | New PrintHub DocType | New DocType |
| Production Schedule | Production | Custom PrintHub | New PrintHub DocType (Future) | Deferred |
| Finishing Operation | Production | Custom PrintHub | New PrintHub DocType (Future) | Deferred |
| Production Stage | Production | No Implementation Owner — Not a Domain Entity | Not applicable | — |
| Machine Queue | Production | No Implementation Owner — Not a Domain Entity | Not applicable | — |
| Print Specification | Production/Estimation | No Implementation Owner — Not Modeled (AR-003 Resolved: Not Adopted) | Not applicable | — |
| Material | Inventory | Custom PrintHub | New PrintHub DocType, linked to native Item | New DocType |
| Substrate | Inventory | Custom PrintHub | Not yet determined | Deferred |
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
| Conversation | AI Assistant (Proposed name only; outside AR-003) | No Implementation Owner Assigned | Not applicable | — |
| Prompt | AI Assistant (Proposed name only; outside AR-003) | No Implementation Owner Assigned | Not applicable | — |
| Knowledge Source | AI Assistant (Proposed name only; outside AR-003) | No Implementation Owner Assigned | Not applicable | — |
| Recommendation | AI Assistant (Proposed name only; outside AR-003) | No Implementation Owner Assigned | Not applicable | — |
| AI Action | AI Assistant (Proposed name only; outside AR-003) | No Implementation Owner Assigned | Not applicable | — |

---

# Implementation Ownership Statistics

| Implementation Owner | Count |
|---|---:|
| Native ERPNext | 16 |
| Extended ERPNext | 13 |
| Custom PrintHub | 26 |
| External Plugin | 8 |
| Pending Architecture Review | 2 |
| No Implementation Owner (Not a Domain Entity / Not Modeled) | 3 |
| No Implementation Owner Assigned (Unregistered, uncovered by any AR item) | 10 |
| **Total** | **78*** |

*78 rows: all 75 real entities from the 75-entity Business Entity Inventory count, plus three explicitly disclosed non-entity traceability rows — "Print Specification" (not a modeled entity), "Production Stage" and "Machine Queue" (both explicitly "Not a Distinct Entity" per the Inventory's own treatment) — none of the three double-counted against the Inventory's own 75-entity total. This Version 0.4 revision (a) added the previously-missing mapping for **Customer Approval Evidence**, the 75th real entity, closing a pre-existing completeness gap, and (b) removed the **`File (Artwork evidence storage)`** row, which was never a Business Entity Inventory entity and is out of this table's entity-only scope; File's storage-only, never-approval-authority role is preserved in the Artwork Context narrative and remains governed by `../blueprint/18_Artwork_Management.md` Section 10. These figures also correct a pre-existing arithmetic mismatch between this table and the actual row-by-row content that predates this task (see Revision History). This Version 0.5 revision moves the five AI Assistant candidate sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) from Pending Architecture Review (14 → 9) to No Implementation Owner Assigned (5 → 10), since AR-003's Resolved disposition (2026-09-19) covers "AI Assistant" itself as a Proposed name only and does not cover these five sub-entities, which have no covering Architecture Review item of their own; Total remains unchanged at 78 (no row added, removed, or reassigned an actual implementation owner). This Version 0.7 revision moves Machine and Machine Profile from Pending Architecture Review (9 → 7) to Custom PrintHub (21 → 23), since AR-004's Resolved disposition (Option C, 2026-09-20) settles both entities' implementation ownership; Total remains unchanged at 78. This Version 0.8 revision moves Quotation and Quotation Line from Pending Architecture Review (7 → 5) to Extended ERPNext (11 → 13), since AR-005's Resolved disposition (Option B, 2026-09-26) settles Quotation's implementation ownership, deferring only downstream field/child-table detail; Total remains unchanged at 78. This Version 0.9 revision moves Material, Substrate, and Product Template from Pending Architecture Review (5 → 2) to Custom PrintHub (23 → 26), since AR-006's Resolved disposition (Option C, 2026-09-26; ADR-016) settles all three entities' implementation ownership — Substrate's exact DocType structure remains deferred to downstream design, but its Implementation Owner is settled; Total remains unchanged at 78.

---

# Validation

- ✓ **Every business entity appears exactly once.** All 75 real entities from [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) appear exactly once in the Master Mapping Table (2026-08-22: **Customer Approval Evidence** added, closing the only prior real-entity omission), plus three explicitly disclosed non-entity traceability rows — "Print Specification," "Production Stage," and "Machine Queue" — none double-counted against the Inventory's own 75-entity total. Total table rows: **78**. Frappe `File` is intentionally **excluded** from this table as a supporting infrastructure primitive, not a Business Entity Inventory entity (see the Artwork Context narrative note); no duplicate normalized entity name exists anywhere in the table.
- ✓ **Every entity has exactly one implementation owner** (or an explicit, singular "No Implementation Owner" disposition where forcing one would misrepresent the entity's actual status, per the three Not-a-Domain-Entity/Not-Modeled rows and ten Unregistered rows).
- ✓ **No duplicate ERPNext DocTypes are introduced.** Each Native/Extended target DocType (Customer, Sales Order, Price List, Item Group, Purchase Order, Supplier, Warehouse, Delivery Note, Sales Invoice, Payment Entry, Journal Entry, Sales Taxes and Charges Template, Payment Terms Template, Currency, GST Settings, Employee, Department, Company, Branch, UOM, Workflow, Customize Form, Dashboard Chart, Query/Script Report, Notification, Webhook) is referenced by exactly one entity above, except where an entity and its Value Object variants legitimately extend the same base (none found).
- ✓ **No duplicate PrintHub DocTypes are introduced.** Each "New PrintHub DocType" target is unique per entity; no two entities share a proposed Custom DocType.
- ✓ **No ERPNext functionality is recreated without justification.** Every Custom PrintHub classification cites either [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) or [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) as the source of the "no native equivalent" finding — none is asserted independently by this document.
- ✓ **Every Pending decision references an Architecture Review item.** All 2 Pending Architecture Review rows cite AR-002 or AR-011 by number (AR-004 Resolved, 2026-09-20; AR-005 Resolved, 2026-09-26; and AR-006 Resolved, 2026-09-26, all three removed from this count); the 10 Unregistered rows (5 Marketplace-collision entities plus the 5 AI Assistant candidate sub-entities, moved here in the Version 0.5 revision since AR-003's Resolved disposition covers "AI Assistant" itself only, not these sub-entities) explicitly do **not** claim an AR reference, since none exists, and are marked "No Implementation Owner Assigned" rather than misusing the Pending category.

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
| 0.4 | 2026-08-22 | Naming-Status Synchronization and Mapping-Scope Correction | **Bounded naming-status synchronization and mapping-scope correction only; Draft status is retained.** Per explicit Project Owner decision dated 2026-08-22, six technical DocType names entered Naming Registry **Proposed** status (`../standards/Naming_Registry.md` Section 13a): `PrintHub Artwork`, `PrintHub Artwork Revision`, `PrintHub Customer Approval Evidence`, `PrintHub Production Artwork Set`, `PrintHub Production Artwork Set Item`, and `PrintHub Job Card`. Synchronized the five existing candidate occurrences (Artwork, Artwork Revision, Production Artwork Set, Production Artwork Set Item entries; summary table rows) from "proposed pending governed naming treatment" to "Naming Registry: Proposed, not Approved." **Added the previously-missing Customer Approval Evidence entity mapping** — the 75th real Business Entity Inventory entity, part of that document's 75-entity count since its own Version 0.3 (2026-07-31) but never mapped here until this correction — recording its **first documented technical-name candidate** (`PrintHub Customer Approval Evidence`); the entity's business rules (`../blueprint/18_Artwork_Management.md` §6.2a; `../database/Artwork_Authority_DocType_Specification.md` §7.1) are unchanged. **Following a subsequent Project Owner mapping-scope decision (Option B, 2026-08-22), removed the historically-added `File (Artwork evidence storage)` row from the Master Mapping Table**, because Frappe `File` is supporting infrastructure — not a Business Entity Inventory entity and not present in `../architecture/Canonical_Domain_Model.md` — and therefore outside this table's entity-only scope; File's storage-only, never-approval-authority role is preserved in the Artwork Context narrative and remains governed by `../blueprint/18_Artwork_Management.md` Section 10; no new File behavior, implementation mechanism, or coding decision is introduced. **A full row-by-row reconciliation, not a simple `+1` delta, was performed**: the committed Version 0.3 Implementation Ownership Statistics were already inconsistent with the table's actual rows — the true underlying HEAD counts were **Native ERPNext 16, Extended ERPNext 11, Custom PrintHub 20, External Plugin 8, Pending Architecture Review 14, No Implementation Owner (Not a Domain Entity / Not Modeled) 3, No Implementation Owner Assigned 5, Total 78** — against a committed statistics table that incorrectly stated Native ERPNext 20, Extended ERPNext 10, Custom PrintHub 17, Pending Architecture Review 13, Total 76. This Version 0.4 corrects the statistics to their true figures: **Custom PrintHub 20 → 21** (the one net addition, Customer Approval Evidence in, File out of the table entirely since File was never counted in any prior statistics category) and **Total 78 → 78** (one real entity added, one out-of-scope infrastructure row removed, net zero row-count change) — previously stated figures of Custom PrintHub 17/18 and Total 76/77 are superseded by these corrected figures. Updated the Validation section to state 75 real entities, 3 disclosed non-entity rows, 78 total rows, 14 Pending Architecture Review rows, and File's intentional exclusion. **Added `PrintHub Job Card`'s Naming Registry status to the summary table's Job Card row**, recording that [ADR-014](../decisions/ADR-014-Production-Terminology.md) is cited only as rationale/traceability for the business term `Job Card` — ADR-014 states "Technical usage: DocType name 'Job Card'" (unprefixed) and did not approve the exact prefixed string `PrintHub Job Card`. **No ERPNext fit/gap conclusion or other mapping decision was altered beyond naming status, the one new entity mapping, and the one infrastructure-row removal; no mapping implementation, database schema, field, or coding authority is introduced.** `AR-003` was not resolved or modified. No implementation was authorized; no source was inspected or modified. |
| 0.9 | 2026-09-26 | AR-006 Disposition Synchronization | Corrected active statements following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-006's Resolved disposition (Option C, 2026-09-26; [ADR-016-Item-Material-Product-Template-Mapping](../decisions/ADR-016-Item-Material-Product-Template-Mapping.md)): Material is a Custom PrintOS master record linked to native Item; Substrate is a specialization of Material; Product Template is its own Custom PrintOS artifact, independent of Material and Item. Removed AR-006 from the Mapping Methodology's Blocking AR items list, noting AR-004, AR-005, and AR-006 as all Resolved and no longer blocking. **Product Template** entry updated from Implementation Owner "Pending Architecture Review" / Target DocType "Pending AR-006" to Implementation Owner "Custom PrintHub" / Target DocType "New PrintHub DocType." **Material** entry updated to Implementation Owner "Custom PrintHub" / Target DocType "New PrintHub DocType, linked to native Item." **Substrate** entry updated to Implementation Owner "Custom PrintHub" with Target DocType "Not yet determined — deferred to downstream DocType-design task," since Substrate's exact structure (child table vs. standalone linked DocType) remains out of scope here. All three entries now cite the newly populated [17_Inventory_Model.md](../blueprint/17_Inventory_Model.md), Version 0.1 (an existing, previously empty Placeholder — no new file created), and ADR-016. Updated the three corresponding Master Mapping Table rows. Updated the Implementation Ownership Statistics: Pending Architecture Review 5 → 2, Custom PrintHub 23 → 26, Total unchanged at 78. Updated the Validation section's Pending-decision statement to match. No other entity's implementation ownership changed; no ERPNext fit/gap conclusion altered; no Architecture Review Register item was created or modified beyond AR-006 itself; no handoff mechanics, fields, Custom Fields, or child-table structure were specified; no implementation was authorized; no source was inspected or modified. |
| 0.8 | 2026-09-26 | AR-005 Disposition Synchronization | Corrected active statements following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-005's Resolved disposition (Option B, 2026-09-26): Custom Cost Estimate and pricing logic feed ERPNext's native customer-facing Quotation through a governed handoff. Removed AR-005 from the Mapping Methodology's Blocking AR items list, noting both AR-004 and AR-005 as Resolved and no longer blocking. **Quotation** entry updated from Implementation Owner "Pending Architecture Review" / Target DocType "Pending AR-005" to Implementation Owner "Extended ERPNext" / Target DocType "Quotation (Extended)," citing the newly populated [14_Quotation_Engine.md](../blueprint/14_Quotation_Engine.md), Version 0.1 (an existing, previously empty Placeholder — no new file created). **Quotation Line** entry updated to Implementation Owner "Extended ERPNext" with Target DocType "Not yet determined — deferred to downstream DocType-design task," since the exact child-table target and field structure remain out of scope here; its Dependencies retain AR-010, using the normalized wording: AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework. **Cost Estimate** entry's Implementation Strategy gained one sentence citing the governed handoff in `14_Quotation_Engine.md`; its Implementation Owner (Custom PrintHub) and Target DocType are unchanged. Updated the two corresponding Master Mapping Table rows. Updated the Implementation Ownership Statistics: Pending Architecture Review 7 → 5, Extended ERPNext 11 → 13, Total unchanged at 78. Updated the Validation section's Pending-decision statement to match. No other entity's implementation ownership changed; no ERPNext fit/gap conclusion altered; no Architecture Review Register item was created or modified; AR-005 itself was not further modified by this document; no handoff mechanics, fields, Custom Fields, or child-table structure were specified; no implementation was authorized; no source was inspected or modified. |
| 0.7 | 2026-09-20 | AR-004 Disposition Synchronization | Corrected active statements following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-004's Resolved disposition (Option C, 2026-09-20). Removed AR-004 from the Mapping Methodology's Blocking AR items list. **Machine** and **Machine Profile** entries updated from Implementation Owner "Pending Architecture Review" / Target DocType "Pending AR-004" to Implementation Owner "Custom PrintHub" / Target DocType "New PrintHub DocType," citing the newly populated `../blueprint/16_Print_Machine_Model.md`, Version 0.1 (an existing, previously empty Placeholder — no new file created). Updated the **Machine Status** entity's Implementation Strategy and Dependencies to record Machine's base as Resolved rather than pending, preserving its own External Plugin/Future classification unchanged. Updated the two corresponding Master Mapping Table rows. Updated the Implementation Ownership Statistics: Pending Architecture Review 9 → 7, Custom PrintHub 21 → 23, Total unchanged at 78. Updated the Validation section's Pending-decision statement to match. No other entity's implementation ownership changed; no ERPNext fit/gap conclusion altered; no Architecture Review Register item was created or modified; AR-004 itself was not further modified by this document; no implementation was authorized; no source was inspected or modified. |
| 0.6 | 2026-09-20 | Approval Record Dependency Correction | Removed [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 from the Approval Record entry's **Dependencies** line, retaining [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md) as its sole dependency. AR-003 does not govern Approval Record — Approval Record's own naming/classification was never one of AR-003's eight named terms (only the requested "Approval Management" module name, mapped to Approval Designer, was) and remains a separate, currently unassigned governance question, outside AR-003, as the entry's own Implementation Strategy sentence (immediately preceding this Dependencies line) already stated and continues to state unchanged. This correction removes a dependency citation that had continued to name AR-003 despite that same explanation; it does not alter Approval Record's Business Owner, Implementation Owner, Target DocType, Customization Required, or Implementation Strategy fields, and does not alter any other entity's mapping, the Master Mapping Table, or the Implementation Ownership Statistics. No Architecture Review Register item was created or modified; AR-003 itself was not further modified by this document; no implementation was authorized; no source was inspected or modified. |
| 0.5 | 2026-09-20 | AR-003 Disposition Synchronization | Corrected active statements following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003's Resolved disposition (2026-09-19). **Approval Record** remains Custom PrintHub, unaffected; corrected its Implementation Strategy and Dependencies to clarify that the "Approval Management" module-name question AR-003 resolved (mapped to Approval Designer) is distinct from Approval Record's own naming/classification, which was never one of AR-003's eight terms and remains a separate, currently unassigned governance question, outside AR-003. **"Print Specification"** remains No Implementation Owner — Not Modeled; corrected its Implementation Strategy and Dependencies to record AR-003's Resolved (not pending) disposition of Not Adopted, and the Master Mapping Table row accordingly. **AI Assistant Context (Section 17):** corrected the section heading and introduction to record "AI Assistant" as a registered Proposed name only (Naming Registry Section 40), and moved its five candidate sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) from "Pending Architecture Review" / "Pending AR-003" to **"No Implementation Owner Assigned"** with "Not applicable" Target DocType and Dependencies, since no Architecture Review item — not AR-003, which covers only "AI Assistant" itself — covers their existence or placement; updated the five corresponding Master Mapping Table rows to match. Corrected the Mapping Methodology's Blocking/Non-Blocking AR-item lists to remove the AI Assistant sub-entities from the Blocking list (they were never genuinely blocked by an *existing* AR item) and to correct the AR-003 Non-Blocking citation to "Print Specification" only, not Approval Record. Updated the Implementation Ownership Statistics table: Pending Architecture Review 14 → 9, No Implementation Owner Assigned 5 → 10, Total unchanged at 78. Updated the Validation section's Pending-decision and entity-count statements to match. No implementation owner, DocType, module, or mapping was assigned to any of the five AI Assistant sub-entities by this task; no other entity's implementation ownership changed; no ERPNext fit/gap conclusion altered; no Architecture Review Register item was created or modified; AR-003 itself was not further modified by this document; no implementation was authorized; no source was inspected or modified. |
| 0.3 | 2026-07-31 | Artwork Production Authority Mapping Correction | Corrected and extended the Artwork Context mapping following the Project Owner's production-capable Artwork track selection (2026-07-30) and approval of the Artwork design defaults (2026-07-31). **Corrected Artwork Revision from "New PrintHub DocType (child table of Artwork)" to a new standalone custom PrintHub DocType in `printos_core`**, recording the standalone rationale (independently approvable; own state; independent permissions; lockable; file-integrity evidence; referenced by Production Artwork Set membership; may require unique database constraints; preserves immutable approval evidence). Added mappings for **Production Artwork Set** (new custom PrintHub DocType; the final production-release authority consumed by the Job Card; at most one Approved for Production per Sales Order) and **Production Artwork Set Item** (new custom PrintHub DocType as a child table of Production Artwork Set, carrying immutable membership rows and explicitly not the approval authority). Recorded **File as a native Frappe storage primitive only, never the approval authority**. Recorded that **ERPNext core remains immutable** — no ERPNext DocType is modified to support Artwork. All new technical DocType names are recorded as **proposed pending governed naming treatment**; the Naming Registry is not modified and AR-003 is neither resolved nor modified. Status remains Draft; no Architecture Review Register item was altered; no other entity's implementation ownership changed; no Job Card Tier A document, ADR or standards document was modified; no implementation was authorized. |

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
