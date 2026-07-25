# Business Entity Inventory

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-25

---

# Purpose

Catalog every persistent business concept PrintHub manages, independent of ERPNext, DocTypes, or database design. This document is purely business-conceptual: it identifies *what* the business tracks, not *how* it will be stored or implemented. It is the primary input to the future ERPNext DocType Mapping document, which will assign each entity below exactly one implementation owner (Native ERPNext DocType, Extended ERPNext DocType, Custom `printos_core` DocType, or External Plugin Model) — that assignment is explicitly **not** performed here.

---

# Scope

Covers every business entity across Core ERP, Print Domain, Configuration Studio, MachineIQ, Marketplace, and AI. Excludes processes, workflows, screens, reports, dashboards, permissions, roles, API endpoints, events, commands, services, repositories, DTOs, database tables, and configuration values — per the explicit exclusion list in this task's instructions. Does not perform ERPNext mapping, DocType design, or database schema design (see the future ERPNext DocType Mapping and Database category documents for those).

---

# Relationship to Prior Documents

This inventory is derived from, and does not reinterpret, [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md), [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md), [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md), [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md), [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md), and [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md). Where an entity's name or scope is affected by an open Architecture Review item, this is noted in that entity's Architecture Notes field and left unresolved.

---

# 1. Core ERP Entities

#### Company
- **Business Description:** The legal business entity operating PrintOS.
- **Business Purpose:** Anchors all organizational, financial, and (per [ADR-006](../decisions/ADR-006-MultiTenant-Strategy.md)) tenant-scoping structure.
- **Business Owner:** Administration
- **Category:** Master Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Company → Branch → Department → Employee; Company → Warehouse; Company → Tax Template, Currency.
- **Architecture Notes:** Company is the Approved tenant-scope anchor pending full multi-tenant ratification — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-002.

#### Branch
- **Business Description:** A physical operating location of the Company.
- **Business Purpose:** Represents multi-location operations.
- **Business Owner:** Administration
- **Category:** Master Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Branch → Department; Branch → Warehouse.

#### Department
- **Business Description:** An organizational unit within a Branch.
- **Business Purpose:** Structures Employee organization.
- **Business Owner:** HR
- **Category:** Master Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Department → Employee.

#### Employee
- **Business Description:** A person working within the business.
- **Business Purpose:** Represents staff, including those assignable to production operations.
- **Business Owner:** HR
- **Category:** Master Data
- **Lifecycle:** Active → On Leave → Terminated
- **Relationships:** Employee → Job Card (as Operator, per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)).

#### Customer
- **Business Description:** A party that purchases print/production work.
- **Business Purpose:** Represents the party requesting and paying for work.
- **Business Owner:** CRM / Sales
- **Category:** Master Data
- **Lifecycle:** Prospect → Active → Inactive
- **Relationships:** Customer → Lead/Enquiry → Quotation → Sales Order → Invoice.
- **Architecture Notes:** "Customer" vs. "Client"/"Party" is Pending ADR ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) §27 item 6) — not resolved here.

#### Supplier
- **Business Description:** A party that supplies materials or services to the business.
- **Business Purpose:** Represents procurement counterparties.
- **Business Owner:** Procurement
- **Category:** Master Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Supplier → Purchase Order.
- **Architecture Notes:** "Supplier" vs. "Vendor" is Pending ADR (§27 item 13).

#### Product Template
- **Business Description:** A sellable product definition (e.g., "Business Card," "Banner").
- **Business Purpose:** Anchors Estimation and Sales around a consistent product concept.
- **Business Owner:** Sales / Estimation
- **Category:** Master Data
- **Lifecycle:** Active → Discontinued
- **Relationships:** Product Template → Product Category; Product Template → Job Types, Finishing Types, Paper Sizes; Product Template → Price List.
- **Architecture Notes:** Relationship to ERPNext "Item" is Pending ADR (§27 item 10) — not resolved here; not decided here either.

#### Product Category
- **Business Description:** Groups related Product Templates for organization and reporting.
- **Business Purpose:** Supports catalog structure.
- **Business Owner:** Sales / Estimation
- **Category:** Reference Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Product Category → Product Template.

#### Material
- **Business Description:** A raw input consumed in production.
- **Business Purpose:** Tracks consumable inputs for cost and availability.
- **Business Owner:** Inventory
- **Category:** Master Data
- **Lifecycle:** Active → Discontinued
- **Relationships:** Material → Substrate; Material → Units of Measure.
- **Architecture Notes:** Item vs. Material mapping is Pending ADR — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-006.

#### Warehouse
- **Business Description:** A physical location where Materials or finished goods are stored.
- **Business Purpose:** Enables stock location tracking.
- **Business Owner:** Warehouse
- **Category:** Master Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Warehouse → Branch; Warehouse → Material stock.

#### Price List
- **Business Description:** A defined set of prices applicable to a customer segment or channel.
- **Business Purpose:** Determines applicable pricing.
- **Business Owner:** Sales / Estimation
- **Category:** Master Data
- **Lifecycle:** Active → Expired
- **Relationships:** Price List → Customer; Price List → Product Template.

#### Payment Terms
- **Business Description:** Agreed timing/conditions for customer or supplier payment.
- **Business Purpose:** Governs billing conditions.
- **Business Owner:** Accounts
- **Category:** Reference Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Payment Terms → Customer, Supplier.

#### Tax Template
- **Business Description:** A defined tax computation rule set.
- **Business Purpose:** Ensures correct tax application on transactions.
- **Business Owner:** Accounts / GST
- **Category:** Reference Data
- **Lifecycle:** Active → Inactive
- **Relationships:** Tax Template → Invoice, Purchase Order.

