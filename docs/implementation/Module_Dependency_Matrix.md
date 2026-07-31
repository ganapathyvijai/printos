# Module Dependency Matrix

Version:
0.7

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-31

---

## 1. Purpose

This document defines the implementation dependencies between PrintHub modules — what must exist, at an architectural level, before each module can be implemented. It formalizes the prior dependency analysis and dependency review findings into a structured, categorized reference. It answers "what must exist before X can be implemented," not "in what order should we build X."

---

## 2. Scope

This is a Module Dependency Matrix, not a sprint plan, not a runtime dependency graph, and not a deployment dependency graph. It does not assign dates, does not sequence work into phases or sprints, and does not resolve any open Architecture Review Register item. It draws exclusively on already-Published Blueprint, Business, Architecture, Technical, Database, Configuration, Standards, ADR, and Architecture-tier documentation, plus the two recently resolved clarifications: Estimation → Sales (not the reverse), and Configuration Studio's ownership of Report Definition/Dashboard Definition with Reporting as consumer.

This matrix covers every Bounded Context/module in [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) and [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), including GST and Analytics, and treats Production Planning, Job Cards, and Machine Scheduling as three distinct modules, per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md).

---

## 3. Dependency Classification Model

- **Business Dependency** — A module requires a business output (a completed record, an approval, a confirmed decision) from another module before its own business process can proceed. Example: Sales consumes an approved Quotation from Estimation.
- **Implementation Dependency** — A module requires a data model, entity, or domain concept to exist first, independent of business-process sequencing. Example: Estimation requires Material/Substrate and Machine Profile master data to compute a real price.
- **ERPNext Dependency** — A module depends on a native or extended ERPNext object. Example: Warehouse, Supplier, UOM, Currency, Tax Template.
- **Configuration Dependency** — A module depends on a Configuration Studio artifact. Example: Feature Flag, Module Definition, Automation Rule.
- **Plugin Dependency** — A module depends on an external provider abstraction reached through Plugin Architecture. Example: Payment Provider, Machine Integration (MachineIQ), AI Provider.
- **Architecture Review Dependency** — A module is blocked or delayed by an open Architecture Review Register item. Example: AR-004 Machine, AR-005 Quotation, AR-006 Material/Product Template.
- **Transitive Dependency** — A module is delayed only because an upstream module it depends on is itself delayed; the downstream module carries no direct blocker of its own. Example: Dispatch is delayed by the Job Cards module's incompleteness, but is not itself directly blocked by AR-004.
- **Optional Dependency** — A module's minimal implementation does not require this input, but a fuller or better-governed implementation benefits from it once available. Example: Artwork's approval gate can use a default mechanism before the Approval Designer exists.

---

## 4. Module Dependency Summary

| Module / Context | Primary Prerequisite(s) |
|---|---|
| Administration | None |
| HR | Administration |
| CRM | Administration |
| Estimation | CRM; Inventory (Material/Substrate); Production (Machine Profile) |
| Sales | Estimation (approved Quotation), with a reorder exception |
| Artwork | Sales (confirmed **Submitted Sales Order**) |
| Job Cards | Sales Order, **Approved Production Artwork Set** from Artwork (work-unit contract, tier A; recorded 2026-07-31); Machine Scheduling + Inventory + Production Planning output for operational completion (tier B) |
| Machine Scheduling | Job Card work-unit contract; Machine Profile |
| Production Planning | Sales, **Approved Production Artwork Set** from Artwork; consumes Job Cards + Machine Scheduling → produces Scheduled Job Cards |
| Inventory | Administration; UOM |
| Purchasing / Procurement | Inventory (replenishment signal) |
| Warehouse | Administration |
| Dispatch / Delivery | Job Cards (complete), Warehouse |
| Accounts | Dispatch, with an advance-billing exception |
| GST | Accounts (tightly coupled) |
| Reporting | Consumes data from most other contexts; consumes Configuration Studio's Published Report/Dashboard Definitions |
| Analytics | Reporting/Reports; Configuration Studio's Published Report/Dashboard Definitions |
| Configuration Studio | None foundational for most designers; Feature Flag/Automation Rule/Module Definition have no native fallback |
| MachineIQ | Machine (AR-004), Production data — MachineIQ depends on Production, not the reverse |
| Marketplace | None technically; routes into CRM/Sales once active |
| AI Assistant | Not assessable — unregistered, unscoped |
| Plugin Architecture | None — Ports definable independent of any specific module |

---

## 5. Foundation Layer

The near-foundational, low-risk layer is broader than Administration alone, but it is not dependency-free — HR and Warehouse both require Administration (Company/Branch) to exist first, per Section 7. What distinguishes this layer is not the absence of any prerequisite, but the absence of any open Architecture Review exposure: every module below is Native ERPNext or otherwise low-risk foundational capability, and none carries an Architecture Review Dependency.

- Administration (Company, Branch) — the sole entry with no prerequisite of its own
- HR (Department, Employee) — Administration-dependent
- Warehouse — Administration-dependent
- Supplier
- Purchase Order structure
- UOM
- Currency
- Tax Template
- Payment Terms

This is the foundation layer in the sense that it carries no unresolved *module-specific* Architecture Review item, not in the sense that it has no prerequisites at all. In dependency terms, Administration is the upstream foundation with no prerequisite of its own; HR and Warehouse each hold a prerequisite (dependency) relationship to Administration, while the remaining ERPNext-native reference items in this layer carry no dependency on one another. This states dependency relationships, not build order.

**Matrix Implementation Impact — AR-001:** AR-001 is formally **Resolved** through Project Owner selection of Option A (Architecture Review Register), reaffirming the Accepted [ADR-001-ERPNext-Framework](../decisions/ADR-001-ERPNext-Framework.md) decision. **ERPNext v16, with the corresponding Frappe v16 major version, is the governed target**; simultaneous v15/v16 support is not in scope; minor and patch selection remains centrally governed. This removes AR-001 as a current development blocker (Priority remains historically Critical). All ERPNext-native and Extended classifications in this layer, and throughout this matrix, were originally stated against v16 as the working basis, the same basis already used in [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) — that basis is now the governed target rather than an assumption. Foundation modules carry no *module-specific* Architecture Review exposure; their remaining *cross-cutting* exposure is no longer an open version-selection question but **pending technical revalidation**: their ERPNext-facing classifications (capability-level Fit, Gap, DocType, hook, event, permission, deployment, and integration claims) remain conditionally assessable only, pending evidence-backed verification against the governed v16 target. Resolution of AR-001 does not authorize implementation of any ERPNext-native or Extended element; final implementation of any such element still requires that technical revalidation, a Published implementation specification, and a separate scoped implementation-authorization decision. See Section 13 for AR-001's official register status.

---

## 6. Core Business Dependency Spine

