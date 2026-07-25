# ERPNext Gap Analysis

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-24

---

## 1. Executive Summary

**Purpose.** This document identifies the business capabilities PrintHub requires that ERPNext does not adequately provide — the residual work `printos_core` and its plugin boundary must actually deliver, after [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) already determined ownership for every capability. Where the Fit Analysis answered "who owns this, ERPNext or PrintHub?", this document answers "given that PrintHub owns it, what specifically is missing and how large is the gap?"

**Scope.** Covers every capability the Fit Analysis classified as Extend (where the extension itself constitutes a real gap beyond trivial configuration), Customize, Plugin, or Future — across Print Domain, Configuration Platform, Intelligence, and Operations. Capabilities the Fit Analysis classified as pure Native reuse (Accounts, HR, Warehouse/stock ledger mechanics, Purchase Order/Supplier, Sales Order/Customer core structure) are intentionally excluded — they are not gaps.

**Relationship to the ERPNext Fit Analysis.** This document treats the Fit Analysis as its baseline and does not re-derive or contradict any classification made there. Where the Fit Analysis already resolved a capability to Native or straightforward Extend, it is not repeated here. Where the Fit Analysis identified a capability as Customize, Plugin, or Future — or as Extend with a substantive gap beneath the extension — this document expands that single-line classification into a full gap record.

**Relationship to the Architecture Review Register.** Several gaps below intersect with unresolved items in [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) (AR-001 through AR-011). Per that register's governing rule, this document does not resolve, recommend an option for, or revisit any AR item — it references the applicable AR ID and continues the gap analysis around it. Any gap whose scope is directly gated by an open AR item is annotated accordingly in its Dependencies field.

---

## 2. Gap Classification Summary

| Classification | Count |
|----------------|------:|
| Extend | 1 |
| Customize | 13 |
| Plugin | 7 |
| Future | 7 |
| **Total Gaps** | **28** |

---

## 3. Detailed Gap Analysis

### PRINT DOMAIN

---

#### Gap Name
Print Estimation & Costing Engine

**Business Purpose**
Produce accurate, industry-specific pricing for requested print/production work — substrate cost, finishing cost, machine time, and quantity-based pricing — as PrintOS's stated Core Domain differentiator.

**Why ERPNext Is Insufficient**
ERPNext's native pricing mechanisms (Price List, Item pricing rules) are designed for generic priced-item selling. They have no concept of substrate, finishing, or machine-time as first-class cost drivers, and no multi-version quoting workflow tailored to print estimation.

**Current ERPNext Capability**
Selling module: Quotation, Price List, Pricing Rule. These provide document structure and generic pricing mechanics only.