#### GST Configuration
- **Business Description:** Statutory tax configuration applicable to the business.
- **Business Purpose:** Ensures GST compliance.
- **Business Owner:** GST
- **Category:** Reference Data
- **Lifecycle:** Active → Superseded
- **Relationships:** GST Configuration → Tax Template.

#### Currency
- **Business Description:** A unit of monetary value used in transactions.
- **Business Purpose:** Supports financial transaction recording.
- **Business Owner:** Administration
- **Category:** Reference Data
- **Lifecycle:** Active
- **Relationships:** Currency → Invoice, Payment, Price List.

#### Units of Measure
- **Business Description:** Standard measurement units used across materials and products.
- **Business Purpose:** Standardizes quantity expression.
- **Business Owner:** Inventory / Estimation
- **Category:** Reference Data
- **Lifecycle:** Active
- **Relationships:** Units of Measure → Material.

#### Lead / Enquiry
- **Business Description:** An unqualified or in-progress request for business, prior to becoming a confirmed order.
- **Business Purpose:** Captures prospective business before qualification.
- **Business Owner:** CRM
- **Category:** Transactional
- **Lifecycle:** Captured → Qualified → Converted / Closed
- **Relationships:** Lead/Enquiry → Customer; Lead/Enquiry → Quotation.
- **Architecture Notes:** Enquiry-vs-Opportunity mapping is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-011.

#### Sales Order
- **Business Description:** A confirmed commercial commitment to produce and deliver work for a Customer.
- **Business Purpose:** Represents the confirmed commitment driving production.
- **Business Owner:** Sales
- **Category:** Transactional
- **Lifecycle:** Draft → Confirmed → Fulfilled → Cancelled
- **Relationships:** Quotation → Sales Order → Job Card; Sales Order → Dispatch Record → Invoice.

#### Purchase Order
- **Business Description:** A commitment to acquire materials or services from a Supplier.
- **Business Purpose:** Formalizes procurement commitments.
- **Business Owner:** Procurement
- **Category:** Transactional
- **Lifecycle:** Draft → Approved → Received → Closed / Cancelled
- **Relationships:** Purchase Order → Supplier; Purchase Order → Material.

#### Invoice
- **Business Description:** The billing record for delivered work.
- **Business Purpose:** Represents the financial settlement obligation of delivered work.
- **Business Owner:** Accounts
- **Category:** Transactional
- **Lifecycle:** Draft → Issued → Paid / Overdue → Cancelled
- **Relationships:** Dispatch Record → Invoice → Payment.

#### Payment
- **Business Description:** A record of funds received against an Invoice.
- **Business Purpose:** Represents financial settlement.
- **Business Owner:** Accounts
- **Category:** Transactional
- **Lifecycle:** Recorded → Reconciled
- **Relationships:** Payment → Invoice.

#### Journal Entry
- **Business Description:** A manual accounting adjustment record.
- **Business Purpose:** Supports non-transactional financial corrections.
- **Business Owner:** Accounts
- **Category:** Transactional
- **Lifecycle:** Draft → Posted → Cancelled
- **Relationships:** Journal Entry → Company (Accounts).

---

# 2. Print Domain Entities

#### Substrate
- **Business Description:** The specific physical material printed or fabricated upon.
- **Business Purpose:** Describes production input at print-specific fidelity.
- **Business Owner:** Inventory / Production
- **Category:** Master Data
- **Lifecycle:** Active → Discontinued
- **Relationships:** Substrate → Material; Substrate → Media Profiles.

#### Machine
- **Business Description:** A production asset (press, cutter, laminator, etc.) capable of executing job operations.
- **Business Purpose:** Represents production capacity and capability constraints.
- **Business Owner:** Production
- **Category:** Master Data
- **Lifecycle:** Active → Under Maintenance → Retired
- **Relationships:** Machine → Machine Profile; Machine → Job Card (assignment); Machine → Machine Event (MachineIQ, Section 4).
- **Architecture Notes:** Machine's ERPNext base is Pending Architecture Review — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-004; not resolved here.

#### Machine Profile
- **Business Description:** The defined capabilities and constraints of a specific Machine.
- **Business Purpose:** Determines which Job Types a Machine can perform.
- **Business Owner:** Production
- **Category:** Master Data
- **Lifecycle:** Active → Superseded
- **Relationships:** Machine Profile → Machine; Machine Profile → Job Types.
- **Architecture Notes:** See AR-004.

#### Job Types
- **Business Description:** Categories of production work (e.g., offset print, digital print, signage fabrication).
- **Business Purpose:** Classifies work for Estimation and Machine capability matching.
- **Business Owner:** Production / Estimation
- **Category:** Reference Data
- **Lifecycle:** Active → Retired
- **Relationships:** Job Types → Product Template; Job Types → Machine Profile.

#### Finishing Types
- **Business Description:** Post-production processes (e.g., lamination, cutting, binding).
- **Business Purpose:** Describes finishing requirements for Estimation and Production.
- **Business Owner:** Production / Estimation
- **Category:** Reference Data
- **Lifecycle:** Active → Retired
- **Relationships:** Finishing Types → Product Template; Finishing Types → Job Card.

#### Paper Sizes
- **Business Description:** Standard or custom sheet/format sizes.
- **Business Purpose:** Describes physical output dimensions.
- **Business Owner:** Estimation / Production
- **Category:** Reference Data
- **Lifecycle:** Active
- **Relationships:** Paper Sizes → Product Template.

#### Media Profiles
- **Business Description:** Characteristics of a printable media (e.g., vinyl, canvas, board).
- **Business Purpose:** Describes Substrate properties relevant to production.
- **Business Owner:** Estimation / Production
- **Category:** Reference Data
- **Lifecycle:** Active → Retired
- **Relationships:** Media Profiles → Substrate.