The primary business dependency spine, corrected per the resolved Estimation/Sales clarification, is expressed as a dependency chain — each node depends on those to its left — and is not a build order:

Administration → CRM → Estimation → Sales → Artwork → Job Card creation (production work unit) → Production Planning + Machine Scheduling → Scheduled Job Card (executable production) → Dispatch → Accounts

The Production segment is deliberately expanded to avoid implying a circular dependency. Its non-circular reading, consistent with [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), is:

- **Job Cards** provide the production work unit — the custom PrintHub Job Card contract (the "Job Card scaffolding" / tier A defined in Section 7). Job Cards do not depend on Production Planning to exist; the work-unit contract is defined from the Sales Order and Approved Artwork.
- **Machine Scheduling** provides machine-assignment capability against the Job Card contract and Machine Profile (`09_PrintOS_Modules.md`: "Dependencies: Machine Profile master data, Job Cards").
- **Production Planning** *consumes* Job Cards and Machine Scheduling and *produces* Scheduled Job Cards (`09_PrintOS_Modules.md`: "Dependencies: Job Cards, Machine Scheduling"; "Outputs: Scheduled Job Cards"). Production Planning does not create the Job Card concept.
- **Operational completion** of a Job Card — an executable, scheduled production unit — depends on the scheduled/assigned production plan: the outputs of Machine Scheduling and Production Planning, plus material allocation from Inventory.

Inventory feeds material allocation into operational Job Card completion. Production Planning, Job Cards, and Machine Scheduling remain three distinct modules, per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md), and are not collapsed into one.