**Gap Description**
PrintHub requires a costing calculation capability that combines Material/Substrate cost, Finishing Types, Machine Profile time-cost, and quantity into a priced proposal, versioned across revisions, before a Quotation is finalized.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) (Estimation Core Domain), [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (Estimation module), [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 3 (Selling); Quotation document target is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-005; BOM necessity is under AR-010.

**Implementation Priority**
Critical

---

#### Gap Name
Print Specification Modeling

**Business Purpose**
Capture the technical specification of a printable product — size, stock, finish combination — consistently across Estimation, Production, and Sales.

**Why ERPNext Is Insufficient**
ERPNext has no native concept representing a print job's full technical specification as a single addressable object; it has generic Item attributes only.

**Current ERPNext Capability**
Stock module: Item, Item Variant Attributes. Partial structural fit only, as already noted in the Fit Analysis.

**Gap Description**
PrintHub requires the combination of Product Template, Job Types, Finishing Types, and Paper Sizes (all Approved master data per [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)) to function together as a coherent specification input to Estimation and Production. Note: per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 4, the requested term "Print Specification" itself is not an Approved Naming Registry term; this gap describes the underlying capability need only, tracked under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-003.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md); naming status under AR-003.

**Implementation Priority**
High

---

#### Gap Name
Artwork & Proof Lifecycle

**Business Purpose**
Manage design asset intake, proofing, revision tracking, and customer approval capture before production may begin.

**Why ERPNext Is Insufficient**
ERPNext provides generic file attachment only; it has no concept of Artwork, Proof, or Approval Record as distinct, trackable business objects with a revision history and approval gate.

**Current ERPNext Capability**
Frappe Framework: File (generic attachment mechanism). No Artwork-specific module or DocType exists.

**Gap Description**
PrintHub requires Artwork, Proof, and Approval Record as owned business objects, with revision tracking and a hard gate preventing Job Card production start without Approved Artwork, per the Business Rule already stated in [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md).

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) (Artwork context), [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 4 (Artwork Management).

**Implementation Priority**
Critical

---

#### Gap Name
Business Approval Orchestration (Artwork/Estimation/Discount Gates)

**Business Purpose**
Enforce business-level approval gates — customer artwork approval, internal estimation sign-off, discount authorization — as distinct, traceable events tied to specific business documents.

**Why ERPNext Is Insufficient**
Frappe's native Workflow engine provides generic state-machine approval mechanics but has no built-in concept of print-industry-specific approval chains (e.g., customer-facing Proof approval versus internal discount approval) or condition-based multi-step escalation as PrintHub requires.

**Current ERPNext Capability**
Frappe Framework: Workflow, Workflow State, Workflow Action — a reusable generic mechanism, not a business-specific one.

**Gap Description**
PrintHub requires business-specific approval chain configuration (approver resolution, escalation, condition evaluation) layered on top of the native Workflow engine. Note: per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 4, this corresponds to the already-Approved Approval Designer ([../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)), not a separate "Approval Management" module — the requested term mismatch is tracked under AR-003.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md); naming status under AR-003.

**Implementation Priority**
High

---

#### Gap Name
Production Planning & Orchestration

**Business Purpose**
Sequence and schedule confirmed print work across available capacity, coordinating Approved Artwork, allocated Materials, and Machine availability into a production plan.

**Why ERPNext Is Insufficient**
ERPNext's Manufacturing module models planning around BOM-driven Work Orders, a structurally different execution model than print production, which is driven by a confirmed Sales Order and Approved Artwork rather than a bill-of-materials explosion (established in [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 3, Manufacturing).

**Current ERPNext Capability**
Manufacturing module: Work Order, Production Plan. Structurally mismatched, not directly reusable.

**Gap Description**
PrintHub requires capacity planning, job prioritization, and schedule coordination logic as an Application-layer capability, orchestrating Job Cards and Machine Scheduling without assuming a BOM/Work-Order model.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (Production Planning module), [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md).

**Implementation Priority**
Critical

---

#### Gap Name
Job Card Execution & Lifecycle

**Business Purpose**
Track a unit of print production work — status, material allocation, quality checkpoints — from scheduling through completion, tied directly to its originating Sales Order.

**Why ERPNext Is Insufficient**
ERPNext's native Manufacturing "Job Card" is tied to a Work Order/BOM Operation sequence — a different concept from PrintHub's Approved "Job Card" entity (canonical per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md)). Reusing it would either force an ill-fitting execution model or create a same-name/different-meaning collision, as already established in [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md).

**Current ERPNext Capability**
Manufacturing module: Job Card (native). Name-colliding, not structurally reusable — this finding is carried forward from the Fit Analysis, not re-argued here.

**Gap Description**
PrintHub requires its own Job Card capability: status tracking, material allocation linkage, quality checkpoint recording, and Machine assignment, independent of ERPNext's Manufacturing Job Card.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md), [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 3 (Manufacturing) and Section 4 (Job Card).

**Implementation Priority**
Critical

---

#### Gap Name
Machine Scheduling & Capability Modeling

**Business Purpose**
Assign production work to available machines within their capability constraints, avoiding scheduling conflicts and accounting for downtime.

**Why ERPNext Is Insufficient**
No native ERPNext object models print-machine capability/constraint data (Machine Profile) at the fidelity PrintHub requires; candidate base objects (Asset, Workstation) are both partial fits at best.

**Current ERPNext Capability**
Assets module: Asset. Manufacturing module: Workstation. Both partial, unconfirmed candidates — the choice among them is explicitly open.