#### Quotation
- **Business Description:** A priced proposal for a defined scope of print/production work.
- **Business Purpose:** Captures priced scope prior to customer commitment.
- **Business Owner:** Estimation
- **Category:** Transactional
- **Lifecycle:** Draft → Sent → Approved → Converted / Expired / Revised
- **Relationships:** Lead/Enquiry → Quotation → Quotation Line; Quotation → Sales Order.
- **Architecture Notes:** Canonical per [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md). Document strategy (native vs. custom) is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-005 — not resolved here.

#### Quotation Line
- **Business Description:** A single priced line item within a Quotation, capturing scope, substrate, finishing, and machine-time cost detail.
- **Business Purpose:** Provides the itemized cost breakdown underlying a Quotation's total price.
- **Business Owner:** Estimation
- **Category:** Transactional (child of Quotation)
- **Lifecycle:** Draft → Finalized
- **Relationships:** Quotation → Quotation Line → Product Template, Material, Machine Profile.
- **Architecture Notes:** Exists only as part of Quotation, per this document's child-entity rule.

#### Cost Estimate
- **Business Description:** An internal, pre-Quotation costing artifact distinct from the customer-facing Quotation.
- **Business Purpose:** Supports internal cost calculation before a priced proposal is finalized.
- **Business Owner:** Estimation
- **Category:** Transactional
- **Lifecycle:** Draft → Finalized → Superseded
- **Relationships:** Cost Estimate → Quotation.
- **Architecture Notes:** Distinct internal artifact per [ADR-013-Quotation-Terminology](../decisions/ADR-013-Quotation-Terminology.md) — "Estimate" survives only in this internal sense, not as a Quotation synonym.

#### Artwork
- **Business Description:** The design/creative asset submitted or approved for production.
- **Business Purpose:** Represents the approved creative/design basis for production.
- **Business Owner:** Artwork
- **Category:** Transactional
- **Lifecycle:** Submitted → In Review → Approved → Superseded
- **Relationships:** Sales Order → Artwork → Artwork Revision, Proof; Artwork → Job Card (gate).

#### Artwork Revision
- **Business Description:** A tracked change to Artwork prior to Approval.
- **Business Purpose:** Preserves revision history during proofing cycles.
- **Business Owner:** Artwork
- **Category:** Transactional (child of Artwork)
- **Lifecycle:** Draft → Submitted → Superseded
- **Relationships:** Artwork → Artwork Revision.

#### Proof
- **Business Description:** A representation of artwork provided to the customer for approval before production.
- **Business Purpose:** Supports customer sign-off prior to production.
- **Business Owner:** Artwork
- **Category:** Transactional
- **Lifecycle:** Sent → Approved / Rejected
- **Relationships:** Artwork Revision → Proof → Approval Record.

#### Approval Record
- **Business Description:** The recorded outcome of a customer or internal approval decision (e.g., Artwork approval, discount approval).
- **Business Purpose:** Provides traceable evidence that a required approval gate was satisfied.
- **Business Owner:** Artwork (primary); also referenced by Estimation, Production
- **Category:** Transactional
- **Lifecycle:** Pending → Approved / Rejected
- **Relationships:** Proof → Approval Record; Quotation → Approval Record (discount/internal approvals).
- **Architecture Notes:** Corresponds to the Approval Designer's business-level output ([../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)); "Approval Management" as a standalone module name is not adopted — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-003.

#### Job Card
- **Business Description:** The production instruction and tracking record for a unit of print/production work.
- **Business Purpose:** Tracks execution of production work against a Sales Order.
- **Business Owner:** Production
- **Category:** Transactional
- **Lifecycle:** Scheduled → In Progress → Quality Check → Complete; Rework (from Quality Check)
- **Relationships:** Sales Order → Job Card; Approved Artwork → Job Card; Job Card → Machine (assignment); Job Card → Material (allocation); Job Card → Quality Check Record.
- **Architecture Notes:** Canonical per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md); "Production Order," "Job Ticket," "Work Order" are Rejected/Deprecated synonyms. Must not be confused with ERPNext's native Manufacturing "Job Card" — see [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Manufacturing).

#### Quality Check Record
- **Business Description:** The recorded pass/fail determination and disposition (rework/scrap) for a Job Card's production output.
- **Business Purpose:** Verifies produced work meets required standards before dispatch.
- **Business Owner:** Production
- **Category:** Transactional (child of Job Card)
- **Lifecycle:** Pending → Pass / Fail → Rework / Scrap
- **Relationships:** Job Card → Quality Check Record.
- **Architecture Notes:** Standalone "Quality" module status is Pending ADR — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-009; currently modeled as a Job Card child entity per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md).

#### Dispatch Record
- **Business Description:** The act and record of delivering finished goods to a Customer.
- **Business Purpose:** Represents fulfillment of the Sales Order to the Customer.
- **Business Owner:** Dispatch
- **Category:** Transactional
- **Lifecycle:** Scheduled → Delivered → Confirmed
- **Relationships:** Job Card → Dispatch Record → Invoice.
- **Architecture Notes:** Dispatch vs. Delivery terminology is Pending ADR — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-008.

#### Delivery Method
- **Business Description:** An available method of delivering finished goods.
- **Business Purpose:** Describes fulfillment options.
- **Business Owner:** Dispatch
- **Category:** Reference Data
- **Lifecycle:** Active → Retired
- **Relationships:** Delivery Method → Dispatch Record.