Reporting and Analytics are not part of this spine. Both are consumers of transactional output from most nodes on the spine (and of Configuration Studio's Published Report Definitions and Dashboard Definitions) — neither gates progress through the spine, and neither is a prerequisite for any other module. GST is tightly coupled to Accounts and sits just downstream of it, not on the primary spine itself.

---

## 7. Detailed Module Dependency Matrix

### Administration
- Required prerequisites: None
- Optional prerequisites: None
- ERPNext dependencies: Company, Branch (Native)
- Configuration dependencies: None
- Plugin dependencies: None
- Architecture Review dependencies: None
- Directly blocked: No
- Transitively delayed: No
- Notes: Root of the dependency graph.

### HR
- Required prerequisites: Administration
- Optional prerequisites: None
- ERPNext dependencies: Employee, Department (Native)
- Configuration dependencies: None
- Plugin dependencies: None
- Architecture Review dependencies: None
- Directly blocked: No
- Transitively delayed: No
- Notes: Feeds Production (Operator assignment) later, non-blocking to either side.

### CRM
- Required prerequisites: Administration
- Optional prerequisites: None
- ERPNext dependencies: Customer (Extended)
- Configuration dependencies: None required to bootstrap
- Plugin dependencies: None
- Architecture Review dependencies: AR-011 (Lead/Enquiry mapping)
- Directly blocked: Customer — No. Lead/Enquiry — Yes, specifically.
- Transitively delayed: No
- Notes: Customer carries no module-specific Architecture Review blocker; AR-001's cross-cutting version-selection gate is Resolved (Section 5), with technical revalidation pending. Only Enquiry-specific behavior remains gated by AR-011.

### Estimation
- Required prerequisites: CRM (Customer/Enquiry); Inventory (Material/Substrate master data); Production (Machine Profile master data)
- Optional prerequisites: Configuration Studio Form Designer, Workflow Designer (both deferrable)
- ERPNext dependencies: Undetermined pending AR-006 (Product Template/Material mapping)
- Configuration dependencies: None required to bootstrap
- Plugin dependencies: None
- Architecture Review dependencies: AR-005 (Quotation strategy), AR-006 (Material/Product Template, shared with Inventory), AR-010 (BOM necessity — affects the Estimation Engine's internals, not entity existence)
- Directly blocked: Yes — Quotation, Quotation Line, and Product Template are Pending Architecture Review. Job Types, Finishing Types, and Paper Sizes reference data are not blocked.
- Transitively delayed: Not applicable — already directly blocked on its core entities.
- Notes: Business-workflow-upstream of Production, but implementation-downstream of Production's and Inventory's master data. Do not model Estimation as dependent only on CRM.

### Sales
- Required prerequisites: Estimation (approved Quotation) — standard path
- Optional prerequisites: None
- ERPNext dependencies: Sales Order (Extended)
- Configuration dependencies: Workflow Designer (optional)
- Plugin dependencies: None
- Architecture Review dependencies: AR-005, indirectly, for the standard Quotation-based path only
- Directly blocked: No — Sales Order itself is Extended ERPNext, not Pending.
- Transitively delayed: Yes, for the standard path, via Estimation's AR-005/AR-006 exposure.
- Notes: Business Rules permit direct order entry for repeat/reorder customers without a fresh Quotation. This reorder path is not delayed by Estimation's open items and can be built and tested independently of the standard path.

### Artwork
- Required prerequisites: Sales (confirmed **Submitted** Sales Order)
- Optional prerequisites: Configuration Studio Approval Designer (deferrable — a default approval gate can precede it)
- ERPNext dependencies: File attachment (Native, underlying primitive only)
- Configuration dependencies: Approval Designer, Notification Designer (both optional)
- Plugin dependencies: Cloud/External Storage (optional, deferrable — native File sufficient at Phase 1 scale)
- Architecture Review dependencies: AR-003, non-blocking (Approval Record naming only)
- Directly blocked: No
- Transitively delayed: Yes, via Sales's exposure to Estimation on the standard path; not delayed on the reorder path.
- Notes: Approved Artwork is a hard gate before Job Cards production may begin, per Business Rules. **Production-capable Artwork authority (recorded 2026-07-31; corrected 2026-07-31 following formal Architecture and Business Review):** following the Project Owner's production-capable track selection (2026-07-30) and approval of the Artwork design defaults (2026-07-31), the gate consumed by Production is the **Approved Production Artwork Set** — see [../blueprint/18_Artwork_Management.md](../blueprint/18_Artwork_Management.md) (Draft 0.2; Architecture Review and Business Review both **Corrections Required**, blocking findings ART-ARCH-F1 and ART-BIZ-F1, corrections applied, targeted re-review pending) and [../database/Artwork_Authority_DocType_Specification.md](../database/Artwork_Authority_DocType_Specification.md) (Draft 0.2; same review status). The Artwork bounded context **owns** the complete Artwork lifecycle; Production **consumes** the approved set and does not own or mutate it. **Dependency direction: Sales → Artwork → Production. The reverse dependency Artwork → Job Card is prohibited.** The Configuration Studio **Approval Designer remains optional and deferrable**, so a default governed Artwork approval mechanism must exist without it. **Customer Approval Evidence (Artwork-internal, working name pending naming treatment) does not create a Production-to-Proof or Production-to-Approval-Record dependency** — it is consumed only inside the Artwork context's own Revision-approval operation, never by Production directly. **Job Card Tier A production-capable Publication depends on the Artwork authority specification passing targeted re-review and its required runtime validation.** **Documentation-only Artwork authority design was approved on 2026-07-31; the Artwork implementation workstream remains Not Authorized; the Job Card implementation workstream remains Not Authorized; documentation-only design authorization does not grant implementation authorization.** Exact `printos_core` module paths for Artwork and Job Card remain unresolved and are pre-Publication decisions.

### Production Planning
- Required prerequisites: Sales Order (confirmed); Approved Artwork; Job Cards (the production work-unit contract it plans against); Machine Scheduling (the assignment capability it schedules against)
- Consumes / produces: Consumes Job Cards and Machine Scheduling; produces Scheduled Job Cards, per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) ("Dependencies: Job Cards, Machine Scheduling"; "Outputs: Scheduled Job Cards")
- Optional prerequisites: Configuration Studio Workflow Designer (deferrable — a default state machine can precede it)
- ERPNext dependencies: None identified — capacity planning, job prioritization, and schedule coordination have no ERPNext equivalent that does not assume BOM-driven manufacturing, per [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Section 3 (Manufacturing).
- Configuration dependencies: Workflow Designer (optional)
- Plugin dependencies: None required; MachineIQ is additive only
- Architecture Review dependencies: AR-005 (Quotation, indirect via Sales Order standard path); AR-004 and AR-006 reach Production Planning indirectly, through the Job Cards and Machine Scheduling inputs it consumes
- Directly blocked: No — Production Planning as a capability has no Architecture Review item of its own.
- Transitively delayed: Yes — via Sales/Estimation's AR-005 exposure on the standard path, and via its consumption of Job Cards and Machine Scheduling (which carry AR-006/AR-004 exposure).
- Notes: Distinct from Job Cards and Machine Scheduling, per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md). Sequences and prioritizes work and produces Scheduled Job Cards; it does **not** create the Job Card concept itself — the Job Card work-unit contract is owned by the Job Cards module (see Job Cards entry).

### Job Cards
- Two-tier dependency: This entry distinguishes the **Job Card work-unit contract** (tier A — the minimum custom PrintHub Job Card model/interface required so Machine Scheduling and Production Planning can reference a production work unit; this is what "Job Card scaffolding" means elsewhere in this matrix) from the **operationally schedulable Job Card** (tier B — the fully usable, executable Job Card). The two tiers have different prerequisites and different Architecture Review exposure.
- Required prerequisites (tier A, work-unit contract): Sales Order (confirmed); Approved Artwork. The contract is defined from these; it does **not** depend on Production Planning or Machine Scheduling to exist.
- Required prerequisites (tier B, operational completion): Machine Scheduling (machine assignment); Inventory (material allocation); Production Planning output (Scheduled Job Card / production plan).
- Optional prerequisites: Configuration Studio Workflow Designer (deferrable — a default state machine can precede it)
- ERPNext dependencies: None — must not reuse ERPNext's native Manufacturing Job Card, per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md). The PrintHub Job Card entity is a custom, print-domain concept, structurally and terminologically distinct from ERPNext's Manufacturing module.
- Configuration dependencies: Workflow Designer (optional)
- Plugin dependencies: None required; MachineIQ is additive only
- Architecture Review exposure: AR-004 (Machine — convergent input, via Machine Scheduling's machine-assignment input to tier B), AR-006 (Material — convergent input, via allocation to tier B), AR-005 (Quotation — transitive, via Sales Order standard path), AR-009 (Quality Check Record naming/scope — see Notes). These are convergent inputs and transitive exposure to end-to-end operation, not direct blockers of the Job Card entity itself.
- Directly blocked: No — no Architecture Review item names the Job Card entity as its subject, and the custom PrintHub Job Card work-unit contract (tier A) may be modeled independently, per ADR-014.
- Convergently blocked: Yes, for tier B (operationally schedulable Job Card) — end-to-end operation cannot complete without the Machine (AR-004) and Material (AR-006) convergent inputs, each blocked at its own source. This is **convergent blocking**, defined in Section 9 — not direct blocking of Job Cards, and not simple transitive delay.
- Transitively delayed: Additionally, via Sales/Estimation's AR-005 exposure on the standard path.
- Notes: The highest-coupled node in the implementation graph — see Section 8. Quality Check Record (a Job Cards child entity) carries AR-009 exposure; per the register, AR-009 is terminology/scope-related (standalone "Quality" module status) and does not block Quality Check Record's implementation as a Job Cards sub-feature today — see Section 13.

### Machine Scheduling
- Required prerequisites: Job Cards work-unit contract (the "Job Card scaffolding" — see Job Cards entry, tier A); Machine Profile. Machine Scheduling does **not** depend on Production Planning; rather, Production Planning consumes Machine Scheduling's output.
- Optional prerequisites: None
- ERPNext dependencies: Undetermined pending AR-004
- Configuration dependencies: None required
- Plugin dependencies: MachineIQ (additive only)
- Architecture Review dependencies: AR-004, direct — Machine and Machine Profile, this module's own structural target, are the subject of AR-004.
- Directly blocked: Yes — AR-004 names Machine/Machine Profile, this module's own structural target.
- Transitively delayed: Not applicable
- Notes: "Job Card scaffolding" here means the minimum Job Card work-unit contract (tier A in the Job Cards entry), not the operationally complete Job Card. Whether Machine Scheduling should be its own Bounded Context distinct from Production remains an open question in `06_Bounded_Contexts.md` itself (not an AR item) — noted for completeness, not resolved here. Remains a module distinct from Production Planning and Job Cards, per ADR-014.

### Inventory
- Required prerequisites: Administration; UOM
- Optional prerequisites: None
- ERPNext dependencies: UOM (Native); Item mapping undetermined pending AR-006
- Configuration dependencies: None
- Plugin dependencies: None
- Architecture Review dependencies: AR-006, direct, for Material/Substrate
- Directly blocked: Yes, for Material/Substrate specifically; Media Profiles and UOM are not blocked.
- Transitively delayed: Not applicable
- Notes: AR-006's blast radius extends beyond Inventory itself into Estimation and Purchasing — see Section 14.

### Purchasing / Procurement
- Required prerequisites: Inventory (replenishment signal)
- Optional prerequisites: None
- ERPNext dependencies: Purchase Order, Supplier (Native)
- Configuration dependencies: None
- Plugin dependencies: None
- Architecture Review dependencies: AR-006, transitive, via Material; AR-007, non-blocking (naming only)
- Directly blocked: No — Purchase Order and Supplier are Native ERPNext with no module-specific Architecture Review exposure; AR-001's cross-cutting version-selection gate is Resolved (Section 5), with technical revalidation pending.
- Transitively delayed: Yes, for replenishment-trigger logic specifically, via Inventory's AR-006 exposure; core Purchase Order/Supplier plumbing is not delayed.
- Notes: Per current Naming Registry/Architecture Review governance, the Blueprint module name is "Purchasing" and the Bounded Context name is "Procurement" (AR-007, Pending, non-blocking); this document uses both names as currently governed, without resolving the discrepancy.

### Warehouse
- Required prerequisites: Administration
- Optional prerequisites: None
- ERPNext dependencies: Warehouse (Native)
- Configuration dependencies: None
- Plugin dependencies: None
- Architecture Review dependencies: None
- Directly blocked: No
- Transitively delayed: No
- Notes: Foundation-tier; fully independent.

### Dispatch / Delivery
- Required prerequisites: Job Cards (complete); Warehouse
- Optional prerequisites: None
- ERPNext dependencies: Delivery Note (Extended)
- Configuration dependencies: None required
- Plugin dependencies: Shipping Provider (optional, additive)
- Architecture Review dependencies: AR-008, non-blocking (naming only)
- Directly blocked: No
- Transitively delayed: Yes — via Job Cards' convergent blocking (AR-004/AR-005/AR-006 chain).
- Notes: The clearest example in this matrix of transitive delay without direct blocking.

### Accounts
- Required prerequisites: Dispatch (confirmation) — standard path
- Optional prerequisites: None
- ERPNext dependencies: Sales Invoice, Payment Entry, Journal Entry, Tax Template, Payment Terms, Currency (all Native)
- Configuration dependencies: None
- Plugin dependencies: Payment Provider (optional, additive — manual Payment Entry works first)
- Architecture Review dependencies: None direct; transitive via the Dispatch→Job Cards chain for the standard path only
- Directly blocked: No
- Transitively delayed: Yes, for the standard dispatch-triggered invoicing path; the advance-billing exception path (per Business Rules) is not delayed.
- Notes: A second exception path enabling early testability, analogous to the Sales reorder exception.

### GST
- Required prerequisites: Accounts
- Optional prerequisites: None
- ERPNext dependencies: GST Configuration, Sales Taxes and Charges Template (Native)
- Configuration dependencies: None
- Plugin dependencies: Tax Services (optional, additive — only relevant beyond ERPNext's native Phase 1 GST capability)
- Architecture Review dependencies: None direct; transitive via Accounts' own exposure to the Dispatch→Job Cards chain for the standard path
- Directly blocked: No
- Transitively delayed: Yes, to the same extent as Accounts on the standard path.
- Notes: Per `06_Bounded_Contexts.md`, "tightly coupled to Accounts; relies on ERPNext's generic tax capability." That document's own Open Question — whether GST should be folded into Accounts as a single context — remains open and is not an Architecture Review item; noted for completeness, not resolved here.

### Reporting
- Required prerequisites: Data from the transactional modules it reports on; Published Report Definitions and Dashboard Definitions from Configuration Studio
- Optional prerequisites: None
- ERPNext dependencies: None directly
- Configuration dependencies: Report Definition, Dashboard Definition (Configuration Studio-owned; Reporting consumes Published instances only)
- Plugin dependencies: BI Platforms / Reporting Services (optional, additive)
- Architecture Review dependencies: None
- Directly blocked: No
- Transitively delayed: Only insofar as the specific module it reports on is itself incomplete — not a blanket delay.
- Notes: Reporting is a consumer, not a process gate; it does not block any other module's implementation. Ownership of Report Definition/Dashboard Definition by Configuration Studio, with Reporting as consumer, is resolved documentation terminology, not an open question.

### Analytics
- Required prerequisites: Reporting/Reports (per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), "Dependencies: Reports module")
- Optional prerequisites: None
- ERPNext dependencies: None directly
- Configuration dependencies: Report Definition, Dashboard Definition (Configuration Studio-owned; consumed, not duplicated)
- Plugin dependencies: BI Platforms / Reporting Services (optional, additive)
- Architecture Review dependencies: None
- Directly blocked: No
- Transitively delayed: Only to the extent the underlying Reports/data sources it aggregates are themselves incomplete — not a blanket delay.
- Notes: Basic analytics is in scope for Phase 1 per `09_PrintOS_Modules.md`; advanced/predictive analytics is an explicit Future Enhancement deferred to MachineIQ, consistent with Section 12's treatment of MachineIQ as additive.

### Configuration Studio
- Required prerequisites: None foundational for Workflow, Approval, Form, Dashboard, Report, Notification, and Integration Designers — each configures a native Frappe mechanism usable directly first.
- Optional prerequisites: Not applicable — see Notes for the true hard-prerequisite subset.
- ERPNext dependencies: Workflow, Customize Form/Client Script, Dashboard Chart/Number Card, Query/Script Report, Notification/Email Alert, Webhook (all Extended)
- Configuration dependencies: Not applicable — this is the Configuration layer itself
- Plugin dependencies: Integration Definition underlies Plugin Architecture's configuration integration
- Architecture Review dependencies: AR-002 (Resolved), for Tenant Override specifically
- Directly blocked: Tenant Override only. All other Configuration Studio artifacts are unblocked.
- Transitively delayed: No
- Notes: Feature Flag, Automation Rule, and Module Definition have no native ERPNext fallback and are true hard prerequisites for any module that needs conditional feature-gating, safe automation, or module enable/disable — see Section 11 for the full decomposition. AR-002 is Resolved (Option A: one isolated Frappe site and one isolated operational database per Tenant, per Accepted ADR-015 and `../blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0), which supplies the governed topology for Tenant Override, but this does not itself authorize Tenant Override's implementation — Blueprint 25 remains unpublished and no scoped implementation authorization has been granted, so Tenant Override remains blocked pending those separate steps.

### MachineIQ
- Required prerequisites: Machine (AR-004) and Production Planning/Job Cards data must exist for MachineIQ to have anything to consume
- Optional prerequisites: Not applicable
- ERPNext dependencies: None (External Plugin)
- Configuration dependencies: Integration Definition, Feature Flag (gating)
- Plugin dependencies: MachineIQ Service Model
- Architecture Review dependencies: AR-004, indirectly (MachineIQ depends on Machine's resolution, not the reverse)
- Directly blocked: No — scope is deferred per ADR-008, which is a different disposition than an AR-item block.
- Transitively delayed: Not applicable — additive, not on any other module's dependency path.
- Notes: The platform ships completely without it.

### Marketplace
- Required prerequisites: None technically, to build the plugin surface in isolation
- Optional prerequisites: Not applicable
- ERPNext dependencies: None (External Plugin)
- Configuration dependencies: Integration Definition, Feature Flag
- Plugin dependencies: Marketplace Service Model
- Architecture Review dependencies: None direct; scope deferred per ADR-009
- Directly blocked: No
- Transitively delayed: No technical delay. The Blueprint's own roadmap sequencing (Phases 2–4 preceding Phase 5) is a stated business rationale, not a technical dependency, and is preserved here as a distinct consideration rather than conflated with an architectural blocker.
- Notes: Architecturally buildable independently; business sequencing is a separate question this matrix does not adjudicate.

### AI Assistant
- Required prerequisites: Not assessable
- Optional prerequisites: Not assessable
- ERPNext dependencies: Not assessable
- Configuration dependencies: Not assessable
- Plugin dependencies: Not assessable
- Architecture Review dependencies: AR-003
- Directly blocked: Yes — blocks only its own definitional existence.
- Transitively delayed: Not applicable
- Notes: Blocks nothing else in the platform. Completely additive if and when scoped.

### Plugin Architecture
- Required prerequisites: None — Ports are definable independent of any specific business module's completion
- Optional prerequisites: Not applicable
- ERPNext dependencies: None, by definition (external boundary)
- Configuration dependencies: Integration Definition, per plugin instance
- Plugin dependencies: Not applicable — this is the governing pattern, not an instance
- Architecture Review dependencies: AR-002 (Resolved), for tenant-scoped plugin configuration only
- Directly blocked: No
- Transitively delayed: No
- Notes: No plugin category is a Phase 1 blocker; every category has a documented degrade-gracefully or manual-fallback path. AR-002's resolution (Option A, one isolated Frappe site and operational database per Tenant) establishes the governed topology for future tenant-scoped plugin configuration, but does not authorize its implementation; tenant-scoped plugin configuration remains a separate, undesigned concern.

---

## 8. Critical Implementation Path

The real convergence in this dependency analysis is the operationally schedulable Job Card (Job Cards tier B, per Section 7) — not the Job Card work-unit contract, which is dependency-light. Expressed as a dependency chain (Administration → CRM → Estimation → Sales → Artwork → Job Card contract → Machine Scheduling + Production Planning → Scheduled Job Card → Dispatch → Accounts), the convergence point is the Scheduled Job Card: the Sales Order path (carrying Estimation's AR-005 exposure), the Artwork approval gate, Inventory's material allocation (carrying AR-006 exposure), Machine Scheduling's machine assignment (carrying AR-004 exposure), and Production Planning's produced schedule all must be satisfied before an operationally complete Job Card exists. The operationally schedulable Job Card is therefore the highest-coupled node in the entire dependency graph — not because the Job Card contract has the most prerequisites in isolation, but because operational completion is where three separate Architecture Review items' consequences all converge. This convergence does not merge Production Planning, Job Cards, and Machine Scheduling into one module — per ADR-014 they remain distinct, and Production Planning consumes (does not create) the Job Card — it only means their individual outputs must all reach the Scheduled Job Card before executable production is possible. Nothing downstream of the Scheduled Job Card (Dispatch, Accounts, GST, Reporting, Analytics) carries any independent blocker of its own. This is dependency analysis, not a statement of build order.

---

## 9. Direct Blockers vs Transitive Delays

This distinction is preserved deliberately to prevent over-classifying the platform as blocked. Three categories apply, not two:

- **Direct blocker** — An Architecture Review item concerns that module's own entity or structural target directly. The module's implementation location or shape is genuinely unknown.
- **Transitive delay** — A module's own implementation target is fully determined, but it sits downstream of a module that is itself blocked, so it cannot be completed *end-to-end* until that upstream module resolves.
- **Convergent blocking** — A module's individual entity is not itself the subject of any Architecture Review item and may be modeled independently, but the module cannot be considered fully, end-to-end implementable because two or more of its *own required inputs* (not merely an upstream business-process predecessor) are each directly blocked by separate Architecture Review items. This differs from a simple transitive delay because the blockers are inputs the module structurally requires, not just a predecessor in the business sequence; it differs from a simple direct blocker because no single Architecture Review item names the module's own entity as its subject.

Direct blockers: Estimation (Quotation, Product Template — AR-005, AR-006), Inventory (Material/Substrate — AR-006), Machine Scheduling (Machine, Machine Profile — AR-004), Configuration Studio's Tenant Override (AR-002, Resolved — implementation blocker retained pending Publication and separate implementation authorization, not architectural uncertainty), AI Assistant (its own existence — AR-003).

Convergently blocked: Job Cards, tier B (the operationally schedulable Job Card) — its own entity/contract is Custom PrintHub and not individually Pending, but operational completion structurally requires Machine (AR-004) and Material (AR-006) as convergent inputs, each directly blocked at its own source. The Job Card work-unit contract (tier A) is not convergently blocked and may be modeled independently. See Section 7 (Job Cards) and Section 8.

Transitive delays only, with no direct blocker or convergent blocking of their own: Production Planning (standard path — though it also carries indirect AR-004/AR-006 exposure through the Job Cards and Machine Scheduling inputs it consumes), Sales (standard path), Artwork (standard path), Dispatch, Accounts (standard path), GST (standard path), Purchasing/Procurement's replenishment logic (though its core Purchase Order/Supplier plumbing is neither blocked nor delayed).

Example, preserved explicitly per the prior dependency analysis: Dispatch is transitively delayed by the Job Cards module's incompleteness, but Dispatch itself is not directly blocked by AR-004 — Dispatch's own implementation target (Delivery Note, Extended) is fully determined today.

---

## 10. Parallel Implementation Opportunities

The following exhibit low mutual dependency and no *module-specific* directly-blocking Architecture Review exposure, and are therefore candidates for parallel treatment in dependency terms — subject, for every ERPNext-facing item, to the cross-cutting AR-001 disposition in Section 5: the version-selection gate is Resolved, but technical revalidation of capability-level claims remains pending:

- Administration (upstream foundation) together with its downstream consumers HR and Warehouse (the near-foundational layer defined in Section 5 — Administration-dependent, not dependency-free)
- ERPNext master data setup (UOM, Currency, Tax Template, Payment Terms, Supplier, Purchase Order structure)
- Configuration Studio designers that configure or extend native Frappe mechanisms (Workflow, Approval, Form, Dashboard, Report, Notification, Integration Designers), since each has a usable native fallback
- Plugin Architecture Port definitions, since a Port's contract does not depend on which business module calls it first
- Non-blocked Estimation and Production reference data (Job Types, Finishing Types, Paper Sizes, Media Profiles), which carry no Architecture Review exposure even while Quotation, Product Template, Material, and Machine remain Pending. Quality Check Record is a partial exception: it carries AR-009 exposure (terminology/scope-related, not a practical implementation blocker per Section 13), so it can proceed as a Job Cards sub-feature today, but is not entirely exposure-free the way the other reference data is.
- Sales' reorder-path plumbing, which does not require Estimation's standard-path items to be resolved
- Accounts' and GST's advance-billing-adjacent paths, for the same reason relative to Dispatch

---

## 11. Configuration Studio Dependency Treatment

Configuration Studio must not be treated as a single, monolithic prerequisite. Three distinct treatments apply:

**True platform prerequisites (no native fallback):** Feature Flag, Automation Rule, and Module Definition. Any module requiring conditional feature-gating, safely-evaluated automation, or module enable/disable cannot proceed on that specific capability until these exist as real `printos_core` capability — there is no raw-ERPNext substitute for them.

**Designer capabilities configuring a native mechanism (deferrable):** Workflow Designer, Approval Designer, Form Designer, Dashboard Designer, Report Designer, Notification Designer, and Integration Designer. Each configures an existing native Frappe/ERPNext mechanism (Workflow, Customize Form, Dashboard Chart, Query/Script Report, Notification, Webhook) that can be used directly, unconfigured by PrintHub's designer layer, as a bootstrap path. A module can launch on the raw native mechanism first and adopt the governed, versioned Configuration Studio layer later; this deferral avoids blocking the module's own domain logic on Configuration Studio's availability, but migrating an existing raw-native configuration into a managed Configuration Studio definition later may still require governance, validation, and conversion work of its own — this document does not assume that migration is free.

**Runtime consumption (Tenant-scoped, AR-002 Resolved):** Tenant Override specifically — and only the *tenant-aware* version of any configuration artifact — is gated by AR-002. AR-002 is Resolved: Project Owner selection of Option A establishes one isolated Frappe site and one isolated operational database per Tenant (per Accepted ADR-015 and `../blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0), with Tenant formally distinct from Company. This removes the prior topology uncertainty but does not authorize Tenant Override's implementation — Blueprint 25 is Approval, not Published, and a separate scoped implementation-authorization decision remains required. The single-installation, default-configuration version of every artifact in the second group above is unaffected by AR-002's resolution status.

---

## 12. Plugin Dependency Treatment

No plugin category is a Phase 1 blocker. Every category defined in Plugin Architecture has an explicit degrade-gracefully or manual-fallback path: Email relies on native Frappe SMTP in Phase 1 unless a future requirement justifies a dedicated messaging plugin; Payment Providers are optional from a platform-dependency perspective because native Payment Entry supports manual payment recording first; Shipping Providers, Tax Services, Storage, Identity, Industrial devices, and Analytics/BI integrations are all additive enhancements with documented fallback behavior. MachineIQ, Marketplace, and AI Assistant are additive in the strongest sense — none gates any core module's implementation, and the platform ships completely without any of them present.

---

## 13. Architecture Review Dependency Summary

The first four columns below (Register Status, Register Priority, Register Blocks Development) are reproduced verbatim from [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) Section 2 and must not be reinterpreted here. The final two columns (Matrix Implementation Impact, Notes) are this document's own interpretation of practical implementation consequence for module planning purposes and are kept visibly separate from the register's official fields.

| AR ID | Topic | Register Status | Register Priority | Register Blocks Development | Affected Modules | Matrix Implementation Impact | Notes |
|---|---|---|---|---|---|---|---|
| AR-001 | ERPNext Platform Version (15/16 conflict) | Resolved | Critical | No | Cross-cutting — every ERPNext-facing Native/Extended classification in this matrix | Resolved — version-selection gate removed; technical revalidation pending (cross-cutting) | AR-001 is Resolved: Project Owner selected Option A, reaffirming the Accepted ADR-001-ERPNext-Framework decision. ERPNext v16, with the corresponding Frappe v16 major version, is the governed target — see Section 5. This matrix does not authorize implementation on the strength of this disposition alone; every ERPNext-dependency entry in Section 7 remains conditionally assessable only and requires technical revalidation against the governed v16 target, a Published implementation specification, and a separate scoped implementation-authorization decision before any coding may begin. |
| AR-002 | Multi-Tenant Strategy Alignment | Resolved | Critical | No | Configuration Studio (Tenant Override), Plugin Architecture (tenant-scoped config) | Resolved — governed topology established; Tenant Override implementation remains pending Publication and separate authorization | AR-002 is Resolved: Project Owner selected Option A, establishing one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company (Accepted ADR-015, Version 1.0; `../blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0). This matrix does not treat this disposition as implementation authorization: Blueprint 25 remains unpublished, a separate scoped implementation-authorization decision remains required, and Tenant Override and tenant-scoped plugin configuration remain undesigned, unimplemented concerns. Central customer identity, cross-tenant analytics, and Tenant Override implementation are explicitly deferred pending separate future governance and are not Job Card Tier A dependencies; Job Card Tier A is Company-scoped inside an isolated Tenant site, with site identity implicit and no separate Tenant field required by the current architecture. |
| AR-003 | Naming Registry Alignment | Open | High | Partial | AI Assistant; Approval Record naming (non-blocking); "Print Specification" (non-blocking, not modeled) | Partial/scoped delay | Blocks only areas still lacking approved terminology, principally AI Assistant's own definitional existence. |
| AR-004 | Machine Domain Ownership | Open | High | Partial | Machine Scheduling (direct), Job Cards (convergent) | Direct blocker for Machine Scheduling; convergent blocker for Job Cards | Blocks Machine and Machine Profile implementation directly; see Section 9's definition of convergent blocking for its effect on Job Cards. |
| AR-005 | Quotation Strategy | Open | High | Partial | Estimation (direct), Sales (transitive, standard path), Production Planning and Job Cards (transitive) | Direct blocker for Estimation; transitive delay for downstream modules | Affects the Estimation/Sales boundary and the final Quotation strategy. |
| AR-006 | Item vs. Material/Product Template Mapping | Open | High | Partial | Inventory (direct), Estimation (direct, Product Template), Purchasing/Procurement (transitive), Job Cards (convergent) | Direct blocker for Inventory and Estimation; convergent blocker for Job Cards | Blocks Inventory and Estimation master data alignment directly; see Section 14 for its wider blast radius. |
| AR-007 | Purchasing vs. Procurement Module Naming | Open | Medium | No | Purchasing/Procurement | No practical implementation block | Terminology-only per the current register. |
| AR-008 | Dispatch vs. Delivery Terminology | Open | Medium | No | Dispatch/Delivery | No practical implementation block | Terminology-only per the current register. |
| AR-009 | Quality Module Status | Open | Medium | No | Job Cards (Quality Check Record) | No practical implementation block | Terminology/scope-only per the current register; Quality Check Record proceeds as a Job Cards sub-feature today. |
| AR-010 | BOM Necessity for Estimation | Open | Medium | Partial | Estimation | Partial/scoped delay | Affects the Estimation Engine's internal calculation approach, not the existence of core entities. |
| AR-011 | CRM Enquiry vs. Opportunity Mapping | Open | Low | No | CRM | Partial/scoped delay | Delays only Enquiry-related CRM behavior; Customer proceeds unaffected. |

No item in this table has been resolved, modified, or reclassified beyond its current status in the Architecture Review Register.

---

## 14. Dependency Risks

- **Job Cards convergence risk.** Because Job Cards is where AR-004, AR-005, and AR-006 all converge, any single one of these resolving late has an outsized effect on the entire Production capability (Production Planning, Job Cards, and Machine Scheduling together), disproportionate to what a simple count of "one AR item per entity" would suggest.
- **AR-001 technical-revalidation risk.** Although AR-001 confirmed ERPNext v16 and Frappe v16 as the governed target, capability-level Fit, Gap, DocType, hook, event, permission, deployment, and integration assumptions throughout this matrix — including foundation-layer and ERPNext-dependency entries in Section 7, not just Estimation/Production-adjacent ones — must still be revalidated against the governed target before they are used as implementation specifications. Environmental minor or patch versions observed in local tooling must not be allowed to become architecture policy by accident; reconciliation of Docker, CI, deployment, and environment documentation against the governed target remains outstanding. Implementation remains unauthorized regardless of this disposition.
- **Estimation's hidden master-data dependency.** Modeling Estimation as dependent only on CRM (matching the customer-journey order) rather than also on Inventory and Production master data (matching the actual implementation order) risks a sequencing mistake that surfaces late, when Estimation's Engine is built but cannot compute a real price.
- **Configuration dependency overstatement.** Treating Configuration Studio as one monolithic prerequisite risks unnecessarily delaying every module until the full designer suite exists, when most designers have a usable native fallback and only three artifacts (Feature Flag, Automation Rule, Module Definition) are genuinely hard prerequisites.
- **AR-006 blast radius.** AR-006 sits underneath Inventory, Estimation, and Purchasing simultaneously; its "High" priority classification in the register, read in isolation, may understate how many modules are actually waiting on it.
- **Treating transitive delays as direct blockers.** Modules such as Dispatch, Accounts, and GST, which carry no Architecture Review item of their own, risk being incorrectly deprioritized or flagged as "blocked" if their transitive relationship to Job Cards is not kept distinct from a genuine direct blocker or from Job Cards' own convergent blocking.
- **Plugin over-engineering too early.** Building out Plugin Architecture's full Port/Adapter surface for categories with no near-term business need (Identity, Industrial, Analytics/BI) ahead of any actual requirement risks speculative investment the degrade-gracefully principle was specifically designed to avoid requiring.

---

## 15. Usage Guidance

This matrix informs future implementation planning; it does not itself define implementation order, sprint sequencing, or delivery dates. A future Development Roadmap or Project Execution Plan should treat this document as an input constraint — respecting direct blockers, distinguishing them from transitive delays, and exploiting the parallel implementation opportunities identified in Section 10 — rather than restating or re-deriving these dependencies independently. Where an Architecture Review item referenced here changes status, this matrix should be revisited for the specific modules that item affects, per Section 13, rather than assumed current indefinitely.

---

# Related Documents

- [00_Implementation_Index.md](00_Implementation_Index.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md)
- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md)
- [../architecture/Plugin_Architecture.md](../architecture/Plugin_Architecture.md)
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md)
- [../configuration/Configuration_Studio_Architecture.md](../configuration/Configuration_Studio_Architecture.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [02_Module_Implementation_Order.md](02_Module_Implementation_Order.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-25 | Initial | Initial Module Dependency Matrix, formalizing the prior implementation dependency analysis. Covers 19 modules/contexts across 8 dependency categories, preserves the corrected Estimation → Sales direction and the resolved Reporting/Configuration Studio ownership clarification, and classifies 10 Architecture Review items as direct blockers, partial/scoped delays, or no practical implementation block, without resolving any of them. No DocType mapping changed. |
| 0.2 | 2026-07-25 | Correction | Targeted correction pass: removed all references to a non-existent Architecture Review item; added AR-001 (ERPNext Platform Version) to the Architecture Review Dependency Summary with its official register status preserved verbatim; separated register-official fields from matrix-specific interpretation into distinct columns for every AR item; split "Production / Job Card" into three distinct modules — Production Planning, Job Cards, and Machine Scheduling — per ADR-014; introduced and applied "convergent blocking" as a defined third classification alongside direct blocker and transitive delay, applied specifically to Job Cards; corrected Quality Check Record's AR-009 exposure to be stated consistently across all sections; replaced "dependency-free foundation" wording with "near-foundational" language clarifying that HR and Warehouse depend on Administration; replaced "approved implementation dependency review" wording with "prior implementation dependency analysis" / "reviewed dependency conclusions," since no formal approval-record artifact exists; added GST and Analytics as explicitly scoped modules; softened the Configuration Studio "no rework" claim to acknowledge that migrating native-mechanism configuration into managed Configuration Studio definitions may require governance, validation, and conversion work. No Architecture Review item resolved. No DocType mapping changed. No roadmap, sprint plan, or timeline introduced. |
| 0.3 | 2026-07-25 | Correction | Final targeted correction pass. (1) Reconciled the Production dependency direction to match `09_PrintOS_Modules.md`: Job Cards own the production work-unit contract; Machine Scheduling depends on the Job Card contract + Machine Profile; Production Planning consumes Job Cards + Machine Scheduling and produces Scheduled Job Cards. Production Planning no longer appears as a prerequisite or creator of the Job Card concept. Introduced an explicit tier A (work-unit contract / "Job Card scaffolding") vs. tier B (operationally schedulable Job Card) distinction to remove the apparent circular dependency, and defined "Job Card scaffolding." Corrected the Core Business Dependency Spine and Critical Implementation Path accordingly. (2) Normalized Job Cards blocking language: AR-004 and AR-006 are now "convergent inputs"/"convergent exposure" to tier B, not "direct blockers" of Job Cards; convergent blocking is defined once and applied consistently. (3) Tightened AR-001 foundation language to conditional wording (conditionally assessable, dependency-light but version-sensitive), stated that ERPNext v16 is a documented working assumption not a resolved decision, affirmed AR-001 remains Critical/development-blocking, and removed language authorizing version-sensitive implementation ahead of AR-001. (4) Removed remaining sequencing language ("Administration first, then…", "buildable immediately", "buildable now") in favor of dependency-relationship wording. (5) Replaced residual "reviewed dependency conclusions" with neutral "prior dependency analysis" / "dependency review findings." No Architecture Review item resolved. No DocType mapping changed. No roadmap, sprint plan, date, or delivery-sequencing language introduced. |
| 0.4 | 2026-07-27 | AR-001 Disposition Synchronization | Synchronized the Matrix with the formal Project Owner disposition of AR-001 (Option A, recorded in `Architecture_Review_Register.md`). Recorded ERPNext v16, with the corresponding Frappe v16 major version, as the governed target, reaffirming the Accepted ADR-001-ERPNext-Framework decision; simultaneous v15/v16 support noted as out of scope; minor/patch selection noted as centrally governed. Updated AR-001's official-field reproduction in Section 13 from Open/Critical/Yes to Resolved/Critical/No, with Priority preserved at its historical Critical value. Replaced "documented working assumption / not a resolved architecture decision / Critical and development-blocking" wording in Section 5 and the three repeated cross-cutting references (Section 7 CRM and Purchasing/Procurement entries, Section 10) with governed-target and pending-technical-revalidation wording. Reframed the Section 14 AR-001 risk entry from a hypothetical "resolves to a different version than assumed" framing to an explicit technical-revalidation risk, adding that environmental minor/patch versions must not become architecture policy by accident and that Docker/CI/deployment/environment reconciliation remains outstanding. No dependency direction, dependency type, blocker classification (beyond removing AR-001 as a current blocker), scope classification, readiness classification, Job Card Tier A/B semantics, Production Planning/Job Cards/Machine Scheduling relationship, or Configuration Studio/Plugin Architecture treatment was changed. No technical Fit, Gap, or DocType revalidation was performed or claimed. AR-002 through AR-011 remained unchanged (AR-002 remains Open/Critical/Yes). No implementation authorization was granted; every implementation scope remains unauthorized. |
| 0.5 | 2026-07-28 | AR-002 Disposition Synchronization | Synchronized the Matrix with the formal Project Owner disposition of AR-002 (Option A, recorded in `Architecture_Review_Register.md`, Version 0.4). Updated AR-002's official-field reproduction in Section 13 from Open/Critical/Yes to Resolved/Critical/No. Recorded the governed topology — one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company — per Accepted ADR-015-Tenant-Company-Multi-Tenancy-Model (Version 1.0) and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published) in Section 7 (Configuration Studio, Plugin Architecture entries), Section 9 (Direct Blockers), Section 11 (Configuration Studio Dependency Treatment), and Section 13. Removed AR-002 as an unresolved topology gate: stale "Pending AR-002" / open-uncertainty wording replaced with wording recording the topology and Tenant/Company distinction as resolved, while explicitly preserving Tenant Override and tenant-scoped plugin configuration as still-blocked pending Blueprint 25's Publication and a separate scoped implementation-authorization decision. Recorded that Job Card Tier A is Company-scoped inside an isolated Tenant site, with site identity implicit and no separate Tenant field required, and that central customer identity, cross-tenant analytics, and Tenant Override implementation are explicitly deferred pending separate future governance rather than required Tier A dependencies. No dependency direction, dependency type, blocker classification, scope classification, readiness classification, Job Card Tier A/B semantics, Production Planning/Job Cards/Machine Scheduling relationship, or workstream/authorization value was changed beyond recording AR-002 as Resolved. AR-001 remains Resolved/Critical/No; AR-003 through AR-011 remained unchanged. No implementation authorization was granted; every implementation scope remains unauthorized; Full Architecture Freeze remains unachieved. |
| 0.6 | 2026-07-31 | Artwork Production Authority Synchronization | Synchronized the Artwork and Job Cards dependency treatment following the Project Owner's production-capable Artwork track selection (2026-07-30) and approval of the Artwork design defaults (2026-07-31), which authorized documentation-only Artwork authority design work. Recorded that the **Artwork bounded context depends on the Submitted Sales Order from Sales**; that **Production/Job Card depends on the Approved Production Artwork Set from Artwork**, replacing the previous generic "Approved Artwork" prerequisite in both the Module Dependency Summary and the Artwork module notes; the **dependency direction Sales → Artwork → Production**; and that the **reverse dependency Artwork → Job Card is prohibited**. Reaffirmed that the Configuration Studio **Approval Designer remains optional and deferrable**, requiring a default governed Artwork approval mechanism to exist without it. Recorded that **Job Card Tier A production-capable Publication depends on the Artwork authority specification and its required runtime validation**; that the **Artwork implementation workstream remains Not Authorized**; that the **Job Card implementation workstream remains Not Authorized**; that documentation-only Artwork authority design was approved on 2026-07-31; and that the exact `printos_core` module paths for Artwork and Job Card remain unresolved pre-Publication decisions. Cross-referenced the new `../blueprint/18_Artwork_Management.md` (Draft 0.1) and `../database/Artwork_Authority_DocType_Specification.md` (Draft 0.1). Status remains Draft; **no Architecture Review Register item was altered**; no dependency classification model, foundation layer or other module's prerequisites changed; no Job Card Tier A document, ADR, Architecture Freeze, Development Roadmap, Naming Registry or standards document was modified; no implementation was authorized. |
| 0.7 | 2026-07-31 | Architecture and Business Review Correction | Applied the Project Owner-approved corrections (2026-07-31) to the formal Architecture Review and Business Review (both Corrections Required, blocking findings ART-ARCH-F1 and ART-BIZ-F1) of the Artwork production-authority documentation package. Corrected the Artwork module's Sales prerequisite wording to **Submitted Sales Order** (aligning the Module Dependency Summary table with the previously correct detailed Artwork section) and the Production Planning module's Artwork prerequisite wording to **Approved Production Artwork Set** (aligning it with the already-corrected Job Cards row). Preserved the dependency direction Sales → Artwork → Production, the prohibited reverse dependency Artwork → Job Card, the optional/deferrable status of the Configuration Studio Approval Designer, and that documentation-only design authorization does not grant implementation authorization. Recorded that **Customer Approval Evidence is internal to Artwork and does not create a Production-to-Proof or Production-to-Approval-Record dependency** — it is consumed only inside the Artwork context's own Revision-approval operation. Updated the Artwork module notes to record the corrected review status of both referenced Artwork documents (Draft 0.2 each; Architecture Review and Business Review both Corrections Required; corrections applied; targeted re-review pending) and that Job Card Tier A production-capable Publication depends on the Artwork specification passing that targeted re-review. Status remains Draft; **no Architecture Review Register item was altered**; no dependency classification model, foundation layer or other module's prerequisites changed; Artwork and Job Card implementation workstreams remain Not Authorized; no Job Card Tier A document, ADR, Architecture Freeze, Development Roadmap, Naming Registry or standards document was modified; no implementation was authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code, sprint plan, or roadmap included
- [ ] No dates assigned
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] No Architecture Review Register item resolved or created
- [ ] Consistent with Canonical Domain Model, ERPNext DocType Mapping, and Configuration Studio Architecture
- [ ] Reviewed by Project Owner