**Gap Description**
PrintHub requires Machine and Machine Profile capability modeling and a scheduling/conflict-detection capability. The specific ERPNext base object for Machine (Asset, Workstation, Custom, or Hybrid) is unresolved.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
Machine base-object selection is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) **AR-004** — not resolved here. [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md) (Machine, Machine Profile).

**Implementation Priority**
Critical

---

#### Gap Name
Finishing Process Tracking

**Business Purpose**
Track post-print finishing processes (lamination, cutting, binding, mounting) as discrete production steps, beyond capturing Finishing Type as a static attribute.

**Why ERPNext Is Insufficient**
No native ERPNext concept exists for finishing-station or finishing-step process tracking distinct from generic manufacturing operations.

**Current ERPNext Capability**
Manufacturing module: Operation (generic, BOM-tied). Not directly applicable given the Job Card/Manufacturing mismatch already established.

**Gap Description**
Finishing Types already exist as Approved master data ([../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)) consumed by Estimation and Job Card. Discrete finishing *process* tracking (e.g., a finishing sub-station queue distinct from overall Job Card status) is not currently scoped in any Approved Blueprint document.

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 4 (Finishing) — this gap was explicitly marked "not adopted as a standalone module" there.

**Implementation Priority**
Low

---

#### Gap Name
Quality Check Processing

**Business Purpose**
Verify finished production work meets required standards before dispatch, with pass/fail determination and rework/scrap disposition.

**Why ERPNext Is Insufficient**
ERPNext's generic Quality Inspection (Manufacturing/Stock module) assumes an Item-receipt or Work-Order-completion trigger, not a Job Card status transition.

**Current ERPNext Capability**
Manufacturing/Stock module: Quality Inspection. Partial, unconfirmed fit.

**Gap Description**
PrintHub requires quality checkpoint recording tied to Job Card status transitions. Per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), this is currently scoped as a Job Cards sub-feature, not a standalone module; a standalone "Quality" module remains open.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
Standalone Quality module status is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) **AR-009** — not resolved here.

**Implementation Priority**
Medium

---

#### Gap Name
Dispatch-Specific Data Capture

**Business Purpose**
Capture print-industry-specific dispatch data (delivery method, dispatch documentation) beyond ERPNext's generic delivery record.

**Why ERPNext Is Insufficient**
ERPNext's native Delivery Note is a strong structural fit but lacks print-specific dispatch fields.

**Current ERPNext Capability**
Selling/Stock module: Delivery Note. Strong native fit, per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 4.

**Gap Description**
Custom Fields on Delivery Note are required to capture Dispatch Record-specific data. This is a narrow, well-bounded gap, not a new business object.

**Recommended Classification**
Extend

**Implementation Ownership**
ERPNext Extension

**Dependencies**
Dispatch/Delivery terminology alignment is under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) **AR-008** — not resolved here.

**Implementation Priority**
Medium

---

#### Gap Name
Customer Production Visibility / Tracking

**Business Purpose**
Allow a Customer to view the production status of their order (e.g., "In Production," "Quality Check," "Dispatched").

**Why ERPNext Is Insufficient**
ERPNext has no customer-facing production-status portal tailored to print-industry job stages; its generic Customer Portal exposes standard transactional documents only.

**Current ERPNext Capability**
Portal module: generic Customer Portal (Sales Order/Invoice visibility only).

**Gap Description**
A read-facing projection of Job Card status for Customer consumption is not currently required, since Customers (G1) exist only as ERP customer records in Phase 1, per [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 10 — they are not active platform users until Phase 5 (Marketplace).

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md) (Phase 5, Marketplace, G1 activation).

**Implementation Priority**
Low

---

### CONFIGURATION PLATFORM

---

#### Gap Name
Configuration Studio Governance Layer

**Business Purpose**
Provide versioning, rollback, audit, and dependency management across every designer (Workflow, Approval, Form, Dashboard, Report, Notification, Integration, Module) as a single coherent platform capability, rather than per-designer ad hoc mechanics.

**Why ERPNext Is Insufficient**
Frappe's native fixture export/import and Version DocType provide the underlying primitives, but no native concept aggregates them into a governed, tenant-scoped configuration promotion system with explicit versioning and rollback semantics across multiple designer types at once.

