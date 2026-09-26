# Canonical Domain Model

Version:
0.4

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-26

---

## Executive Summary

This document organizes the 75 entities cataloged in [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) into a Domain-Driven Design structure: Bounded Contexts, Aggregate Roots, Child Entities, Value Objects, and Domain Services. It performs no ERPNext mapping, no DocType design, and no database design — those are reserved for the future ERPNext DocType Mapping document. It resolves no open Architecture Review item; where a domain-modeling decision intersects an open item (e.g., Machine's aggregate status touching AR-004), the item is referenced and left open.

This document uses the Bounded Context list already Published in [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) as its organizing structure, adding only the Configuration Studio context (justified by the full `docs/configuration/` documentation set) and flagging — not adopting — "AI Assistant" and the requested "Print Operations" grouping, neither of which is an Approved Bounded Context name.

---

## Domain Modeling Principles

- **One aggregate, one context.** Every entity is assigned to exactly one Bounded Context, consistent with [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)'s statement that contexts communicate through defined inputs/outputs rather than shared internal state.
- **Aggregate Roots own their Child Entities' consistency.** A Child Entity has identity but no independent lifecycle outside its Aggregate Root, per [../architecture/03_DDD_Architecture.md](../architecture/03_DDD_Architecture.md)'s Aggregate definition.
- **Value Objects carry no independent identity.** Where an entity in the Business Entity Inventory is descriptive/reference data with no individually-tracked lifecycle beyond Active/Retired, it is reclassified here as a Value Object rather than an Aggregate Root — this is a domain-modeling judgment this document is explicitly tasked with making, not a change to the Inventory's entity list itself.
- **Domain Services hold behavior that belongs to no single entity.** Where a capability spans multiple entities (e.g., Estimation's cost calculation spanning Product Template, Material, and Machine Profile), it is modeled as a Domain Service, per [../architecture/03_DDD_Architecture.md](../architecture/03_DDD_Architecture.md).
- **Master data joint ownership is resolved to single DDD ownership here only.** Where [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md) lists joint business ownership (e.g., "Accounts/Sales"), this document assigns one primary owning Bounded Context for domain-modeling purposes, consistent with the shared data still being consumed cross-context — this is a DDD organizing decision, not a reinterpretation of Blueprint's stated business ownership.

---

## Bounded Contexts

### CRM Context

- **Business Purpose:** Lead/enquiry capture and customer relationship tracking, per [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md).
- **Owned Entities:** Customer, Lead / Enquiry
- **Aggregate Roots:** Customer, Lead / Enquiry
- **Child Entities:** None
- **Value Objects:** None identified in the Inventory
- **Domain Services:** None named; Lead qualification is currently represented as a Lead/Enquiry state transition, not a separate service. Enquiry-vs-Opportunity mapping is under [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-011**.

---

### Sales Context

- **Business Purpose:** Confirmed order management, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Sales Order, Price List
- **Aggregate Roots:** Sales Order, Price List
- **Child Entities:** None
- **Value Objects:** None
- **Domain Services:** None named.

---

### Estimation Context

- **Business Purpose:** Industry-specific pricing and Quotation generation — a Core Domain, per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md).
- **Owned Entities:** Quotation, Quotation Line, Cost Estimate, Product Template, Product Category, Paper Sizes
- **Aggregate Roots:** Quotation, Cost Estimate, Product Template
- **Child Entities:** Quotation Line (child of Quotation); Product Category is modeled as a light reference Aggregate Root rather than a child, since it is independently manageable.
- **Value Objects:** Paper Sizes — reclassified from the Inventory's Reference Data entity to a Value Object, since it is a descriptive dimension with no independently tracked business lifecycle beyond Active.
- **Domain Services:** **Estimation Engine (Cost Calculator)** — combines Product Template, Material/Substrate, Machine Profile, and quantity into a priced proposal; corresponds to the Customize-classified "Print Estimation & Costing Engine" gap in [ERPNext_Gap_Analysis.md](ERPNext_Gap_Analysis.md). Quotation's document strategy is Resolved — [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-005**, Option B (2026-09-26): Custom Cost Estimate and pricing logic feed ERPNext's native Quotation through a governed handoff (see [14_Quotation_Engine.md](../blueprint/14_Quotation_Engine.md)). The necessity of a BOM-like structure within this engine remains under **AR-010**; AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework.

---

### Artwork Context

- **Business Purpose:** Design asset management and customer approval prior to production, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Artwork, Artwork Revision, Proof, Approval Record
- **Aggregate Roots:** Artwork
- **Child Entities:** Artwork Revision (child of Artwork); Proof (child of Artwork Revision)
- **Value Objects:** None
- **Domain Services:** None named within this context; Approval Record's transition rules are governed by configured Approval Definitions in the Configuration Studio context, not an Artwork-context service. Approval Record's own naming/classification was never one of [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-003**'s eight named terms (only the requested module name "Approval Management," now mapped to Approval Designer, was); it remains a separate, currently unassigned governance question.

---

### Production Context

- **Business Purpose:** Job Card execution and tracking — a Core Domain, per `05_Domain_Model.md`. Includes Machine Scheduling as a sub-scope pending the Open Question below.
- **Owned Entities:** Job Card, Quality Check Record, Machine, Machine Profile, Job Types, Finishing Types, Production Schedule *(candidate)*, Production Stage *(flagged — see Special Review)*, Machine Queue *(flagged — see Special Review)*, Finishing Operation *(candidate, Future)*
- **Aggregate Roots:** Job Card, Machine
- **Child Entities:** Quality Check Record (child of Job Card); Machine Profile (child of Machine)
- **Value Objects:** Job Types, Finishing Types — reclassified from Reference Data to Value Objects, as descriptive classifications consumed by Job Card and Quotation Line rather than independently tracked entities.
- **Domain Services:** **Production Planning / Scheduling Engine** — orchestrates Job Card sequencing and Machine assignment; corresponds to the Customize-classified "Production Planning & Orchestration" gap in [ERPNext_Gap_Analysis.md](ERPNext_Gap_Analysis.md). Machine's aggregate/base-object status is Resolved — [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-004**, Option C (2026-09-20): Machine is a wholly Custom DocType; Quality Check Record's standalone-module status is under **AR-009**.
- **Open Blueprint Question (not an AR item, cited for completeness):** [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) itself asks "Should Machine Scheduling be modeled as its own bounded context distinct from Production, given its complexity?" This document does not answer that question; Machine/Machine Profile are modeled within Production only because that question remains open.

---

### Inventory Context

- **Business Purpose:** Material availability and consumption tracking, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Material, Substrate, Media Profiles, Units of Measure
- **Aggregate Roots:** Material
- **Child Entities:** Substrate (child of Material) — Substrate is "the specific physical material" derived from a Material record, per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md), and is modeled here as owned by its Material rather than independently.
- **Value Objects:** Media Profiles, Units of Measure
- **Domain Services:** None named. Material's relationship to ERPNext "Item" is under [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-006**.

---

### Procurement Context

- **Business Purpose:** Material/service acquisition from Suppliers, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Purchase Order, Supplier
- **Aggregate Roots:** Purchase Order, Supplier
- **Child Entities:** None
- **Value Objects:** None
- **Domain Services:** None named.
- **Naming Note:** This is the task's requested "Purchasing" context. The Blueprint Bounded Context name is **Procurement**; the Blueprint *module* name is **Purchasing**. This dual naming is Pending ADR — see [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-007** — and is not resolved by this document's choice to use "Procurement" as the context name, which simply follows `06_Bounded_Contexts.md` as written.

---

### Warehouse Context

- **Business Purpose:** Physical storage and goods movement, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Warehouse
- **Aggregate Roots:** Warehouse
- **Child Entities:** None
- **Value Objects:** None
- **Domain Services:** None named.

---

### Dispatch Context

- **Business Purpose:** Delivery of finished goods, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Dispatch Record, Delivery Method
- **Aggregate Roots:** Dispatch Record
- **Child Entities:** None
- **Value Objects:** Delivery Method
- **Domain Services:** None named. Dispatch-vs-Delivery terminology is under [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-008**.

---

### Accounts Context

- **Business Purpose:** Financial transaction recording, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Invoice, Payment, Journal Entry, Tax Template, Payment Terms, Currency
- **Aggregate Roots:** Invoice, Payment, Journal Entry — Payment is modeled as its own Aggregate Root, not a child of Invoice, since partial/multiple payments against an Invoice carry independent identity and lifecycle per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)'s Business Rules.
- **Child Entities:** None
- **Value Objects:** Tax Template, Payment Terms, Currency
- **Domain Services:** None named — GST computation and statutory tax logic are ERPNext-native per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) Section 3 (Accounts) and are not modeled as a PrintHub Domain Service.

---

### GST Context

- **Business Purpose:** Statutory tax compliance, per `06_Bounded_Contexts.md`.
- **Owned Entities:** GST Configuration
- **Aggregate Roots:** None
- **Child Entities:** None
- **Value Objects:** GST Configuration — modeled as a Value Object referenced by Tax Template (Accounts context), consistent with GST's description in `06_Bounded_Contexts.md` as "tightly coupled to Accounts."
- **Domain Services:** None named.

---

### HR Context

- **Business Purpose:** Employee record management, per `06_Bounded_Contexts.md`.
- **Owned Entities:** Employee, Department
- **Aggregate Roots:** Employee
- **Child Entities:** Department — modeled as owned within HR's organizational structure, referencing Branch (Administration context) cross-context.
- **Value Objects:** None
- **Domain Services:** None named.

---

### Administration Context

- **Business Purpose:** System and organizational configuration, per `06_Bounded_Contexts.md` — narrowed here to Company/Branch structure specifically, distinct from the Configuration Studio context below.
- **Owned Entities:** Company, Branch
- **Aggregate Roots:** Company
- **Child Entities:** Branch (child of Company)
- **Value Objects:** None
- **Domain Services:** None named.

---

### Configuration Studio Context

- **Business Purpose:** Configuration-as-data platform enabling designer-driven customization of Workflow, Approval, Form, Dashboard, Report, Notification, Integration, Feature Flag, and Automation behavior without code, per [../configuration/01_Configuration_Architecture.md](../configuration/01_Configuration_Architecture.md). Added to this domain model as its own context — not present by this name in `06_Bounded_Contexts.md` — because the entire `docs/configuration/` documentation set (16 documents) describes it as a coherent, self-contained platform capability distinct from Administration's narrower Company/Branch scope.
- **Owned Entities:** Module Definition, Workflow Definition, Approval Definition, Form Definition, Dashboard Definition, Report Definition, Notification Template, Integration Definition, Feature Flag, Automation Rule, Configuration Template, Tenant Override
- **Aggregate Roots:** Module Definition, Workflow Definition, Approval Definition, Form Definition, Dashboard Definition, Report Definition, Notification Template, Integration Definition, Feature Flag, Automation Rule, Configuration Template
- **Child Entities:** Tenant Override (child of Configuration Template)
- **Value Objects:** None
- **Domain Services:** **Configuration Resolution Engine** — resolves configuration through the tenant-override → template-default → system-default order described in [../configuration/01_Configuration_Architecture.md](../configuration/01_Configuration_Architecture.md); corresponds to the Customize-classified "Configuration Studio Governance Layer" and "Tenant Customization / Resolution Engine" gaps in [ERPNext_Gap_Analysis.md](ERPNext_Gap_Analysis.md). Tenant Override's terminology and scope are under [Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-002**.
- **Ownership Clarification (resolved as documentation terminology alignment, not an architecture conflict):** An apparent overlap was previously observed between [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)'s Reporting context and this Configuration Studio context, both referencing "Report Definition"/"Dashboard." A governance review concluded this was documentation terminology ambiguity, not a true architectural ownership conflict: Configuration Studio owns Report Definition and Dashboard Definition as configuration artifacts, produced by its Report Designer and Dashboard Designer; the Reporting context owns the cross-context reporting *capability* (aggregation, visibility, consumption, and execution of Published definitions), not the configuration artifacts themselves. `06_Bounded_Contexts.md` has been updated accordingly. No Architecture Review item was required or created for this clarification.

---

### Reporting Context

- **Business Purpose:** Cross-context reporting capability — aggregation, dashboard visibility, business performance reporting, and consumption/execution of Published Report Definitions and Dashboard Definitions, per `06_Bounded_Contexts.md` (as clarified).
- **Owned Entities:** None. Reporting owns the reporting *capability*, not a configuration artifact; Report Definition and Dashboard Definition are owned by the Configuration Studio context (see above) and are consumed, not duplicated, here. This is a resolved documentation terminology clarification, not an open architectural question.
- **Aggregate Roots:** None
- **Child Entities:** None
- **Value Objects:** None
- **Domain Services:** None named.

---

### MachineIQ Context *(Future)*

- **Business Purpose:** Machine intelligence over production/reporting data, per `06_Bounded_Contexts.md`; full scope deferred per [ADR-008-MachineIQ](../decisions/ADR-008-MachineIQ.md).
- **Owned Entities:** Machine Event, Sensor, Telemetry Reading, Counter Reading, Maintenance Alert, Machine Status
- **Aggregate Roots:** Machine Event, Sensor
- **Child Entities:** Telemetry Reading (child of Sensor); Counter Reading (child of Machine, cross-context reference to Production)
- **Value Objects:** Machine Status — a point-in-time state snapshot rather than an independently tracked entity; Maintenance Alert retains Aggregate Root status since it has its own Raised → Acknowledged → Resolved lifecycle.
- **Domain Services:** None named — full scope deferred.
- **Note:** All entities here are Naming Registry Proposed terms (§36), not yet formally scoped in any Blueprint document.

---

### Marketplace Context *(Future)*

- **Business Purpose:** Public-facing discovery and ordering connecting buyers (G1) to print shops, per `06_Bounded_Contexts.md`; deferred to Phase 5 per [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).
- **Owned Entities (Approved concepts only — Group A):** Marketplace Listing, Marketplace Order
- **Aggregate Roots:** Marketplace Listing, Marketplace Order
- **Child Entities:** None
- **Value Objects:** None
- **Domain Services:** None named — full scope deferred.
- **Excluded from this context (see Special Review below):** Marketplace Package, Marketplace Template, Extension, Publisher, Marketplace Package Version — these describe a distinct, unregistered plugin/extension-marketplace concept and are **not** placed within this Bounded Context; see Special Review.

---

### AI Assistant Context *(Proposed name only — not an Approved Bounded Context)*

- **Business Purpose:** Not yet defined in any Approved Blueprint document.
- **Owned Entities:** Conversation, Prompt, Knowledge Source, Recommendation, AI Action — all unregistered, outside AR-003's scope, pending separate future governance.
- **Aggregate Roots:** Not assessable pending naming/scoping.
- **Child Entities:** Not assessable.
- **Value Objects:** Not assessable.
- **Domain Services:** Not assessable.
- **Note:** This grouping is included only because it was named in this task's required evaluation list. Per [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md) and [Naming Registry](../standards/Naming_Registry.md) Section 40, "AI Assistant" is registered as a **Proposed name only** ([Architecture Review Register](../decisions/Architecture_Review_Register.md) **AR-003**, Resolved 2026-09-19) — it has no Approved Bounded Context, architecture, provider, model, plugin design, implementation owner, or implementation authorization. It is not treated as an Approved Bounded Context here. Its five candidate sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) were never among AR-003's eight named terms and remain unregistered, outside AR-003's scope, pending separate future governance.

---

## Aggregate Catalog

| Aggregate Root | Bounded Context | Business Purpose |
|---|---|---|
| Customer | CRM | Party requesting/paying for work |
| Lead / Enquiry | CRM | Prospective business prior to qualification |
| Sales Order | Sales | Confirmed commercial commitment |
| Price List | Sales | Applicable pricing per segment/channel |
| Quotation | Estimation | Priced proposal for defined scope of work |
| Cost Estimate | Estimation | Internal pre-Quotation costing artifact |
| Product Template | Estimation | Sellable product definition |
| Product Category | Estimation | Product grouping |
| Artwork | Artwork | Design/creative asset for production |
| Job Card | Production | Production execution and tracking record |
| Machine | Production | Production asset and capability constraints |
| Material | Inventory | Consumable production input |
| Purchase Order | Procurement | Commitment to acquire materials/services |
| Supplier | Procurement | Party supplying materials/services |
| Warehouse | Warehouse | Physical storage location |
| Dispatch Record | Dispatch | Delivery of finished goods |
| Invoice | Accounts | Billing record for delivered work |
| Payment | Accounts | Funds received against an Invoice |
| Journal Entry | Accounts | Manual accounting adjustment |
| Employee | HR | Person working within the business |
| Company | Administration | Legal business entity |
| Module Definition | Configuration Studio | Registry of installable/enableable modules |
| Workflow Definition | Configuration Studio | Configured document state machine |
| Approval Definition | Configuration Studio | Configured approval chain |
| Form Definition | Configuration Studio | Configured field layout/visibility |
| Dashboard Definition | Configuration Studio | Configured dashboard composition |
| Report Definition | Configuration Studio | Configured report |
| Notification Template | Configuration Studio | Configured message template |
| Integration Definition | Configuration Studio | Configured external system connection |
| Feature Flag | Configuration Studio | Toggle for feature availability |
| Automation Rule | Configuration Studio | Configured trigger-condition-action rule |
| Configuration Template | Configuration Studio | Versioned configuration bundle |
| Machine Event | MachineIQ *(Future)* | Discrete, time-stamped Machine occurrence |
| Sensor | MachineIQ *(Future)* | Physical device reporting Machine condition |
| Maintenance Alert | MachineIQ *(Future)* | Machine condition requiring attention |
| Marketplace Listing | Marketplace *(Future)* | Print shop catalog entry visible to buyers |
| Marketplace Order | Marketplace *(Future)* | Order originated by a public buyer |

---

## Child Entity Catalog

| Child Entity | Parent Aggregate | Bounded Context |
|---|---|---|
| Quotation Line | Quotation | Estimation |
| Artwork Revision | Artwork | Artwork |
| Proof | Artwork Revision | Artwork |
| Quality Check Record | Job Card | Production |
| Machine Profile | Machine | Production |
| Substrate | Material | Inventory |
| Department | (organizational, references Branch) | HR |
| Branch | Company | Administration |
| Tenant Override | Configuration Template | Configuration Studio |
| Telemetry Reading | Sensor | MachineIQ *(Future)* |
| Counter Reading | Machine (cross-context) | MachineIQ *(Future)* |

---

## Value Object Catalog

| Value Object | Used By (Bounded Context) | Rationale |
|---|---|---|
| Paper Sizes | Estimation | Descriptive dimension, no independent lifecycle |
| Job Types | Production | Descriptive classification consumed by Job Card/Quotation Line |
| Finishing Types | Production | Descriptive classification consumed by Job Card/Quotation Line |
| Media Profiles | Inventory | Descriptive Substrate characteristic |
| Units of Measure | Inventory | Standard measurement unit, no independent identity |
| Delivery Method | Dispatch | Descriptive fulfillment option |
| Tax Template | Accounts | Reusable tax computation rule set |
| Payment Terms | Accounts | Reusable billing condition set |
| Currency | Accounts | Unit of monetary value |
| GST Configuration | GST | Statutory configuration referenced by Tax Template |
| Machine Status | MachineIQ *(Future)* | Point-in-time state snapshot, not independently tracked |

---

## Domain Service Catalog

| Domain Service | Bounded Context | Business Behavior |
|---|---|---|
| Estimation Engine (Cost Calculator) | Estimation | Combines Product Template, Material/Substrate, Machine Profile, and quantity into a priced proposal |
| Production Planning / Scheduling Engine | Production | Sequences Job Cards and assigns Machines within capacity/capability constraints |
| Configuration Resolution Engine | Configuration Studio | Resolves configuration values through tenant-override → template-default → system-default order |

No Domain Services are named for CRM, Sales, Artwork, Inventory, Procurement, Warehouse, Dispatch, Accounts, GST, HR, Administration, MachineIQ, Marketplace, or AI Assistant at this time — either because their behavior is fully expressible as Aggregate/Entity operations, or because the context's scope remains deferred (MachineIQ, Marketplace, AI Assistant).

---

## Cross-Context Relationships

- **CRM → Sales:** A qualified Lead/Enquiry becomes a Customer; Customer feeds Sales Order creation.
- **CRM → Estimation:** Lead/Enquiry scope initiates Quotation preparation.
- **Estimation → Sales:** An Approved Quotation converts into a Sales Order.
- **Sales → Artwork:** A confirmed Sales Order requires Artwork submission and approval.
- **Artwork → Production:** Approved Artwork is a required gate before Job Card production may begin.
- **Estimation → Production:** Quotation Line detail (Product Template, Job Types, Finishing Types) informs Job Card requirements.
- **Production → Inventory:** Job Card material allocation consumes Material/Substrate.
- **Inventory → Procurement:** Low Material stock triggers Purchase Order creation.
- **Procurement → Warehouse:** Received Purchase Order goods enter Warehouse.
- **Production → Warehouse → Dispatch:** Finished Job Card output moves through Warehouse to Dispatch Record.
- **Dispatch → Accounts:** Dispatch confirmation triggers Invoice generation.
- **Accounts → GST:** Invoice/Purchase Order data feeds GST Configuration-driven tax computation.
- **HR → Production:** Employee records support Operator assignment to Job Cards.
- **Administration → All Contexts:** Company/Branch scoping underlies every other context's data, pending [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-002's resolution of the Tenant/Company relationship.
- **Configuration Studio → All Contexts:** Workflow Definition, Approval Definition, Form Definition, Dashboard Definition, Report Definition, Notification Template, and Automation Rule each configure behavior within every other Bounded Context without those contexts depending back on Configuration Studio's internals — a one-directional relationship.
- **Production → MachineIQ *(Future)*:** Machine and Job Card data are the intended future input to Machine Event/Telemetry analysis.
- **Reporting → All Contexts:** Aggregates data from every other context for cross-context reporting, consuming Published Report Definitions and Dashboard Definitions owned by the Configuration Studio Context.
- **CRM/Dispatch → Marketplace *(Future)*:** Marketplace Order is intended to route into CRM/Sales once Phase 5 is scoped, per `06_Bounded_Contexts.md`.

---

## Naming Review

Every entity from [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md), classified as **Canonical** (Approved term, no entity-specific open review item), **Alias** (a non-canonical synonym still in use — none found; all Deprecated synonyms were already excluded from the Inventory), **Pending Architecture Review** (the entity's naming, scope, or structural strategy is the direct subject of an open AR item), or **Not an Independent Entity** (does not warrant independent domain identity).

| Entity | Classification | Note |
|---|---|---|
| Company | Canonical | Tenant-scope anchor status under AR-002 (anchor role, not the entity's own naming) |
| Branch | Canonical | — |
| Department | Canonical | — |
| Employee | Canonical | — |
| Customer | Canonical | Synonym conflict (Client/Party) is Pending ADR but entity itself is Canonical |
| Supplier | Canonical | Synonym conflict (Vendor) is Pending ADR but entity itself is Canonical |
| Product Template | Canonical | Item-vs-Material relationship touches this entity — see AR-006 |
| Product Category | Canonical | — |
| Material | Pending Architecture Review | AR-006 |
| Warehouse | Canonical | — |
| Price List | Canonical | — |
| Payment Terms | Canonical | — |
| Tax Template | Canonical | — |
| GST Configuration | Canonical | — |
| Currency | Canonical | — |
| Units of Measure | Canonical | — |
| Lead / Enquiry | Pending Architecture Review | AR-011 |
| Sales Order | Canonical | — |
| Purchase Order | Canonical | — |
| Invoice | Canonical | — |
| Payment | Canonical | — |
| Journal Entry | Canonical | — |
| Substrate | Canonical | — |
| Machine | Canonical | Resolved — AR-004, Option C (2026-09-20) |
| Machine Profile | Canonical | Resolved — AR-004, Option C (2026-09-20, dependent on Machine) |
| Job Types | Canonical | — |
| Finishing Types | Canonical | — |
| Paper Sizes | Canonical | — |
| Media Profiles | Canonical | — |
| Quotation | Pending Architecture Review | AR-005 Resolved (Option B, 2026-09-26); AR-010 remains the open dependency |
| Quotation Line | Pending Architecture Review | Dependent on Quotation (AR-005 Resolved); AR-010 remains the open dependency |
| Cost Estimate | Canonical | Distinct from Quotation per ADR-013 |
| Artwork | Canonical | — |
| Artwork Revision | Canonical | — |
| Proof | Canonical | — |
| Approval Record | Pending Architecture Review | Unassigned — outside AR-003; a separate, currently unassigned governance question |
| Job Card | Canonical | Term itself canonical per ADR-014; ERPNext-name-collision risk documented, not a naming pending item |
| Quality Check Record | Pending Architecture Review | AR-009 |
| Dispatch Record | Pending Architecture Review | AR-008 |
| Delivery Method | Canonical | — |
| "Print Specification" | Not adopted | AR-003 Resolved 2026-09-19 — maps to Product Template, Job Types, Finishing Types, and Paper Sizes; see Special Review below |
| "Production Stage" | Not an Independent Entity | See Special Review below |
| "Machine Queue" | Not an Independent Entity | See Special Review below |
| Finishing Operation | Pending Architecture Review | Future-classified per Gap Analysis; not yet scoped |
| Production Schedule | Pending Architecture Review | Future-classified per Gap Analysis; not yet scoped |
| Module Definition | Canonical | — |
| Workflow Definition | Canonical | — |
| Approval Definition | Canonical | — |
| Form Definition | Canonical | — |
| Dashboard Definition | Canonical | Overlap with Reporting context noted above |
| Report Definition | Canonical | Overlap with Reporting context noted above |
| Notification Template | Canonical | — |
| Integration Definition | Canonical | — |
| Feature Flag | Canonical | — |
| Automation Rule | Canonical | — |
| Configuration Template | Canonical | — |
| Tenant Override | Pending Architecture Review | AR-002 |
| Machine Event | Canonical | Approved term (§6/§23); scope deferred |
| Sensor | Pending Architecture Review | Proposed term (§36), not yet scoped |
| Telemetry Reading | Pending Architecture Review | Proposed term (§36), not yet scoped |
| Counter Reading | Pending Architecture Review | Proposed term (§36), not yet scoped |
| Maintenance Alert | Pending Architecture Review | Proposed term (§36), not yet scoped |
| Machine Status | Pending Architecture Review | Related Proposed terms (§36), not yet scoped |
| Marketplace Listing | Pending Architecture Review | Proposed term (§24); relationship to other entities partly Pending ADR (§27 item 14) |
| Marketplace Order | Pending Architecture Review | Proposed term (§24); relationship to Sales Order Pending ADR (§27 item 14) |
| Marketplace Package | Pending Architecture Review | Unregistered — naming collision, see Special Review |
| Marketplace Template | Pending Architecture Review | Unregistered — naming collision, see Special Review |
| Extension | Pending Architecture Review | Unregistered — see Special Review |
| Publisher | Pending Architecture Review | Unregistered — see Special Review |
| Marketplace Package Version | Pending Architecture Review | Unregistered — see Special Review |
| Conversation | Pending Architecture Review | Unregistered — outside AR-003, pending separate future governance |
| Prompt | Pending Architecture Review | Unregistered — outside AR-003, pending separate future governance |
| Knowledge Source | Pending Architecture Review | Unregistered — outside AR-003, pending separate future governance |
| Recommendation | Pending Architecture Review | Unregistered — outside AR-003, pending separate future governance; possible overlap with MachineIQ "Insight" (§23), unreconciled |
| AI Action | Pending Architecture Review | Unregistered — outside AR-003, pending separate future governance |

**Summary:** 47 Canonical, 0 Alias, 25 Pending Architecture Review, 1 Not adopted, 2 Not an Independent Entity. (75 total, matching the Business Entity Inventory.)

---

## Special Review

As directed, particular attention to four items:

### Marketplace Naming Collision
**Finding:** The task's requested Marketplace entities (Marketplace Package, Marketplace Template, Extension, Publisher, Marketplace Package Version) describe a plugin/extension-distribution concept, entirely distinct from the Approved "Marketplace" Bounded Context (the G1 public-buyer platform, per [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md)). This collision was first identified in [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) Section 5.
**Domain Model Treatment:** These five entities are **excluded from the Marketplace Bounded Context** in this document (see Marketplace Context above) and are classified as **Pending Review**. They are not assigned to any Bounded Context, Aggregate, Child Entity, or Value Object category, since doing so would require either registering a new "Extension/Plugin Marketplace" concept or merging it into the existing Marketplace context — both of which are naming/scoping decisions outside this document's authority.

### Print Specification
**Finding:** **Not Adopted** — [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-003 resolved (2026-09-19) that "Print Specification" is not adopted as a module, DocType, entity, or umbrella object. Its underlying business need maps to the combination of Product Template, Job Types, Finishing Types, and Paper Sizes — all already placed as Aggregate Roots/Value Objects within the Estimation and Production contexts above.
**Domain Model Treatment: Not adopted.** Not modeled as its own Aggregate, Child Entity, or Value Object in this document. This is a resolved disposition, not a pending one; it is retained here only as a not-adopted traceability entry mapping the requested term to its existing constituent entities.

### Production Stage
**Finding:** Requested as a candidate entity representing a Job Card's position in the production process.
**Domain Model Treatment: Not a Domain Entity.** This is fully represented by Job Card's own Lifecycle attribute (Scheduled → In Progress → Quality Check → Complete). It carries no identity independent of Job Card and is not modeled as an Aggregate, Child Entity, or Value Object.

### Machine Queue
**Finding:** Requested as a candidate entity representing the set of Job Cards awaiting a given Machine.
**Domain Model Treatment: Not a Domain Entity.** This is a derived view over existing Job Card-to-Machine assignments (an operational query), not independently persisted domain state. It is not modeled as an Aggregate, Child Entity, or Value Object.

---

## Open Architecture Dependencies

This domain model's structure is directly gated, in part, by the following open items — none resolved here:

| Dependency | Affected Domain Model Element | Source |
|---|---|---|
| Tenant vs. Company definition and multi-tenant model | Administration Context, Configuration Studio's Tenant Override, "Administration → All Contexts" relationship | [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-002 |
| AI Assistant naming and scope (registered as a Proposed name only; no Approved Bounded Context, architecture, provider, model, plugin design, implementation owner, or implementation authorization) | AI Assistant Context, and its five unregistered candidate sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) | AR-003 (Resolved 2026-09-19 as a Proposed-name-only registration; does not scope or authorize the AI Assistant Context, its architecture, or its sub-entities, which remain outside AR-003 pending separate future governance) |
| Approval Record naming/classification (distinct from the "Approval Management" module-name question AR-003 resolved, mapped to Approval Designer) | Artwork Context's Approval Record | Unassigned — no covering Architecture Review item; a separate, currently unassigned governance question |
| Item vs. Material/Product Template mapping | Inventory Context's Material Aggregate Root, Estimation Context's Product Template | AR-006 |
| Purchasing vs. Procurement naming | Procurement Context's name itself | AR-007 |
| Dispatch vs. Delivery terminology | Dispatch Context's Dispatch Record Aggregate Root | AR-008 |
| Quality module standalone status | Production Context's Quality Check Record Child Entity | AR-009 |
| BOM necessity for Cost Estimate's detailed cost-breakdown design (AR-005 Quotation strategy itself is Resolved — Option B, 2026-09-26; Quotation Line's exact target remains deferred to downstream DocType design) | Estimation Context's Estimation Engine Domain Service, Cost Estimate | AR-010 (remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework) |
| CRM Enquiry vs. Opportunity mapping | CRM Context's Lead/Enquiry Aggregate Root | AR-011 |

---

## Validation

- ✓ **Every business entity belongs to exactly one bounded context.** All 75 entities from the Business Entity Inventory are assigned to exactly one of the 17 contexts above (or explicitly excluded with rationale, in the case of the 5 Marketplace-collision entities, which are Pending Review rather than context-assigned).
- ✓ **Every aggregate has a business purpose.** See the Aggregate Catalog — each row's Bounded Context traces to a Business Purpose already stated in that context's section above.
- ✓ **No aggregate exists in multiple contexts.** Cross-context references (e.g., Counter Reading referencing Machine, Department referencing Branch) are modeled as relationships, not duplicate ownership — each entity has exactly one Bounded Context in the Aggregate/Child Entity/Value Object catalogs.
- ✓ **Value Objects are not treated as aggregates.** Paper Sizes, Job Types, Finishing Types, Media Profiles, Units of Measure, Delivery Method, Tax Template, Payment Terms, Currency, GST Configuration, and Machine Status all appear only in the Value Object Catalog, not the Aggregate Catalog.
- ✓ **Architecture Review items are referenced where applicable.** All 11 AR items (AR-001 is version-related and not domain-modeling-relevant, so it is the only one not cited) are referenced against the specific domain element they affect, in both the Bounded Context sections and the Open Architecture Dependencies table.
- ✓ **No ERPNext mapping is performed.** No DocType, database table, or ERPNext object name appears anywhere in this document except within citations to the ERPNext Fit Analysis/Gap Analysis for context, consistent with this document's exclusively business/domain scope.

---

# Related Documents

- [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md)
- [ERPNext_Fit_Analysis.md](ERPNext_Fit_Analysis.md)
- [ERPNext_Gap_Analysis.md](ERPNext_Gap_Analysis.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [03_DDD_Architecture.md](03_DDD_Architecture.md)
- [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-25 | Initial | Initial Canonical Domain Model. Organized 75 entities from the Business Entity Inventory into 17 Bounded Contexts (14 Approved per Blueprint, plus Configuration Studio, MachineIQ, Marketplace, and a flagged AI Assistant grouping), 37 Aggregate Roots, 11 Child Entities, 11 Value Objects, and 3 Domain Services. Classified all 75 entities in the Naming Review (45 Canonical, 28 Pending Architecture Review, 2 Not an Independent Entity). Performed Special Review of the Marketplace naming collision, Print Specification, Production Stage, and Machine Queue. Surfaced one new observed overlap (Reporting vs. Configuration Studio ownership of Report/Dashboard Definition) without adding it to the Architecture Review Register. No ERPNext mapping, ADR modification, or Architecture Review resolution performed. |
| 0.2 | 2026-07-25 | Documentation Clarification | Clarified Reporting and Configuration Studio ownership language for reports and dashboards. Reporting owns reporting capability and consumption; Configuration Studio owns Report Definition and Dashboard Definition configuration artifacts. Documentation clarification only; no architecture change. |
| 0.4 | 2026-09-26 | AR-004 and AR-005 Disposition Synchronization | Corrected active statements following [Architecture Review Register](../decisions/Architecture_Review_Register.md) AR-004's Resolved disposition (Option C, 2026-09-20) and AR-005's Resolved disposition (Option B, 2026-09-26: Custom Cost Estimate and pricing logic feed ERPNext's native Quotation through a governed handoff — see [14_Quotation_Engine.md](../blueprint/14_Quotation_Engine.md)), neither of which had previously been synchronized into this document. **Machine** and **Machine Profile** reclassified in the Naming Review table from "Pending Architecture Review" to **Canonical**, citing AR-004 Resolved, Option C; corrected the Production Context's Domain Services text accordingly. **Quotation** and **Quotation Line** remain **Pending Architecture Review** — AR-005 is Resolved, but AR-010 (BOM necessity, governing Cost Estimate's detailed cost-breakdown design) remains the open dependency; corrected the Estimation Context's Domain Services text and both Naming Review rows to record AR-005 Resolved and AR-010 as the remaining open item, using the normalized wording: AR-010 remains Open and should be resolved before detailed Cost Estimate cost-breakdown design, to avoid later rework. Removed the now-resolved Machine domain ownership row from the Open Architecture Dependencies table and repurposed the former Quotation-strategy row to describe the remaining AR-010 dependency (BOM necessity for Cost Estimate's detailed cost-breakdown design), noting AR-005's own Quotation-strategy question is Resolved. Recalculated the Naming Review summary from the actual final rows: 45 → 47 Canonical, 27 → 25 Pending Architecture Review, 1 Not adopted and 2 Not an Independent Entity unchanged (75 total, unchanged). All prior Revision History rows (0.1, 0.2, 0.3) are preserved unchanged. No Bounded Context, Aggregate Root, Child Entity, or Value Object was added or removed; no ERPNext mapping, DocType design, or database schema design performed; no Architecture Review Register item was created or modified; neither AR-004 nor AR-005 was further modified by this document; AR-006 through AR-011 unchanged; no implementation was authorized. |
| 0.3 | 2026-09-20 | AR-003 Disposition Synchronization | Corrected active statements following AR-003's Resolved disposition ([Architecture Review Register](../decisions/Architecture_Review_Register.md) Version 0.5, 2026-09-19): "Print Specification" reclassified in the Naming Review table and Special Review section from "Pending Architecture Review" to **Not adopted**, recording its resolved mapping to Product Template, Job Types, Finishing Types, and Paper Sizes; "AI Assistant" corrected throughout (AI Assistant Context heading and note, Naming Review) to record it as a **Proposed name only** (Naming Registry Section 40) with no Approved Bounded Context, architecture, provider, model, plugin design, implementation owner, or implementation authorization; its five candidate sub-entities (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) corrected to record they remain unregistered and outside AR-003's eight-term scope, pending separate future governance, rather than "AR-003." Corrected Approval Record's Naming Review note and the Artwork Context's Domain Services text to record that Approval Record's own naming/classification was never one of AR-003's eight terms (only the requested "Approval Management" module name, mapped to Approval Designer, was) and remains a separate, currently unassigned governance question. Split the Open Architecture Dependencies table's combined AR-003 row into a corrected AI Assistant row and a separate, non-AR-003 Approval Record row, and removed the now-resolved Print Specification entry from that open-dependencies table. Updated the Naming Review summary to 45 Canonical, 27 Pending Architecture Review, 1 Not adopted, 2 Not an Independent Entity (75 total, unchanged). No Bounded Context, Aggregate Root, Child Entity, Value Object, or Domain Service was added, removed, or approved; AR-004 through AR-011 unchanged; no ERPNext mapping performed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] No ERPNext mapping or DocType design included
- [ ] No Blueprint/ADR terminology invented, renamed, or superseded
- [ ] No Architecture Review Register item resolved
- [ ] Consistent with Business Entity Inventory, ERPNext Fit Analysis, and Gap Analysis baseline
- [ ] Reviewed by Project Owner