#### "Print Specification" (Not Yet Approved — see Architecture Notes)
- **Business Description:** A candidate aggregate concept representing a print job's full technical specification (size, stock, finish combination).
- **Business Purpose:** Requested as a unifying specification concept for Estimation and Production input.
- **Business Owner:** Not yet assigned
- **Category:** Master Data (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would combine Product Template, Job Types, Finishing Types, Paper Sizes if adopted.
- **Architecture Notes:** Not an Approved Naming Registry term. Per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Print Specification Modeling"), the underlying need already appears covered by the combination of already-Approved master data entities above. Tracked under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-003 — not resolved here. Listed for completeness only, per this task's explicit request.

#### "Production Stage" (Not a Distinct Entity — see Architecture Notes)
- **Business Description:** Requested as a candidate entity representing a Job Card's current position in the production process.
- **Business Purpose:** Would track granular in-progress state.
- **Business Owner:** Not yet assigned
- **Category:** Not applicable
- **Lifecycle:** Not applicable
- **Relationships:** Would relate to Job Card.
- **Architecture Notes:** This appears to be fully represented by Job Card's own Lifecycle/status attribute (Scheduled → In Progress → Quality Check → Complete, per [../blueprint/10_Business_Workflows.md](../blueprint/10_Business_Workflows.md)) rather than requiring independent business identity. Listed for completeness per this task's explicit request; not recommended as a separate entity, though that recommendation is not a resolution of any Architecture Review item.

#### "Machine Queue" (Not a Distinct Entity — see Architecture Notes)
- **Business Description:** Requested as a candidate entity representing the set of Job Cards awaiting a given Machine.
- **Business Purpose:** Would represent scheduling order.
- **Business Owner:** Not yet assigned
- **Category:** Not applicable
- **Lifecycle:** Not applicable
- **Relationships:** Would relate to Machine, Job Card.
- **Architecture Notes:** This appears to be a derived view over Job Card-to-Machine assignments (an operational query, excluded per this document's "not a business entity" rule for Views/Screens) rather than independent persistent state. Listed for completeness per this task's explicit request.

#### Finishing Operation (Future — see Architecture Notes)
- **Business Description:** A candidate entity representing a discrete finishing process step (e.g., a specific lamination pass) distinct from overall Job Card status.
- **Business Purpose:** Would enable finishing-station-level process tracking.
- **Business Owner:** Not yet assigned
- **Category:** Operational (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Job Card, Finishing Types.
- **Architecture Notes:** Per [ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) ("Finishing Process Tracking"), this is explicitly Future-classified and not currently scoped in any Approved Blueprint document.

#### Production Schedule (Future — see Architecture Notes)
- **Business Description:** A candidate entity representing the persistent output of Production Planning — the assignment of Job Cards to time slots/capacity.
- **Business Purpose:** Would provide a queryable schedule artifact distinct from individual Job Card records.
- **Business Owner:** Production Planning
- **Category:** Operational (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Job Card, Machine.
- **Architecture Notes:** Production Planning itself is a process, excluded per this document's rules; "Production Schedule" as a persistent artifact of that process is not yet a distinct entity in any Approved Blueprint document. Listed for completeness per this task's explicit request; not resolved.

---

# 3. Configuration Studio Entities

#### Module Definition
- **Business Description:** The registry record of an installable/enableable PrintOS module and its dependency declarations.
- **Business Purpose:** Enables administrators to enable/disable modules per installation.
- **Business Owner:** Administration (Module Manager)
- **Category:** Configuration
- **Lifecycle:** Enabled → Disabled
- **Relationships:** Module Definition → Module Definition (dependency).

#### Workflow Definition
- **Business Description:** A configured state machine (states and transitions) for a PrintOS transactional document.
- **Business Purpose:** Lets administrators define document lifecycle without code changes.
- **Business Owner:** Administration (Workflow Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated
- **Relationships:** Workflow Definition → Sales Order, Quotation, Job Card (target documents).

#### Approval Definition
- **Business Description:** A configured multi-step, condition-based approval chain.
- **Business Purpose:** Governs who must approve what, in what order.
- **Business Owner:** Administration (Approval Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated
- **Relationships:** Approval Definition → Approval Record (produces).

#### Form Definition
- **Business Description:** Configured field visibility, layout, and conditional behavior for a document.
- **Business Purpose:** Controls presentation without code changes.
- **Business Owner:** Administration (Form Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated

#### Dashboard Definition
- **Business Description:** A configured dashboard composed of widgets and data sources.
- **Business Purpose:** Surfaces PrintOS data to different roles.
- **Business Owner:** Administration (Dashboard Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated

#### Report Definition
- **Business Description:** A configured report (standard/query/script) over PrintOS data.
- **Business Purpose:** Provides consistent, permissioned reporting.
- **Business Owner:** Administration (Report Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated

#### Notification Template
- **Business Description:** A reusable, configured message template with placeholders, tied to a Notification trigger.
- **Business Purpose:** Standardizes alert/notification content.
- **Business Owner:** Administration (Notification Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Deprecated

#### Integration Definition
- **Business Description:** A configured connection to an external system: endpoint, credential reference, mapping rules.
- **Business Purpose:** Enables PrintOS to connect to external services without code per instance.
- **Business Owner:** Administration (Integration Designer)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Disabled
- **Relationships:** Integration Definition → Payment Gateway, WhatsApp Channel, MachineIQ Service, Marketplace Service (external targets).

#### Feature Flag
- **Business Description:** A named toggle controlling whether a PrintOS feature is active.
- **Business Purpose:** Enables safe, incremental rollout and per-installation packaging.
- **Business Owner:** Administration (Feature Flags)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Retired

#### Automation Rule
- **Business Description:** A configured trigger-condition-action automation.
- **Business Purpose:** Automates operational shortcuts without embedding logic ad hoc.
- **Business Owner:** Administration (Automation Rules)
- **Category:** Configuration
- **Lifecycle:** Draft → Active → Disabled

#### Configuration Template
- **Business Description:** A named, versioned bundle of configuration records (e.g., a "Standard Print Shop" workflow + dashboard set).
- **Business Purpose:** Accelerates onboarding with proven defaults.
- **Business Owner:** Administration (Template Library)
- **Category:** Configuration
- **Lifecycle:** Draft → Published → Superseded
- **Relationships:** Configuration Template → Workflow Definition, Approval Definition, Dashboard Definition (bundled items).

#### Tenant Override
- **Business Description:** A per-installation override of a Configuration Template default value.
- **Business Purpose:** Enables installation-specific customization without forking configuration.
- **Business Owner:** Administration (Tenant Customization)
- **Category:** Configuration
- **Lifecycle:** Active → Removed
- **Architecture Notes:** "Tenant" terminology and scope are Pending ADR — see [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-002; not resolved here.

---

# 4. MachineIQ Entities

#### Machine Event
- **Business Description:** A discrete, time-stamped occurrence reported by or about a Machine.
- **Business Purpose:** Provides the raw signal basis for MachineIQ analysis.
- **Business Owner:** MachineIQ (Future)
- **Category:** Intelligence
- **Lifecycle:** Recorded
- **Relationships:** Machine → Machine Event.
- **Architecture Notes:** Approved term ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) §6/§23); full scope deferred per [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).

#### Sensor
- **Business Description:** A physical device reporting a measurable Machine condition.
- **Business Purpose:** Source of Telemetry Reading data.
- **Business Owner:** MachineIQ (Future)
- **Category:** Intelligence
- **Lifecycle:** Not yet defined
- **Relationships:** Sensor → Machine; Sensor → Telemetry Reading.
- **Architecture Notes:** Proposed term ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) §36); not yet scoped.

#### Telemetry Reading
- **Business Description:** Streamed measurement data reported from a Machine or Sensor.
- **Business Purpose:** Provides raw operational data for MachineIQ.
- **Business Owner:** MachineIQ (Future)
- **Category:** Intelligence
- **Lifecycle:** Recorded
- **Relationships:** Sensor → Telemetry Reading.
- **Architecture Notes:** Proposed term (§36); not yet scoped.

#### Counter Reading
- **Business Description:** A running tally of units produced by a Machine (Production Counter).
- **Business Purpose:** Supports throughput and OEE calculation.
- **Business Owner:** MachineIQ (Future)
- **Category:** Intelligence
- **Lifecycle:** Recorded
- **Relationships:** Machine → Counter Reading.
- **Architecture Notes:** Proposed term (§36, "Production Counter"); not yet scoped.

#### Maintenance Alert
- **Business Description:** A MachineIQ-generated notification of a condition requiring maintenance attention.
- **Business Purpose:** Supports predictive/preventive maintenance.
- **Business Owner:** MachineIQ (Future)
- **Category:** Intelligence
- **Lifecycle:** Raised → Acknowledged → Resolved
- **Relationships:** Machine → Maintenance Alert.
- **Architecture Notes:** Corresponds to Proposed term "Alert" (§36); not yet scoped.

#### Machine Status
- **Business Description:** The current operating state of a Machine (e.g., running, idle, downtime, maintenance).
- **Business Purpose:** Provides real-time Machine availability visibility.
- **Business Owner:** MachineIQ (Future) / Production
- **Category:** Intelligence
- **Lifecycle:** Running → Idle → Downtime → Maintenance
- **Relationships:** Machine → Machine Status.
- **Architecture Notes:** Related Proposed terms: Downtime, Idle Time, Maintenance Window (§36); not yet scoped.

---

# 5. Marketplace Entities

This section contains two distinct groups that must not be conflated — flagged explicitly rather than merged, per this document's rule against inventing or resolving terminology.

**Group A — Approved Marketplace concepts** (the G1 public-buyer-facing platform per [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md) and [../standards/Naming_Registry.md](../standards/Naming_Registry.md) §24/§37):

#### Marketplace Listing
- **Business Description:** A print shop's advertised capability/catalog entry visible to public buyers.
- **Business Purpose:** Enables Marketplace discovery.
- **Business Owner:** Marketplace (Future, Phase 5)
- **Category:** Platform
- **Lifecycle:** Not yet defined
- **Relationships:** Marketplace Listing → Product Template (future).
- **Architecture Notes:** Proposed term (§24); scope deferred to Phase 5 per [ADR-009](../decisions/ADR-009-Marketplace.md).

#### Marketplace Order
- **Business Description:** An order originated by a Public Customer (G1) through the Marketplace.
- **Business Purpose:** Routes public-buyer orders into CRM/Sales.
- **Business Owner:** Marketplace (Future, Phase 5)
- **Category:** Platform
- **Lifecycle:** Not yet defined
- **Relationships:** Marketplace Order → Lead/Enquiry or Sales Order (future).
- **Architecture Notes:** Proposed term (§24); relationship to Sales Order not yet defined (§27 item 14, Pending ADR).

**Group B — Requested "Marketplace" entities describing a distinct, unregistered plugin/extension-marketplace concept** (Package, Template, Extension, Publisher, Package Version, as listed in this task's instructions). These do not correspond to Group A's Approved "Marketplace" business concept and are flagged as a naming collision, not resolved:

#### Marketplace Package
- **Business Description:** A candidate entity representing an installable bundle of PrintOS extensions/plugins.
- **Business Purpose:** Would enable distribution of third-party or PrintHub-authored extensions.
- **Business Owner:** Not yet assigned
- **Category:** Platform (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Marketplace Package Version, Publisher, Extension.
- **Architecture Notes:** **Naming collision flagged.** This concept is not the same as the Approved "Marketplace" (G1 buyer platform, Group A above). It appears to describe a software/plugin marketplace, which is not registered anywhere in Blueprint or the Naming Registry. Not resolved here.

#### Marketplace Template
- **Business Description:** A candidate entity representing a reusable, distributable configuration/industry template offered via a plugin marketplace.
- **Business Purpose:** Would allow templates to be shared/distributed beyond a single installation.
- **Business Owner:** Not yet assigned
- **Category:** Platform (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Configuration Template (Section 3) — relationship not yet defined.
- **Architecture Notes:** **Naming collision flagged** against Configuration Template (Section 3) and against Group A's Marketplace Listing. Not resolved here.

#### Extension
- **Business Description:** A candidate entity representing an installable unit of PrintOS functionality distributed via a plugin marketplace.
- **Business Purpose:** Would formalize the Plugin/Feature Pack concept already discussed in [../architecture/05_Extensibility_Architecture.md](../architecture/05_Extensibility_Architecture.md) Future Considerations.
- **Business Owner:** Not yet assigned
- **Category:** Platform (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Marketplace Package.
- **Architecture Notes:** Not a registered Naming Registry term; overlaps conceptually with "Plugin"/"Feature Pack" already discussed elsewhere but not formally unified with them. Not resolved here.

#### Publisher
- **Business Description:** A candidate entity representing the author/publisher of a Marketplace Package.
- **Business Purpose:** Would attribute and govern third-party extension authorship.
- **Business Owner:** Not yet assigned
- **Category:** Platform (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Marketplace Package.
- **Architecture Notes:** Not a registered Naming Registry term. Not resolved here.

#### Marketplace Package Version
- **Business Description:** A candidate entity representing a specific released version of a Marketplace Package.
- **Business Purpose:** Would support version-controlled extension distribution.
- **Business Owner:** Not yet assigned
- **Category:** Platform (candidate, child of Marketplace Package)
- **Lifecycle:** Not yet defined
- **Relationships:** Marketplace Package → Marketplace Package Version.
- **Architecture Notes:** Not a registered Naming Registry term. Not resolved here.

---

# 6. AI Entities

None of the entities in this section are registered in [../standards/Naming_Registry.md](../standards/Naming_Registry.md) or scoped in any Blueprint document. "AI Assistant" itself is flagged as an unregistered term in [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) and [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-003. This section is listed for completeness per this task's explicit request; none of these entities should be treated as Approved pending that resolution.

#### Conversation
- **Business Description:** A candidate entity representing a session of interaction between a user and the AI Assistant capability.
- **Business Purpose:** Would provide continuity/context across AI interactions.
- **Business Owner:** Not yet assigned
- **Category:** Intelligence (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Would relate to Prompt.
- **Architecture Notes:** Unregistered term; see AR-003.

#### Prompt
- **Business Description:** A candidate entity representing a single user input/instruction to the AI Assistant.
- **Business Purpose:** Would capture the specific request driving an AI Action.
- **Business Owner:** Not yet assigned
- **Category:** Intelligence (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Conversation → Prompt.
- **Architecture Notes:** Unregistered term; see AR-003.

#### Knowledge Source
- **Business Description:** A candidate entity representing a reference source the AI Assistant draws upon.
- **Business Purpose:** Would ground AI responses in PrintHub-specific data.
- **Business Owner:** Not yet assigned
- **Category:** Intelligence (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Not yet defined.
- **Architecture Notes:** Unregistered term; see AR-003.

#### Recommendation
- **Business Description:** A candidate entity representing an AI-generated suggestion (distinct from MachineIQ's "Insight," §23).
- **Business Purpose:** Would surface AI-driven guidance to users.
- **Business Owner:** Not yet assigned
- **Category:** Intelligence (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Not yet defined.
- **Architecture Notes:** Unregistered term; potential overlap with MachineIQ's "Insight" (§23) not yet reconciled. See AR-003.

#### AI Action
- **Business Description:** A candidate entity representing an action the AI Assistant performs or proposes on behalf of a user.
- **Business Purpose:** Would represent AI-initiated or AI-assisted operations.
- **Business Owner:** Not yet assigned
- **Category:** Intelligence (candidate)
- **Lifecycle:** Not yet defined
- **Relationships:** Prompt → AI Action.
- **Architecture Notes:** Unregistered term; see AR-003.

---

# Entity Dependency Matrix

| Entity | Depends On | Parent Entity |
|---|---|---|
| Branch | Company | Company |
| Department | Branch | Branch |
| Employee | Department | Department |
| Warehouse | Branch | Branch |
| Product Category | — | — |
| Product Template | Product Category | Product Category |
| Material | Units of Measure | — |
| Substrate | Material | Material |
| Media Profiles | Substrate | — |
| Machine Profile | Machine, Job Types | Machine |
| Lead / Enquiry | Customer | — |
| Quotation | Lead/Enquiry, Product Template | — |
| Quotation Line | Quotation | Quotation |
| Cost Estimate | Quotation | — |
| Sales Order | Quotation | — |
| Artwork | Sales Order | — |
| Artwork Revision | Artwork | Artwork |
| Proof | Artwork Revision | — |
| Approval Record | Proof, Quotation | — |
| Job Card | Sales Order, Artwork (Approved) | — |
| Quality Check Record | Job Card | Job Card |
| Dispatch Record | Job Card | — |
| Invoice | Dispatch Record | — |
| Payment | Invoice | — |
| Purchase Order | Supplier, Material | — |
| Journal Entry | Company | — |
| Module Definition | — | — |
| Workflow Definition | Module Definition (target document) | — |
| Approval Definition | Module Definition (target document) | — |
| Configuration Template | Workflow Definition, Approval Definition, Dashboard Definition | — |
| Tenant Override | Configuration Template | — |
| Integration Definition | — | — |
| Machine Event | Machine | Machine |
| Sensor | Machine | Machine |
| Telemetry Reading | Sensor | Sensor |
| Counter Reading | Machine | Machine |
| Maintenance Alert | Machine | Machine |
| Machine Status | Machine | Machine |
| Marketplace Listing | Product Template | — |
| Marketplace Order | Lead/Enquiry, Sales Order | — |
| Marketplace Package Version | Marketplace Package | Marketplace Package |
| Extension | Marketplace Package | — |
| Conversation | — | — |
| Prompt | Conversation | Conversation |
| AI Action | Prompt | — |

---

# Master Entity Catalog

Sorted alphabetically. Entities marked *(candidate)* are not yet Approved Blueprint/Naming Registry terms and are included per this task's explicit request only — see each entry's Architecture Notes above.

| Entity | Module | Category | Lifecycle |
|---|---|---|---|
| AI Action *(candidate)* | AI | Intelligence | Not yet defined |
| Approval Definition | Administration (Approval Designer) | Configuration | Draft → Active → Deprecated |
| Approval Record | Artwork | Transactional | Pending → Approved / Rejected |
| Artwork | Artwork | Transactional | Submitted → In Review → Approved → Superseded |
| Artwork Revision | Artwork | Transactional | Draft → Submitted → Superseded |
| Automation Rule | Administration (Automation Rules) | Configuration | Draft → Active → Disabled |
| Branch | Administration | Master Data | Active → Inactive |
| Company | Administration | Master Data | Active → Inactive |
| Configuration Template | Administration (Template Library) | Configuration | Draft → Published → Superseded |
| Conversation *(candidate)* | AI | Intelligence | Not yet defined |
| Cost Estimate | Estimation | Transactional | Draft → Finalized → Superseded |
| Counter Reading *(candidate)* | MachineIQ | Intelligence | Recorded |
| Currency | Administration | Reference Data | Active |
| Customer | CRM / Sales | Master Data | Prospect → Active → Inactive |
| Dashboard Definition | Administration (Dashboard Designer) | Configuration | Draft → Active → Deprecated |
| Delivery Method | Dispatch | Reference Data | Active → Retired |
| Department | HR | Master Data | Active → Inactive |
| Dispatch Record | Dispatch | Transactional | Scheduled → Delivered → Confirmed |
| Employee | HR | Master Data | Active → On Leave → Terminated |
| Extension *(candidate)* | Marketplace | Platform | Not yet defined |
| Feature Flag | Administration (Feature Flags) | Configuration | Draft → Active → Retired |
| Finishing Operation *(candidate, Future)* | Production | Operational | Not yet defined |
| Finishing Types | Production / Estimation | Reference Data | Active → Retired |
| Form Definition | Administration (Form Designer) | Configuration | Draft → Active → Deprecated |
| GST Configuration | GST | Reference Data | Active → Superseded |
| Integration Definition | Administration (Integration Designer) | Configuration | Draft → Active → Disabled |
| Invoice | Accounts | Transactional | Draft → Issued → Paid / Overdue → Cancelled |
| Job Card | Production | Transactional | Scheduled → In Progress → Quality Check → Complete |
| Job Types | Production / Estimation | Reference Data | Active → Retired |
| Journal Entry | Accounts | Transactional | Draft → Posted → Cancelled |
| Knowledge Source *(candidate)* | AI | Intelligence | Not yet defined |
| Lead / Enquiry | CRM | Transactional | Captured → Qualified → Converted / Closed |
| Machine | Production | Master Data | Active → Under Maintenance → Retired |
| Machine Event | MachineIQ | Intelligence | Recorded |
| Machine Profile | Production | Master Data | Active → Superseded |
| Machine Queue *(candidate)* | Production | Not applicable | Not applicable |
| Machine Status *(candidate)* | MachineIQ / Production | Intelligence | Running → Idle → Downtime → Maintenance |
| Maintenance Alert *(candidate)* | MachineIQ | Intelligence | Raised → Acknowledged → Resolved |
| Marketplace Listing *(Proposed)* | Marketplace | Platform | Not yet defined |
| Marketplace Order *(Proposed)* | Marketplace | Platform | Not yet defined |
| Marketplace Package *(candidate)* | Marketplace | Platform | Not yet defined |
| Marketplace Package Version *(candidate)* | Marketplace | Platform | Not yet defined |
| Marketplace Template *(candidate)* | Marketplace | Platform | Not yet defined |
| Material | Inventory | Master Data | Active → Discontinued |
| Media Profiles | Estimation / Production | Reference Data | Active → Retired |
| Module Definition | Administration (Module Manager) | Configuration | Enabled → Disabled |
| Notification Template | Administration (Notification Designer) | Configuration | Draft → Active → Deprecated |
| Paper Sizes | Estimation / Production | Reference Data | Active |
| Payment | Accounts | Transactional | Recorded → Reconciled |
| Payment Terms | Accounts | Reference Data | Active → Inactive |
| Price List | Sales / Estimation | Master Data | Active → Expired |
| Print Specification *(candidate)* | Not yet assigned | Master Data | Not yet defined |
| Prompt *(candidate)* | AI | Intelligence | Not yet defined |
| Product Category | Sales / Estimation | Reference Data | Active → Inactive |
| Product Template | Sales / Estimation | Master Data | Active → Discontinued |
| Production Schedule *(candidate, Future)* | Production Planning | Operational | Not yet defined |
| Production Stage *(candidate)* | Production | Not applicable | Not applicable |
| Proof | Artwork | Transactional | Sent → Approved / Rejected |
| Publisher *(candidate)* | Marketplace | Platform | Not yet defined |
| Purchase Order | Procurement | Transactional | Draft → Approved → Received → Closed / Cancelled |
| Quality Check Record | Production | Transactional | Pending → Pass / Fail → Rework / Scrap |
| Quotation | Estimation | Transactional | Draft → Sent → Approved → Converted / Expired / Revised |
| Quotation Line | Estimation | Transactional | Draft → Finalized |
| Recommendation *(candidate)* | AI | Intelligence | Not yet defined |
| Report Definition | Administration (Report Designer) | Configuration | Draft → Active → Deprecated |
| Sales Order | Sales | Transactional | Draft → Confirmed → Fulfilled → Cancelled |
| Sensor *(candidate)* | MachineIQ | Intelligence | Not yet defined |
| Substrate | Inventory / Production | Master Data | Active → Discontinued |
| Supplier | Procurement | Master Data | Active → Inactive |
| Tax Template | Accounts / GST | Reference Data | Active → Inactive |
| Telemetry Reading *(candidate)* | MachineIQ | Intelligence | Recorded |
| Tenant Override | Administration (Tenant Customization) | Configuration | Active → Removed |
| Units of Measure | Inventory / Estimation | Reference Data | Active |
| Warehouse | Warehouse | Master Data | Active → Inactive |
| Workflow Definition | Administration (Workflow Designer) | Configuration | Draft → Active → Deprecated |

---

# Validation

- ✓ **Every Blueprint module has entities.** CRM (Lead/Enquiry, Customer), Sales (Sales Order, Price List), Estimation (Quotation, Quotation Line, Cost Estimate), Artwork (Artwork, Artwork Revision, Proof, Approval Record), Production Planning (Production Schedule *[candidate]*), Job Cards (Job Card, Quality Check Record), Machine Scheduling (Machine, Machine Profile), Inventory (Material, Substrate, Units of Measure), Purchasing (Purchase Order, Supplier), Warehouse (Warehouse), Dispatch (Dispatch Record, Delivery Method), Accounts (Invoice, Payment, Journal Entry, Tax Template), GST (GST Configuration), HR (Employee, Department), Administration (Company, Branch, all Configuration Studio entities), Reports/Analytics (none — Reports/Dashboards are explicitly excluded as non-entities per this document's scope), MachineIQ (Section 4 entities), Marketplace (Section 5 entities).
- ✓ **Every ERPNext Gap has supporting entities.** All 13 Customize-classified gaps in [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) map to entities above: Print Estimation & Costing Engine → Quotation/Quotation Line/Cost Estimate; Print Specification Modeling → "Print Specification" *(candidate)* plus its constituent Approved entities; Artwork & Proof Lifecycle → Artwork/Artwork Revision/Proof; Business Approval Orchestration → Approval Record/Approval Definition; Production Planning & Orchestration → Production Schedule *(candidate)*; Job Card Execution & Lifecycle → Job Card; Machine Scheduling & Capability Modeling → Machine/Machine Profile; Quality Check Processing → Quality Check Record; Configuration Studio Governance Layer → Section 3 entities; Feature Flag Engine → Feature Flag; Tenant Customization/Resolution Engine → Tenant Override; Automation Rule Condition Evaluator → Automation Rule; Module Dependency Management → Module Definition.
- ✓ **No duplicate entity names exist.** Marketplace Package/Template were explicitly renamed with a "Marketplace" prefix specifically to avoid collision with Configuration Template; no other name collisions were found.
- ✓ **No business entity appears in multiple modules without justification.** Machine appears once (Production, Section 2) and is referenced — not duplicated — by MachineIQ entities (Section 4). Quotation appears once (Estimation, Section 2); Sales Order appears once (Sales, Section 1). Where an entity has joint ownership (e.g., Substrate: Inventory/Production; Material: Inventory), this reflects genuine shared ownership already stated in [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md), not an unjustified duplication.
- ✓ **Every entity has a business owner.** All entities in Sections 1–4 have a named Business Owner. Entities explicitly marked *(candidate)* or "Not yet assigned" in Sections 2, 5, and 6 intentionally lack a confirmed owner because they are not yet Approved concepts — this is flagged, not omitted.
- ✓ **Architecture Review items are referenced where applicable.** AR-002 (Company), AR-003 (Approval Record, "Print Specification," AI entities), AR-004 (Machine, Machine Profile), AR-005 (Quotation), AR-006 (Material), AR-008 (Dispatch Record), AR-009 (Quality Check Record), AR-011 (Lead/Enquiry) are each cited in their corresponding entity's Architecture Notes.

---

# Related Documents

- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)
- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [00_Master_Index.md](00_Master_Index.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-25 | Initial | Initial Business Entity Inventory. Cataloged 75 entities across Core ERP (22), Print Domain (23), Configuration Studio (12), MachineIQ (6), Marketplace (7, including a flagged naming collision between Approved Marketplace concepts and requested plugin-marketplace entities), and AI (5, all unregistered pending AR-003). No ERPNext mapping, DocType design, or Architecture Review resolution performed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] No DocType or database design included
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] No Architecture Review Register item resolved
- [ ] Consistent with ERPNext Fit Analysis and Gap Analysis baseline
- [ ] Reviewed by Project Owner