**Current ERPNext Capability**
Frappe Framework: fixtures, Version DocType (native audit trail). Reusable as underlying primitives only.

**Gap Description**
PrintHub requires the aggregate governance layer described in [../configuration/01_Configuration_Architecture.md](../configuration/01_Configuration_Architecture.md) and [../configuration/15_Deployment_Model.md](../configuration/15_Deployment_Model.md) — a single configuration resolution/promotion model spanning all designers, built on native fixtures but not equivalent to them alone.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../configuration/00_Master_Index.md](../configuration/00_Master_Index.md), [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 5.

**Implementation Priority**
Critical

---

#### Gap Name
Feature Flag Engine

**Business Purpose**
Toggle PrintOS functionality per installation, environment, or rollout stage without branching code paths.

**Why ERPNext Is Insufficient**
No native ERPNext concept of a feature flag exists; ERPNext's app-enable/disable mechanism operates at the whole-app level, not the fine-grained feature level PrintHub requires.

**Current ERPNext Capability**
None directly applicable.

**Gap Description**
PrintHub requires a Feature Flag and Feature Flag Override data model, consulted by Application-layer use cases and Interface-layer routes, per [../configuration/12_Feature_Flags.md](../configuration/12_Feature_Flags.md).

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../configuration/12_Feature_Flags.md](../configuration/12_Feature_Flags.md).

**Implementation Priority**
High

---

#### Gap Name
Tenant Customization / Resolution Engine

**Business Purpose**
Resolve configuration values through a tenant-specific override → template default → system default hierarchy across every Configuration Studio surface.

**Why ERPNext Is Insufficient**
ERPNext has no native per-tenant configuration resolution concept; Company provides a native scoping anchor but not a layered override-resolution mechanism.

**Current ERPNext Capability**
Company (native scoping anchor only, per [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 8).

**Gap Description**
PrintHub requires the resolution-order mechanism described in [../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md). The formal definition of "tenant" itself, and the multi-tenant deployment model this resolution engine assumes, are open.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
Multi-tenant strategy ratification and Tenant/Company definition are under [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) **AR-002** — not resolved here.

**Implementation Priority**
Critical

---

#### Gap Name
Automation Rule Condition Evaluator

**Business Purpose**
Safely evaluate condition expressions (e.g., "only required if discount > 10%") within configured Automation Rules, without executing arbitrary code.

**Why ERPNext Is Insufficient**
Frappe's Server Script mechanism executes arbitrary Python, which is explicitly disallowed as the basis for Automation Rule conditions per [../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md)'s constrained-syntax requirement.

**Current ERPNext Capability**
Frappe Framework: Server Script (generic, not safely constrained for this purpose).

**Gap Description**
PrintHub requires a constrained, safely-evaluated condition-expression syntax, evaluated by the Application layer, distinct from raw Server Script execution.

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md).

**Implementation Priority**
Medium

---

#### Gap Name
Module Dependency Management

**Business Purpose**
Enforce and surface module-level dependency rules (a module cannot be enabled if a module it depends on is disabled) within the Module Manager.

**Why ERPNext Is Insufficient**
ERPNext's app-level dependency mechanism operates at the whole-app installation level, not at PrintHub's finer-grained business-module level.

**Current ERPNext Capability**
Frappe Framework: app-level `required_apps` dependency (coarser granularity only).

**Gap Description**
PrintHub requires a PrintOS Module Dependency data model and enforcement logic, per [../configuration/02_Module_Manager.md](../configuration/02_Module_Manager.md).

**Recommended Classification**
Customize

**Implementation Ownership**
printos_core

**Dependencies**
[../configuration/02_Module_Manager.md](../configuration/02_Module_Manager.md).

**Implementation Priority**
Medium

---

#### Gap Name
Industry Template / Configuration Package Library

**Business Purpose**
Onboard new print shops quickly using pre-built, versioned bundles of default Workflow, Approval, Form, and Dashboard configuration per print-industry vertical.

**Why ERPNext Is Insufficient**
No native ERPNext concept of a cross-designer configuration bundle/template exists.

**Current ERPNext Capability**
None directly applicable.

**Gap Description**
Per [../configuration/14_Template_Library.md](../configuration/14_Template_Library.md), this capability is designed for but does not yet have a first real template populated; it is explicitly noted there as validated only once a real Industry Template (e.g. "Signage Print Shop") exists.

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[../configuration/14_Template_Library.md](../configuration/14_Template_Library.md) Future Considerations.

**Implementation Priority**
Low

---

### INTELLIGENCE

---

#### Gap Name
MachineIQ

**Business Purpose**
Apply machine intelligence to production and business data for optimization and predictive insight.

**Why ERPNext Is Insufficient**
ERPNext has no analytical/machine-intelligence capability of this kind; this is intentionally outside ERPNext's scope as a generic ERP.

**Current ERPNext Capability**
None applicable — Reports/Analytics modules provide only the underlying data MachineIQ would consume.

**Gap Description**
Full scope is deliberately deferred per [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md); MachineIQ is consumed via the `MachineIntegrationService` port pattern ([10_Integration_Architecture.md](10_Integration_Architecture.md)) once scoped.

**Recommended Classification**
Plugin

**Implementation Ownership**
External Plugin (Future Phase for scoping)

**Dependencies**
[ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md), [10_Integration_Architecture.md](10_Integration_Architecture.md).

**Implementation Priority**
Low (Phase 1); scope re-evaluation is a prerequisite, not a Phase 1 build item.

---

#### Gap Name
Marketplace

**Business Purpose**
Connect public buyers (G1) directly with print shops as active platform participants.

**Why ERPNext Is Insufficient**
ERPNext has no public-facing, multi-seller marketplace capability; this is intentionally outside ERPNext's scope as a generic ERP.

**Current ERPNext Capability**
None applicable — CRM/Sales modules would be the routing target for Marketplace-originated orders once scoped.

**Gap Description**
Full scope is deliberately postponed to Phase 5 per [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).

**Recommended Classification**
Plugin

**Implementation Ownership**
External Plugin (Future Phase for scoping)

**Dependencies**
[ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md), [../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md) (Phase 5).

**Implementation Priority**
Low (Phase 1)

---

#### Gap Name
AI Assistant

**Business Purpose**
Not yet defined in any Approved Blueprint or Naming Registry document.

**Why ERPNext Is Insufficient**
Not assessable — the capability itself is not yet scoped or named authoritatively.

**Current ERPNext Capability**
Not assessable.

**Gap Description**
Per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) and [Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) **AR-003**, "AI Assistant" is not a registered Naming Registry term and has no corresponding Blueprint scoping document. This gap entry exists only to record the capability's presence in current task instructions, not to define or size it.

**Recommended Classification**
Plugin (provisional, pending naming/scoping)

**Implementation Ownership**
Future Phase

**Dependencies**
[Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) AR-003 — not resolved here.

**Implementation Priority**
Low

---

### OPERATIONS

---

#### Gap Name
Advanced Scheduling & Resource/Press Optimization

**Business Purpose**
Optimize machine/press assignment and sequencing beyond basic conflict-avoidance scheduling — e.g., minimizing changeover/makeready time across a day's job queue.

**Why ERPNext Is Insufficient**
Neither ERPNext nor PrintHub's Phase 1 Machine Scheduling scope (basic capability-matching and conflict detection, per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)) includes optimization algorithms of this kind.

**Current ERPNext Capability**
None applicable beyond basic Manufacturing scheduling (already assessed as structurally mismatched).

**Gap Description**
Explicitly listed as a Future Enhancement ("MachineIQ-driven optimal scheduling") in [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), not required for Phase 1's basic Machine Scheduling module.

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (Machine Scheduling, Future Enhancements); MachineIQ gap above.

**Implementation Priority**
Low

---

#### Gap Name
Machine Telemetry & IoT Integration

**Business Purpose**
Ingest real-time Machine Event/sensor data (PLC, telemetry, downtime, cycle counts) for future MachineIQ consumption.

**Why ERPNext Is Insufficient**
ERPNext has no native IoT/telemetry ingestion capability.

**Current ERPNext Capability**
None applicable.

**Gap Description**
Provisionally reserved vocabulary only (PLC, Sensor, Telemetry, Machine Event — [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 36); no Phase 1 implementation requirement exists.

**Recommended Classification**
Plugin

**Implementation Ownership**
Future Phase

**Dependencies**
[../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 36; MachineIQ gap above.

**Implementation Priority**
Low

---

#### Gap Name
Production Cost Optimization Analytics

**Business Purpose**
Analyze production cost drivers (material waste, machine downtime cost, rework cost) to identify optimization opportunities.

**Why ERPNext Is Insufficient**
ERPNext's native Accounts/Reports capability provides financial reporting but not print-production-specific cost-driver analysis.

**Current ERPNext Capability**
Accounts module: standard financial reports (partial data source only).

**Gap Description**
Not currently scoped in any Approved Blueprint document beyond basic "Job throughput time, rework rate" reporting already listed under Job Cards in [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md). Deeper cost-optimization analytics is MachineIQ-adjacent and unscoped.

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (Job Cards, Analytics); MachineIQ gap above.

**Implementation Priority**
Low

---

#### Gap Name
Predictive Production Analytics

**Business Purpose**
Forecast production bottlenecks, delivery risk, or capacity constraints ahead of time using historical production data.

**Why ERPNext Is Insufficient**
ERPNext's native Analytics/Reports capability is descriptive (what happened), not predictive.

**Current ERPNext Capability**
Reports/Analytics modules provide the historical data source only.

**Gap Description**
Explicitly listed as a Future Enhancement ("Predictive analytics via MachineIQ") in [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) (Reporting context) and [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) (Analytics module).

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md); MachineIQ gap above.

**Implementation Priority**
Low

---

### EXTERNAL PLUGIN BOUNDARY

The following capabilities were already classified as Plugin in [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 6; they are restated here in full gap-record form because the task scope requires every gap to carry the complete template, not because their classification changes.

---

#### Gap Name
Payment Gateway Integration

**Business Purpose**
Process customer payments through an external payment gateway.

**Why ERPNext Is Insufficient**
ERPNext has no built-in connection to any specific third-party payment gateway; this is inherently an external integration concern regardless of ERP maturity.

**Current ERPNext Capability**
Accounts module: Payment Entry (records the outcome; does not process the payment itself).

**Gap Description**
An Infrastructure-layer adapter per gateway, consumed via a `PaymentGatewayPort`, per [10_Integration_Architecture.md](10_Integration_Architecture.md).

**Recommended Classification**
Plugin

**Implementation Ownership**
External Plugin

**Dependencies**
[10_Integration_Architecture.md](10_Integration_Architecture.md); [../configuration/11_Integration_Designer.md](../configuration/11_Integration_Designer.md).

**Implementation Priority**
Medium

---

#### Gap Name
WhatsApp / SMS Messaging Integration

**Business Purpose**
Send operational notifications (approval requests, status updates) to Customers/staff via WhatsApp or SMS channels.

**Why ERPNext Is Insufficient**
ERPNext has native Email notification only; WhatsApp/SMS require external channel providers.

**Current ERPNext Capability**
Frappe Framework: Notification, Email Alert (Email channel only).

**Gap Description**
Adapter per channel, consumed via the Notification Designer's channel abstraction, per [../configuration/08_Notification_Designer.md](../configuration/08_Notification_Designer.md).

**Recommended Classification**
Plugin

**Implementation Ownership**
External Plugin

**Dependencies**
[../configuration/08_Notification_Designer.md](../configuration/08_Notification_Designer.md); [10_Integration_Architecture.md](10_Integration_Architecture.md).

**Implementation Priority**
Medium

---

#### Gap Name
Shipping Carrier Integration

**Business Purpose**
Obtain shipping rates/tracking from external logistics carriers for Dispatch.

**Why ERPNext Is Insufficient**
ERPNext has no native carrier-rate or tracking integration.

**Current ERPNext Capability**
Selling/Stock module: Delivery Note (records the shipment; does not integrate with a carrier).

**Gap Description**
Adapter per carrier, consumed via a `CarrierPort`, per [10_Integration_Architecture.md](10_Integration_Architecture.md).

**Recommended Classification**
Plugin

**Implementation Ownership**
External Plugin

**Dependencies**
[10_Integration_Architecture.md](10_Integration_Architecture.md).

**Implementation Priority**
Low

---

#### Gap Name
Cloud / External Storage

**Business Purpose**
Store Artwork/Proof files at scale beyond Frappe's native file storage capacity.

**Why ERPNext Is Insufficient**
Not currently insufficient — Frappe's native File/attachment mechanism is adequate for Phase 1 scale, per [10_Integration_Architecture.md](10_Integration_Architecture.md).

**Current ERPNext Capability**
Frappe Framework: File attachment (Native/Extend, sufficient today).

**Gap Description**
Only becomes a genuine gap at a scale not yet reached; recorded here for completeness of the Plugin boundary, not as a Phase 1 requirement.

**Recommended Classification**
Future

**Implementation Ownership**
Future Phase

**Dependencies**
[10_Integration_Architecture.md](10_Integration_Architecture.md).

**Implementation Priority**
Low

---

## 4. Cross-Gap Dependency Matrix

| Gap | Depends On | Related Review Item | Priority |
|---|---|---|---|
| Print Estimation & Costing Engine | Print Specification Modeling | AR-005, AR-010 | Critical |
| Print Specification Modeling | — | AR-003 | High |
| Artwork & Proof Lifecycle | — | — | Critical |
| Business Approval Orchestration | Artwork & Proof Lifecycle | AR-003 | High |
| Production Planning & Orchestration | Artwork & Proof Lifecycle, Machine Scheduling & Capability Modeling | — | Critical |
| Job Card Execution & Lifecycle | Production Planning & Orchestration | — | Critical |
| Machine Scheduling & Capability Modeling | Job Card Execution & Lifecycle | AR-004 | Critical |
| Finishing Process Tracking | Print Specification Modeling | — | Low |
| Quality Check Processing | Job Card Execution & Lifecycle | AR-009 | Medium |
| Dispatch-Specific Data Capture | Production Planning & Orchestration | AR-008 | Medium |
| Customer Production Visibility / Tracking | Job Card Execution & Lifecycle, Marketplace | — | Low |
| Configuration Studio Governance Layer | — | — | Critical |
| Feature Flag Engine | Configuration Studio Governance Layer | — | High |
| Tenant Customization / Resolution Engine | Configuration Studio Governance Layer | AR-002 | Critical |
| Automation Rule Condition Evaluator | Configuration Studio Governance Layer | — | Medium |
| Module Dependency Management | Configuration Studio Governance Layer | — | Medium |
| Industry Template / Configuration Package Library | Configuration Studio Governance Layer | — | Low |
| MachineIQ | Machine Scheduling & Capability Modeling, Machine Telemetry & IoT Integration | — | Low |
| Marketplace | Customer Production Visibility / Tracking | — | Low |
| AI Assistant | — | AR-003 | Low |
| Advanced Scheduling & Resource/Press Optimization | Machine Scheduling & Capability Modeling, MachineIQ | — | Low |
| Machine Telemetry & IoT Integration | Machine Scheduling & Capability Modeling | — | Low |
| Production Cost Optimization Analytics | Job Card Execution & Lifecycle, MachineIQ | — | Low |
| Predictive Production Analytics | Production Cost Optimization Analytics, MachineIQ | — | Low |
| Payment Gateway Integration | — | — | Medium |
| WhatsApp / SMS Messaging Integration | — | — | Medium |
| Shipping Carrier Integration | Dispatch-Specific Data Capture | — | Low |
| Cloud / External Storage | Artwork & Proof Lifecycle | — | Low |

---

## 5. Implementation Boundary Summary

**ERPNext Responsibilities**
Document structure and submission mechanics for Sales Order, Quotation, Purchase Order, Delivery Note, Item; all Accounts/GL/Payment/Tax processing; Customer/Supplier/Warehouse/Employee master records; the native Workflow, Role/Permission, Custom Field, Print Format, Report Builder, and Notification mechanisms that every Configuration Studio designer configures rather than replaces.

**printos_core Responsibilities**
Print Estimation & Costing, Print Specification Modeling, Artwork & Proof Lifecycle, Business Approval Orchestration, Production Planning & Orchestration, Job Card Execution & Lifecycle, Machine Scheduling & Capability Modeling, Quality Check Processing, the Configuration Studio Governance Layer (versioning/rollback/audit/dependency management), Feature Flag Engine, Tenant Customization/Resolution Engine, and Automation Rule Condition Evaluator. All of these are Customize-classified because no ERPNext native equivalent exists, and each is justified by an existing Core Domain or Configuration Studio designation already Approved in Blueprint/Configuration documentation — none is newly invented here.

**External Plugin Responsibilities**
MachineIQ, Marketplace, AI Assistant (pending naming resolution), Payment Gateways, WhatsApp/SMS Messaging, Shipping Carriers, and (at future scale) Cloud Storage — all consumed exclusively through Ports & Adapters, never embedded in `printos_core` Domain/Application logic directly.

**Future Responsibilities**
Finishing Process Tracking, Customer Production Visibility, Industry Template Library, Advanced Scheduling/Press Optimization, Machine Telemetry/IoT Integration, Production Cost Optimization Analytics, Predictive Production Analytics — each deferred either by explicit Blueprint "Future Enhancement" notation or by dependency on a not-yet-scoped capability (MachineIQ, Marketplace).

---

## 6. Recommendations

1. **The Critical-priority gaps (Print Estimation & Costing Engine, Artwork & Proof Lifecycle, Production Planning & Orchestration, Job Card Execution & Lifecycle, Machine Scheduling & Capability Modeling, Configuration Studio Governance Layer, Tenant Customization/Resolution Engine) represent the true build scope of Phase 1** — everything else in this document is either a bounded extension (Dispatch-Specific Data Capture), a Plugin-boundary integration, or explicitly Future.
2. **Two Critical gaps are directly gated by open Architecture Review items and cannot proceed to DocType Mapping without them**: Machine Scheduling & Capability Modeling (AR-004) and Tenant Customization/Resolution Engine (AR-002). This document does not resolve either; it only confirms their gap scope.
3. **The Job Card / Manufacturing name-collision finding from the Fit Analysis is the single highest-leverage risk to avoid duplicating ERPNext functionality** — every Production-domain gap above (Production Planning, Job Card, Machine Scheduling, Quality Check) is scoped explicitly to avoid reusing ERPNext's Manufacturing module, consistent with that finding.
4. **The Configuration Studio's genuine gap is the governance layer, not the individual designers** — each designer (Workflow, Approval, Form, Dashboard, Report, Notification) configures an existing native Frappe mechanism; the real build effort is the versioning/rollback/audit/dependency-management layer that makes those mechanisms behave as a coherent, governed platform.
5. **The Plugin boundary is uniform** — MachineIQ, Marketplace, AI Assistant, and every external integration (payment, messaging, shipping, storage) should be treated identically at the architecture level: consumed via Ports & Adapters, owned outside `printos_core`, with no special-casing per capability.
6. **AI Assistant remains a naming/scoping gap, not yet a sizeable engineering gap** — until AR-003 is addressed and a Blueprint scoping document exists, no further gap sizing is possible for it.
7. **This document should be the direct input to ERPNext DocType Mapping**, using the Customize-classified gaps above as the candidate DocType list, and the Extend-classified gap (Dispatch-Specific Data Capture) plus every Extend-classified capability already confirmed in the Fit Analysis as the candidate Custom Field list — DocType Mapping should not re-derive gap scope independently.

---

# Related Documents

- [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [00_Architecture_Index.md](00_Architecture_Index.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-24 | Initial | Initial ERPNext Gap Analysis. Identified 28 capability gaps (1 Extend, 13 Customize, 7 Plugin, 7 Future) across Print Domain, Configuration Platform, Intelligence, and Operations, building strictly on `ERPNext_Fit_Analysis.md` and referencing (without resolving) `Architecture_Review_Register.md` items AR-002, AR-003, AR-004, AR-005, AR-008, AR-009, AR-010. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] No Architecture Review Register item resolved or recommended
- [ ] Consistent with ERPNext Fit Analysis baseline
- [ ] Reviewed by Project Owner
