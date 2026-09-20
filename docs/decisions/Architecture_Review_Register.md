# Architecture Review Register

Version:
0.5

Status:
Draft — Open Register (living document)

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-19

---

## 1. Purpose

This register exists to track every unresolved architectural or naming question surfaced during [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md), so that implementation planning does not silently proceed on assumed answers to questions that were explicitly flagged, not resolved, in that analysis.

**Relationship to the ERPNext Fit Analysis.** The Fit Analysis classified every PrintHub business capability as Native/Extend/Customize/Plugin/Future. In doing so, it repeatedly encountered questions it was instructed not to resolve — version conflicts, unresolved naming conflicts, and genuinely open architectural choices (e.g. what ERPNext object, if any, underlies "Machine"). This register is the formal collection point for those items. It does not restate the Fit Analysis's classification content; it only extracts and tracks the open questions.

**Relationship to the ERPNext Gap Analysis.** This register sits between the completed ERPNext Fit Analysis and the upcoming ERPNext Gap Analysis. Its purpose is to ensure the Gap Analysis is not built on top of unacknowledged architectural uncertainty — Critical items in this register are intended to block the start of Gap Analysis work until they are at least formally acknowledged (resolved or deferred) by the appropriate authority.

**Relationship to Architecture Freeze.** This register is a mandatory input to the future Implementation Decision Review (Architecture Freeze). Architecture Freeze cannot be declared while Critical or High items remain in `Open` status with no formal disposition.

**Relationship to ADRs.** This register **records review items — it does not record decisions.** An ADR is the outcome of a decision-making process (Project Owner approval, per [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 4, Naming Authority, and `docs/Documentation_Workflow.md` Section 11). A register entry is the *input* to that process: the question, its impact, and its options — never the answer. When a review item is resolved, its disposition in this register should point to the ADR that resolved it (or the Blueprint/Naming Registry update that closed it); this register is never itself amended to contain the decision's rationale.

---

## 2. Review Summary

| ID | Topic | Status | Priority | Blocks Development |
|----|-------|--------|----------|-------------------|
| AR-001 | ERPNext Platform Version (15/16 conflict) | Resolved | Critical | No |
| AR-002 | Multi-Tenant Strategy Alignment | Resolved | Critical | No |
| AR-003 | Naming Registry Alignment (non-Approved module names, unregistered terms) | Resolved | High | No |
| AR-004 | Machine Domain Ownership | Open | High | Partial |
| AR-005 | Quotation Strategy | Open | High | Partial |
| AR-006 | Item vs. Material/Product Template Mapping | Open | High | Partial |
| AR-007 | Purchasing vs. Procurement Module Naming | Open | Medium | No |
| AR-008 | Dispatch vs. Delivery Terminology | Open | Medium | No |
| AR-009 | Quality Module Status | Open | Medium | No |
| AR-010 | BOM Necessity for Estimation | Open | Medium | Partial |
| AR-011 | CRM Enquiry vs. Opportunity Mapping | Open | Low | No |

---

## 3. Review Items

### AR-001

#### Title
ERPNext Platform Version

#### Background
The Fit Analysis was commissioned against "ERPNext 15/Frappe 15." Blueprint documentation (`07_Technology_Stack.md`, currently Draft) specifies ERPNext v16, consistent with the binding Accepted decision in [ADR-001-ERPNext-Framework](ADR-001-ERPNext-Framework.md), which formally accepts ERPNext v16 as the framework. This was recorded as Conflict #1 in [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) and was not resolved there.

#### Current Situation
Two different, contradicting version targets exist in active project documentation: ERPNext v16 (Draft Blueprint content aligned with the Accepted ADR) and ERPNext 15/Frappe 15 (this and other recent task instructions). No document has reconciled them.

#### Why Review Is Required
DocType availability, field behavior, API surface, and upgrade path all differ materially between ERPNext 15 and 16. Every capability classification in the Fit Analysis (Native/Extend/Customize) is version-sensitive; proceeding to Gap Analysis or DocType mapping against the wrong version risks discovering, mid-implementation, that a "Native" capability behaves differently or doesn't exist as assumed.

#### Impact
- **Architecture:** Affects every ERPNext-facing classification in the Fit Analysis.
- **Data Model:** DocType field availability may differ between versions.
- **Implementation:** Environment provisioning ([../implementation/07_Deployment_Strategy.md](../implementation/07_Deployment_Strategy.md)) cannot be finalized without a confirmed version.
- **Upgradeability:** ADR-001's upgrade-safety rationale was written against v16; changing version changes the upgrade baseline.
- **Documentation:** ADR-001 and `07_Technology_Stack.md` would require amendment if the version target actually changes.

#### Possible Options
- Option A: Confirm ERPNext v16 remains the target; treat "15" in recent instructions as informal/incorrect shorthand.
- Option B: Formally move the target to ERPNext 15 via ADR amendment, updating `07_Technology_Stack.md` and ADR-001.
- Option C: Support both, with version-specific implementation notes where behavior diverges (higher ongoing maintenance cost).

#### Required Decision
Which ERPNext/Frappe version is the actual Phase 1 implementation target, and how is the resulting conflict between ADR-001/`07_Technology_Stack.md` and recent task instructions formally reconciled.

#### Blocking Status
Critical

#### Disposition
Resolved — Option A selected

**Decision date:** 2026-07-26
**Decision authority:** Project Owner
**Resolving decision:** [ADR-001-ERPNext-Framework](ADR-001-ERPNext-Framework.md), Accepted, Version 1.0 — reaffirmed (not amended or superseded)

**Governed target:**
- ERPNext v16
- corresponding Frappe v16 major version
- one governed major-version line
- no simultaneous v15/v16 support

**Version-governance policy:**
- minor and patch selection within the v16 line is centrally governed by the PrintHub platform owner;
- official maintenance and security status must be considered;
- compatibility validation and controlled testing are required;
- rollback or recovery procedures are required;
- Docker, CI, and deployment configuration must be reconciled.

**Required revalidation:**
- ERPNext Fit Analysis;
- ERPNext Gap Analysis;
- ERPNext DocType Mapping;
- Technology Stack Blueprint;
- deployment and environment documentation;
- Docker and CI configuration.

**Implementation Authorization:** Not Granted

Resolution of AR-001 removes the version-selection Architecture Review blocker only. It does not authorize coding, publish an implementation specification, resolve another AR, settle a DocType ownership decision, or make a roadmap workstream Implementation-ready or Implementation-authorized. Other AR gates and documentation prerequisites remain effective; every roadmap workstream remains Not Authorized until separately governed.

#### Dependencies
- [ADR-001-ERPNext-Framework](ADR-001-ERPNext-Framework.md)
- [../blueprint/07_Technology_Stack.md](../blueprint/07_Technology_Stack.md)
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Conflict #1)
- Existing ERPNext Gap Analysis (Draft; remains conditional and version-dependent)

---

### AR-002

#### Title
Multi-Tenant Strategy Alignment

#### Background
Recent task instructions state a specific, settled multi-tenant strategy (one ERPNext site, one database, one backend instance per tenant; shared app code/Docker image/CI-CD; no shared database). [ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md) is "Accepted (strategy); Implementation Deferred" and explicitly reserves the concrete model decision for `docs/blueprint/25_MultiTenant_Architecture.md`, which does not yet exist. "Tenant" itself is a Pending ADR term against "Company" ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 11). This was recorded as Conflict #2 in [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md), used there only as a working assumption, not resolved.

#### Current Situation
A specific tenancy model is being asserted in project instructions as though ratified, while the formal architecture record shows it as strategy-only, implementation-deferred, with the underlying "Tenant" concept itself not yet reconciled with "Company" (ERPNext's native scoping anchor).

#### Why Review Is Required
Every Configuration Studio surface touching "tenant" ([../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md), [../configuration/12_Feature_Flags.md](../configuration/12_Feature_Flags.md)), the Fit Analysis's Configuration Studio section, and deployment/environment planning ([../implementation/07_Deployment_Strategy.md](../implementation/07_Deployment_Strategy.md)) depend on knowing whether tenancy is site-per-tenant, database-per-tenant-on-shared-site, or some other model — and on what "tenant" even formally means relative to Company. Proceeding without ratification risks building deployment tooling and data-scoping logic around an assumption that the eventual `25_MultiTenant_Architecture.md` may not confirm.

#### Impact
- **Architecture:** Determines deployment topology, data isolation strategy, and whether Company alone is a sufficient scoping anchor.
- **Data Model:** Determines whether tenant-scoping fields are needed at all (site-per-tenant needs none) or whether Company-level scoping fields are required throughout.
- **Implementation:** Directly affects [../implementation/07_Deployment_Strategy.md](../implementation/07_Deployment_Strategy.md) and provisioning automation.
- **Naming:** "Tenant" vs. "Company" resolution is a prerequisite for using either term consistently in implementation artifacts.
- **Configuration Studio:** [../configuration/13_Tenant_Customization.md](../configuration/13_Tenant_Customization.md)'s entire scope depends on this.

#### Possible Options
- Option A: Ratify the one-site-per-tenant, one-database-per-tenant model as formally described in recent instructions, via completion of `docs/blueprint/25_MultiTenant_Architecture.md` and the Tenant/Company ADR.
- Option B: Formally adopt a different candidate model (e.g. single instance, multi-Company) as already listed in [../architecture/04_MultiTenant_Architecture.md](../architecture/04_MultiTenant_Architecture.md) (working draft).
- Option C: Defer ratification entirely until Phase 2+ planning, continuing Phase 1 as explicitly single-tenant with no multi-tenant mechanism built yet.

#### Required Decision
Whether the one-site-per-tenant model is formally ratified as PrintHub's multi-tenant architecture, and how "Tenant" is formally defined relative to "Company."

#### Blocking Status
Critical

#### Disposition
Resolved — Option A selected

**Decision date:** 2026-07-28
**Decision authority:** Project Owner
**Resolving decision:** [ADR-015-Tenant-Company-Multi-Tenancy-Model](ADR-015-Tenant-Company-Multi-Tenancy-Model.md), Accepted, Version 1.0 — companion to and completing the concrete-model decision deferred by [ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md) (which remains Accepted and is neither superseded nor reversed)
**Architecture elaboration:** `docs/blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0 — **not Published**

**Decision:** PrintHub formally adopts one isolated Frappe site and one isolated operational database per print-shop Tenant.

**Tenant and Company:**
- Tenant is the subscription, site, database, isolation, backup, upgrade-rollout, and operational-management unit.
- Company is an ERPNext legal/accounting and business-scoping entity inside a Tenant.
- One Tenant may contain one or more Companies.
- Company is **not** synonymous with Tenant and is **not** the isolation boundary between unrelated print shops.

**Infrastructure:**
- Underlying compute hosts may be shared or dedicated by service tier.
- Host sharing does **not** permit operational-database sharing between unrelated Tenants.
- Shared governed PrintHub application code, Docker-image strategy, CI/CD, and centralized platform operations are permitted.
- Permanent Tenant-specific code or version forks are prohibited; upgrades remain centrally governed.

**Job Card Tier A consequence:**
- Company-scoped.
- Site identity remains implicit.
- No separate Tenant field by default.
- No dependency on global Customer identity or cross-tenant analytics.

**Explicitly deferred (not decided by this disposition):**
- global Customer directory;
- automatic cross-tenant Customer matching;
- centralized transaction storage;
- cross-tenant business analytics;
- data warehouse or data lake;
- cross-shop visibility;
- consent, identity-resolution, and analytics-retention architecture;
- exact RPO, RTO, pricing, quotas, or contractual SLA values;
- detailed provisioning and operational implementation.

Every service tier receives baseline correctness, security, isolation, data integrity, and recoverable backup protection; paid tiers may differ only in capacity, retention, monitoring, recovery, and support priority.

**Implementation Authorization:** Not Granted. This resolution is an architecture decision only. `docs/blueprint/25_MultiTenant_Architecture.md` remains Approval and is not Published. This disposition does not authorize tenant provisioning, deployment or infrastructure implementation, Docker or CI changes, schema or migration work, or Job Card Tier A coding. Published coding specifications remain required, and a separate scoped implementation-authorization decision remains required before any coding may begin. Every roadmap workstream remains Not Authorized. The Full Architecture Freeze remains unachieved.

#### Dependencies
- [ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md) — remains Accepted; identifies ADR-015 as its companion/completion decision (see Disposition above)
- `docs/blueprint/25_MultiTenant_Architecture.md` — **no longer reserved/unwritten**: now exists at Approval, Version 1.0 (not Published), per [ADR-010](ADR-010-Blueprint-Numbering-Strategy.md)'s reservation
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 11 — **Resolved** through Accepted ADR-015
- [../architecture/04_MultiTenant_Architecture.md](../architecture/04_MultiTenant_Architecture.md) (working draft) — remains an unreconciled working draft; its reconciliation with `docs/blueprint/25_MultiTenant_Architecture.md` is a **separate, later controlled task**, not performed by this disposition
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Conflict #2) — historical context preserved; the working-assumption basis this conflict recorded is now formally resolved by ADR-015

---

### AR-003

#### Title
Naming Registry Alignment (Non-Approved Module Names and Unregistered Terms)

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) Conflict #3 and #4 identified that recent task instructions referenced several module/capability names not present in the Approved Module Registry ([../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 11): "Print Specification," "Approval Management," "Production Workflow," "Machine Management" (Approved name is "Machine Scheduling"), "Finishing," and "Quality Control." It also identified "AI Assistant" and "Production Orchestration" as terms absent from the Naming Registry entirely.

#### Current Situation
The Fit Analysis analyzed the underlying business need for each requested term under its closest Approved concept and explicitly declined to adopt the requested name in any capability classification, DocType recommendation, or ownership table. No formal disposition (Approved / Rejected / Merged / Proposed) has been recorded for any of these terms in the Naming Registry itself.

#### Why Review Is Required
Per [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 38 (Mandatory Registration), no new business term may be used anywhere in PrintHub — including implementation planning documents — without Registry registration. Continuing to reference these names informally in task instructions, without either registering them as Proposed or formally confirming they map to existing Approved terms, risks the same term drifting into implementation artifacts under two different names.

#### Impact
- **Naming:** Direct — this is the core Naming Registry governance concern.
- **Documentation:** Future implementation documents referencing these capabilities must use Approved names consistently once resolved.
- **Configuration Studio:** "Approval Management" specifically overlaps with the already-Approved Approval Designer ([../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)) — risk of a duplicate concept if not reconciled.

#### Possible Options
- Option A: Formally confirm each requested term maps to an existing Approved concept (as the Fit Analysis provisionally did) and discontinue using the requested names going forward.
- Option B: Propose one or more of the requested terms (e.g. "Print Specification") to the Naming Registry as genuinely new, Level 1/2 terminology, if Architecture Review determines the existing Approved concepts do not fully cover the need.
- Option C: Take no action; continue treating the mismatch as informal shorthand understood in context (not recommended given Section 38's Mandatory Registration rule, but recorded as a genuine option since no decision has been made).

#### Required Decision
For each of "Print Specification," "Approval Management," "Production Workflow," "Machine Management," "Finishing," "Quality Control," "AI Assistant," and "Production Orchestration": confirm mapping to an existing Approved term, or formally propose as new terminology per the Naming Review Checklist ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 26/39).

#### Blocking Status
High

#### Dependencies
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Sections 11, 26, 38, 39
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md)
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Conflicts #3, #4; Section 4 entries for each affected term)

#### Disposition
Resolved — Option A selected

**Decision date:** 2026-09-19
**Decision authority:** Project Owner
**Resolving decision:** Naming Registry Section 40 (AR-003 Naming Alignment Disposition), recording the confirmed disposition of all eight requested terms.

**Per-term disposition:**
- **Print Specification** — Not adopted as a module, DocType, or umbrella object; maps to Product Template, Job Types, Finishing Types, and Paper Sizes (existing Approved master data).
- **Approval Management** — Not adopted as a module; canonical configuration surface is Approval Designer.
- **Production Workflow** — Permitted as a business-workflow label; not a module, DocType, service, or separate capability.
- **Machine Management** — Not adopted as a module name; canonical module is Machine Scheduling. AR-004 (Machine object base) remains open and unchanged.
- **Finishing** — Existing Approved business term (Naming Registry Section 22/24); not an approved standalone module.
- **Quality Control** — Permitted as a workflow/process label and embedded Job Card capability; the standalone-module question remains AR-009 (Quality Module Status) and Naming Registry §27 item 9, open and unchanged.
- **AI Assistant** — Registered as a Proposed future capability name only (Naming Registry Section 40). No Approved Bounded Context, architecture, provider, model, plugin design, or implementation. Excluded from implementation-ready scope pending separate future governance.
- **Production Orchestration** — Not adopted as a separate capability or module; maps to Production Planning and Job Cards.

**Implementation Authorization:** Not Granted. This disposition is a naming-alignment decision only. It does not authorize any DocType, module, plugin, workflow, or implementation work for any of the eight terms; it does not resolve AR-004 or AR-009; it does not authorize AI Assistant's architecture, Bounded Context, provider, model, or plugin design. A separate scoped implementation-authorization decision, preceded by Published implementation specifications, remains required for any future work.

**Disposition evidence and synchronized references:**
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 40 (new) — the durable disposition table for all eight terms.
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) — unchanged; no new module added.
- [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md) — unchanged; confirmed as the canonical surface for "Approval Management."
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) — its per-term analysis (Section 4) is confirmed consistent with this disposition; its AI Assistant registration-gap language is separately synchronized.
- [../architecture/Plugin_Architecture.md](../architecture/Plugin_Architecture.md) — AI Assistant's "unregistered" language is separately synchronized to reflect Proposed registration; Bounded Context Ownership remains None.

---

### AR-004

#### Title
Machine Domain Ownership

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 4, "Machine Management") identified that Machine has no confirmed ERPNext base object. Three candidates were surfaced: ERPNext's generic Asset DocType, ERPNext's Manufacturing Workstation DocType, or a wholly Custom DocType. This same open question was already logged, with fewer candidates, in [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions.

#### Current Situation
No candidate has been selected. Machine Profile (capability/constraint modeling) is separately and consistently classified as Custom across both documents, regardless of which base is chosen for Machine itself.

#### Why Review Is Required
Machine Scheduling (a Core Domain module per [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)) and the entire Job Card execution flow depend on Machine's structure. Choosing Asset, Workstation, or Custom has materially different implications for lifecycle management, native report reuse, and how Machine Profile relates to its base — this cannot be decided implicitly during DocType design; it must be decided first.

#### Impact
- **Architecture:** Determines whether Machine Scheduling extends ERPNext's Manufacturing/Assets modules or is fully Custom.
- **Data Model:** Determines Machine's field inheritance and relationship to Machine Profile.
- **Implementation:** Directly blocks detailed design for [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)'s Machine Scheduling entry (Phase 3, per [../implementation/01_Phase_1_Roadmap.md](../implementation/01_Phase_1_Roadmap.md)).
- **Upgradeability:** Extending Asset or Workstation ties Machine's upgrade path to ERPNext's Assets/Manufacturing module changes; Custom avoids this but forgoes any native reuse.
- **Integrations:** Future MachineIQ Machine Event/Telemetry concepts ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 36) will attach to whichever base is chosen.

#### Possible Options
- Option A: Extend ERPNext Asset.
- Option B: Extend ERPNext Workstation.
- Option C: Build Machine as a wholly Custom DocType.
- Option D: Hybrid — e.g. Asset for lifecycle/depreciation tracking, with a Custom Machine Profile-adjacent object for production-capability modeling.

#### Required Decision
Which ERPNext object, if any, Machine is built upon: Asset, Workstation, Custom, or a Hybrid combination.

#### Blocking Status
High

#### Dependencies
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Manufacturing, Section 4 Machine Management, Section 8 item 3)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) (Open Questions)
- [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md) (Machine Scheduling entry)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md) (Machine, Machine Profile)

---

### AR-005

#### Title
Quotation Strategy

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3, Selling) identified that Estimation is a Core Domain requiring substrate/finishing/machine-time-based costing and multi-version quoting, and that ERPNext's native Quotation DocType has no concept of any of these. Whether to feed a heavily-extended native Quotation, or introduce a Custom pricing/estimation document distinct from the native Quotation, was left open — the same question already present in [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) Open Questions.

#### Current Situation
The Fit Analysis provisionally classified the *document* as Extend and the *pricing engine* as Customize, but explicitly noted this split is not a resolution of whether native Quotation is structurally sufficient.

#### Why Review Is Required
Estimation is explicitly called out in [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) as PrintOS's "key differentiator." Under-provisioning it by forcing complex pricing logic into a generic native Quotation DocType risks structural strain later; over-provisioning it with a fully Custom document risks losing native Sales Order/Quotation workflow, permissions, and reporting integration for no reason. This is a genuine architectural trade-off, not a default.

#### Impact
- **Architecture:** Determines whether Estimation's Application-layer output writes to a native ERPNext document or a Custom one.
- **Data Model:** Determines Quotation's schema ownership (ERPNext-defined vs. PrintHub-defined).
- **Implementation:** Blocks detailed design for [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)'s Estimation and Sales entries.
- **Naming:** Affects how "Quotation" (canonical per [ADR-013-Quotation-Terminology](ADR-013-Quotation-Terminology.md)) maps to a specific DocType — not a naming change, but a structural mapping decision.
- **Configuration Studio:** Affects how the Workflow Designer ([../configuration/03_Workflow_Designer.md](../configuration/03_Workflow_Designer.md)) configures Quotation state transitions, depending on which document type it targets.

#### Possible Options
- Option A: Extend native Quotation heavily via Custom Fields and a child table for cost breakdown.
- Option B: Introduce a Custom Estimation/pricing document that feeds into native Quotation only at the point of customer-facing document generation.
- Option C: Replace native Quotation entirely with a Custom DocType (highest customization, most upgrade risk).

#### Required Decision
Whether Quotation/Estimation is delivered via extended native Quotation, a separate Custom Estimation document feeding native Quotation, or a fully Custom Quotation replacement.

#### Blocking Status
High

#### Dependencies
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Selling, Section 4 Estimation/Quotation, Section 8 item 3)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md) (Open Questions)
- [ADR-013-Quotation-Terminology](ADR-013-Quotation-Terminology.md)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) (Estimation context)

---

### AR-006

#### Title
Item vs. Material/Product Template Mapping

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Inventory, Section 7) relied on [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 10 (ERPNext "Item" vs. PrintOS "Material"/"Product Template"), which is Pending ADR, to provisionally classify Material/Substrate as "Extend" against ERPNext Item.

#### Current Situation
No ADR resolves whether PrintOS's Material, Substrate, and Product Template concepts map to ERPNext Item directly, Item Variants, or require a distinct object. The Fit Analysis's classification is explicitly marked provisional pending this.

#### Why Review Is Required
Inventory, Estimation, and Production all consume Material/Substrate data; an incorrect early mapping (e.g. assuming Item Variants suffice when a distinct Substrate object is actually needed) would require rework across multiple modules once discovered.

#### Impact
- **Architecture:** Affects Inventory and Estimation's Infrastructure-layer adapters.
- **Data Model:** Determines whether Substrate-specific attributes (GSM, media profile) live on Item Variant Attributes or a separate DocType.
- **Implementation:** Affects [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md) Inventory and Estimation entries.

#### Possible Options
- Option A: Map Material/Substrate/Product Template to ERPNext Item with Item Variant Attributes.
- Option B: Extend Item with Custom Fields, no Variants.
- Option C: Introduce a distinct Substrate/Material Custom DocType linked to Item.

#### Required Decision
Whether PrintOS Material/Substrate/Product Template concepts map to native Item (with or without Variants) or require a distinct Custom object.

#### Blocking Status
High

#### Dependencies
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 10
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Inventory)
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md)

---

### AR-007

#### Title
Purchasing vs. Procurement Module Naming

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Purchasing) noted the existing, unresolved conflict where the Blueprint module is named "Purchasing" ([../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)) while the Bounded Context is named "Procurement" ([../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)) — [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 3, Pending ADR.

#### Current Situation
Both names remain in active use in different, currently-Published Blueprint documents. The Fit Analysis used "Purchasing" as the module name (matching `09_PrintOS_Modules.md`) without resolving the underlying conflict.

#### Why Review Is Required
Implementation artifacts (workspace names, module registry entries) need a single name; continuing with an unresolved dual-naming situation risks the two terms diverging further as more documents are written.

#### Impact
- **Naming:** Direct.
- **Documentation:** `09_PrintOS_Modules.md` and `06_Bounded_Contexts.md` would need reconciliation once resolved.

#### Possible Options
- Option A: Standardize on "Purchasing" (module-level term), updating `06_Bounded_Contexts.md`.
- Option B: Standardize on "Procurement" (context-level term), updating `09_PrintOS_Modules.md`.

#### Required Decision
Which term — Purchasing or Procurement — is canonical at both the module and bounded-context level.

#### Blocking Status
Medium

#### Dependencies
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 3
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)

---

### AR-008

#### Title
Dispatch vs. Delivery Terminology

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 4 Dispatch) noted the existing Dispatch vs. Delivery naming conflict ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 5, Pending ADR) while classifying the underlying capability as Extend against ERPNext's native Delivery Note.

#### Current Situation
"Dispatch" is the Bounded Context/module name; ERPNext's native object is named "Delivery Note"; Blueprint prose also uses "Delivery" informally. No ADR distinguishes whether these are the same concept under different names in different layers, or require formal disambiguation.

#### Why Review Is Required
DocType naming for the Extend strategy (Custom Fields on Delivery Note) needs to know whether "Dispatch" should appear anywhere in field/label naming, or whether ERPNext's native "Delivery" vocabulary is retained at the DocType layer while "Dispatch" remains the module/context name only.

#### Impact
- **Naming:** Direct.
- **Data Model:** Affects Custom Field naming on Delivery Note.
- **Documentation:** Affects consistency between `06_Bounded_Contexts.md` and future implementation naming.

#### Possible Options
- Option A: Confirm "Dispatch" is the module/context name while "Delivery Note"/"Delivery" remains the correct ERPNext-layer term — no field-level renaming needed.
- Option B: Introduce a distinct "Dispatch Record" concept separate from Delivery Note.
- Option C: Standardize fully on one term across all layers.

#### Required Decision
Whether Dispatch and Delivery are confirmed as the same concept expressed at different layers (context/module vs. ERPNext DocType), or require distinct treatment.

#### Blocking Status
Medium

#### Dependencies
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 5
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 4 Dispatch)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md)

---

### AR-009

#### Title
Quality Module Status

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 4, "Quality Control") noted that Quality Check Record is currently embedded within the Job Cards module per [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md), while a standalone "Quality" module remains Pending ADR ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 9).

#### Current Situation
No standalone Quality module exists in Approved Blueprint documentation. The Fit Analysis classified quality checkpoint functionality as Customize (within Job Cards) and explicitly declined to classify a standalone "Quality Control" module, marking it Future.

#### Why Review Is Required
If a standalone Quality module is later approved, quality-checkpoint functionality already built inside Job Cards would need to be extracted — a costly rework if not anticipated. Conversely, building for a standalone module prematurely would be scope creep against current Approved Blueprint content.

#### Impact
- **Architecture:** Determines whether quality checkpoints are a Job Cards sub-feature or an independent module/bounded context.
- **Data Model:** Determines Quality Check Record's DocType ownership.
- **Documentation:** Affects `09_PrintOS_Modules.md` if a new module is approved.

#### Possible Options
- Option A: Confirm Quality Check Record remains a Job Cards sub-feature for Phase 1; revisit only if Naming Registry item 9 is resolved in favor of a standalone module.
- Option B: Proactively design Quality Check Record as a loosely-coupled capability now, anticipating eventual extraction.
- Option C: Escalate Naming Registry item 9 for resolution now, ahead of Phase 3 (Production) implementation.

#### Required Decision
Whether to proceed with Quality Check Record as a Job Cards sub-feature for Phase 1, or resolve the standalone Quality module question first.

#### Blocking Status
Medium

#### Dependencies
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 9
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 4 Quality Control)

---

### AR-010

#### Title
BOM Necessity for Estimation

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Manufacturing, Section 8 Open Questions) raised whether ERPNext's BOM (Bill of Materials) concept is genuinely needed to express a print job's material/finishing requirements for Estimation, or whether Product Template + Job Types + Finishing Types (already Approved master data) is sufficient without it.

#### Current Situation
BOM was tentatively classified as Future ("not confirmed as needed for Phase 1") in the Fit Analysis. No confirmation exists either way.

#### Why Review Is Required
If BOM later proves necessary for Estimation's cost breakdown, retrofitting it after Estimation's pricing engine (AR-005) is already built without it would require rework. This is directly coupled to AR-005 and should likely be reviewed alongside it.

#### Impact
- **Architecture:** Affects whether Estimation's Application layer models material/finishing requirements via a BOM-like structure or its own construct.
- **Data Model:** Affects whether ERPNext's BOM DocType is reused at all anywhere in PrintHub.
- **Implementation:** Affects Estimation's Phase 2 implementation detail.

#### Possible Options
- Option A: Confirm Product Template + Job Types + Finishing Types is sufficient; do not adopt BOM anywhere in Phase 1.
- Option B: Adopt BOM specifically for Estimation's material/finishing cost breakdown, without adopting the rest of Manufacturing.
- Option C: Defer the question entirely until Estimation detailed design begins.

#### Required Decision
Whether BOM is adopted, in any form, for Estimation's cost-breakdown modeling.

#### Blocking Status
Medium

#### Dependencies
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 Manufacturing, Section 8 Open Questions)
- AR-005 (Quotation Strategy) — closely coupled
- [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md)

---

### AR-011

#### Title
CRM Enquiry vs. Opportunity Mapping

#### Background
[ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 CRM) noted that "Enquiry" as a distinct concept from ERPNext's native "Opportunity" is not fully reconciled — [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)'s Core Business Entities lists "Lead / Enquiry" together without a confirmed 1:1 ERPNext mapping decision.

#### Current Situation
The Fit Analysis provisionally classified CRM as Extend, assuming minor Custom Field additions to Lead/Opportunity/Contact suffice, but did not confirm whether "Enquiry" needs any distinct treatment from "Opportunity."

#### Why Review Is Required
This is a lower-impact question than AR-001–AR-006 but should still be confirmed before CRM implementation (Phase 2, per [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)) begins, to avoid a late-discovered gap.

#### Impact
- **Data Model:** Minor — affects whether Custom Fields on Opportunity suffice or a distinct concept is needed.
- **Naming:** Minor — "Enquiry" is not currently in the Naming Registry's Business Vocabulary Registry as a standalone entry distinct from Lead.

#### Possible Options
- Option A: Confirm "Enquiry" is fully represented by ERPNext's native Opportunity, with print-specific Custom Fields.
- Option B: Confirm "Enquiry" is a distinct pre-Opportunity stage requiring its own light-weight Custom concept.

#### Required Decision
Whether "Enquiry" requires any modeling distinct from ERPNext's native Opportunity.

#### Blocking Status
Low

#### Dependencies
- [ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) (Section 3 CRM)
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md)
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) (CRM context)

---

## 4. Cross-Review Matrix

| Review Item | Impacts Blueprint | Impacts DocTypes | Impacts Configuration Studio | Blocks Gap Analysis |
|-------------|-------------------|------------------|------------------------------|---------------------|
| AR-001 ERPNext Platform Version | Yes (`07_Technology_Stack.md`, ADR-001) | Yes (version-dependent field/DocType availability) | No | Yes |
| AR-002 Multi-Tenant Strategy Alignment | Yes (`25_MultiTenant_Architecture.md`) | Partial (scoping fields) | Yes (Tenant Customization) | Yes |
| AR-003 Naming Registry Alignment | Yes (`09_PrintOS_Modules.md`) | Partial | Yes (Approval Designer overlap) | Partial |
| AR-004 Machine Domain Ownership | No | Yes (Machine, Machine Profile) | No | Partial |
| AR-005 Quotation Strategy | No | Yes (Quotation) | Yes (Workflow Designer target) | Partial |
| AR-006 Item vs. Material/Product Template | Yes (`08_Master_Data_Model.md`) | Yes (Item, Material, Substrate) | No | Partial |
| AR-007 Purchasing vs. Procurement Naming | Yes (`09_PrintOS_Modules.md`, `06_Bounded_Contexts.md`) | No | No | No |
| AR-008 Dispatch vs. Delivery Terminology | Yes (`06_Bounded_Contexts.md`) | Partial (field naming) | No | No |
| AR-009 Quality Module Status | Yes (`09_PrintOS_Modules.md`) | Yes (Quality Check Record) | No | No |
| AR-010 BOM Necessity for Estimation | No | Partial | No | Partial |
| AR-011 CRM Enquiry vs. Opportunity Mapping | Yes (`05_Domain_Model.md`) | Partial | No | No |

---

## 5. Review Priority

### Critical
Must be resolved before: **ERPNext Gap Analysis**

- AR-001 ERPNext Platform Version
- AR-002 Multi-Tenant Strategy Alignment

### High
Must be resolved before: **DocType Mapping**

- AR-003 Naming Registry Alignment
- AR-004 Machine Domain Ownership
- AR-005 Quotation Strategy
- AR-006 Item vs. Material/Product Template Mapping

### Medium
Must be resolved before: **Architecture Freeze**

- AR-007 Purchasing vs. Procurement Module Naming
- AR-008 Dispatch vs. Delivery Terminology
- AR-009 Quality Module Status
- AR-010 BOM Necessity for Estimation

### Low
Can be deferred.

- AR-011 CRM Enquiry vs. Opportunity Mapping

---

## 6. Exit Criteria

This register can be considered complete (ready to support Architecture Freeze) when:

- [ ] Every review item has a recorded outcome (Resolved via ADR, Formally Deferred, or Rejected).
- [ ] Every Critical item has been resolved or formally deferred by the Project Owner before ERPNext Gap Analysis begins.
- [ ] Every High item has been resolved or formally deferred before DocType Mapping begins.
- [ ] Every required ADR arising from a resolved item has been identified and linked from this register.
- [ ] All naming conflicts referenced in this register (AR-003, AR-006, AR-007, AR-008, AR-009) have been addressed via the Naming Registry's Term Change Policy ([../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 33), not informally.
- [ ] No Open item with Blocking Status Critical or High remains at the point Architecture Freeze is declared.

---

# Related Documents

- [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md)
- [00_ADR_Index.md](00_ADR_Index.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [../implementation/03_ERPNext_Mapping.md](../implementation/03_ERPNext_Mapping.md)
- [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)
- [../blueprint/00_Master_Index.md](../blueprint/00_Master_Index.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-24 | Initial | Initial Architecture Review Register. Extracted 11 unresolved review items (AR-001 through AR-011) from `ERPNext_Fit_Analysis.md`'s flagged conflicts and open questions. No architecture, ADR, or Blueprint content resolved or modified. |
| 0.2 | 2026-07-27 | AR-001 Source-Authority Correction | Corrected AR-001's Background and Current Situation wording, which inaccurately described `07_Technology_Stack.md` as "Published Blueprint documentation" when its live Status is Draft and the repository-wide Published document count is zero. Replaced with wording accurately stating the Blueprint content is Draft and aligned with the binding, Accepted decision in ADR-001-ERPNext-Framework. Also updated AR-001's Dependencies entry for the ERPNext Gap Analysis from "Future... (version-dependent)" to reflect that the Draft Gap Analysis already exists and remains conditional and version-dependent. All official AR-001 fields (ID, Topic, Status: Open, Priority: Critical, Blocks Development: Yes, Options A/B/C, Required Decision, Blocking Status) preserved byte-for-byte. No other AR entry changed. AR-001 remains Open; no version selected; no implementation authorized; ADR-001 not modified. |
| 0.3 | 2026-07-26 | AR-001 Resolved — Project Owner Disposition | AR-001 formally resolved by Project Owner selection of Option A. Section 2 Review Summary row updated: Status Open → Resolved, Blocks Development Yes → No, Priority unchanged at Critical. Added a Disposition block to the AR-001 entry recording ADR-001-ERPNext-Framework (Accepted, Version 1.0) as the reaffirmed — not amended or superseded — resolving decision; the governed target (ERPNext v16, corresponding Frappe v16 major version, one governed major-version line, no simultaneous v15/v16 support); the version-governance policy (centrally governed minor/patch selection subject to official support status, compatibility validation, controlled testing, rollback procedures, and Docker/CI/deployment reconciliation); and the required downstream revalidation (ERPNext Fit Analysis, ERPNext Gap Analysis, ERPNext DocType Mapping, Technology Stack Blueprint, deployment/environment documentation, Docker and CI configuration — none of it performed by this task). Implementation Authorization recorded as Not Granted; the disposition removes only the AR-001 version-selection blocker and does not authorize implementation, does not make any roadmap workstream Implementation-ready, does not publish any document, and does not resolve AR-002 through AR-011, all of which remain unchanged (AR-002 remains Open / Critical / Blocks Development: Yes). Options A, B, and C preserved unchanged as historical alternatives. ADR-001 was not modified. |
| 0.4 | 2026-07-28 | AR-002 Resolved — Project Owner Disposition | AR-002 formally resolved by Project Owner selection of Option A on 2026-07-28. Section 2 Review Summary row updated: Status Open → Resolved, Blocks Development Yes → No, Priority unchanged at Critical. Added a Disposition block to the AR-002 entry recording Accepted ADR-015-Tenant-Company-Multi-Tenancy-Model (Version 1.0) as the resolving decision — a companion to, not a supersession of, ADR-006-MultiTenant-Strategy, which remains Accepted — and `docs/blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published) as the architecture elaboration. Recorded the adopted decision: one isolated Frappe site and one isolated operational database per print-shop Tenant; the formal Tenant/Company distinction (Tenant as subscription/site/database/isolation/backup/upgrade-rollout/operational-management unit; Company as the ERPNext legal/accounting entity inside a Tenant, one-or-more per Tenant, not synonymous, not the isolation boundary); the infrastructure principles (host sharing permitted, operational-database sharing prohibited, shared governed code/Docker/CI/CD permitted, permanent Tenant-specific forks prohibited, centrally governed upgrades); the Job Card Tier A consequence (Company-scoped, implicit site identity, no Tenant field by default, no dependency on global Customer identity or cross-tenant analytics); and the explicit deferral list (global Customer directory, cross-tenant Customer matching, centralized transaction storage, cross-tenant analytics, data warehouse/lake, cross-shop visibility, consent/identity-resolution/retention architecture, exact RPO/RTO/pricing/quota/SLA values, detailed provisioning/operational implementation). Reconciled the Dependencies section to reflect that ADR-015, Blueprint 25, and Naming Registry §27 item 11 are no longer absent/unresolved, while preserving `04_MultiTenant_Architecture.md` as an unreconciled working draft (reconciliation deferred to a separate later task) and preserving all historical Background/Current Situation/Why-Review-Is-Required/Impact/Options A-B-C/Required-Decision content unchanged. Implementation Authorization recorded as Not Granted; the disposition is an architecture decision only and does not authorize tenant provisioning, deployment/infrastructure implementation, Docker/CI changes, schema/migration work, or Job Card Tier A coding — Published coding specifications and a separate scoped implementation-authorization decision remain required; every roadmap workstream remains Not Authorized; the Full Architecture Freeze remains unachieved. AR-001 and AR-003 through AR-011 unchanged. No other AR resolved, reprioritized, or created. |
| 0.5 | 2026-09-19 | AR-003 Resolved — Project Owner Disposition | AR-003 formally resolved by Project Owner selection of Option A on 2026-09-19. Section 2 Review Summary row updated: Status Open → Resolved, Blocks Development Partial → No, Priority unchanged at High. Added a Disposition block recording the confirmed per-term disposition of all eight requested terms (Print Specification, Approval Management, Production Workflow, Machine Management, Finishing, Quality Control, AI Assistant, Production Orchestration), citing new Naming Registry Section 40 as the durable disposition table. Seven terms are mapped, confirmed, or rejected against existing Approved concepts; AI Assistant is registered as Proposed only, with no Bounded Context, architecture, provider, model, plugin design, or implementation authorization, and remains excluded from implementation-ready scope pending separate future governance. AR-004 (Machine object base) and AR-009 (Quality Module Status) are explicitly confirmed unchanged and not resolved by this disposition. Implementation Authorization recorded as Not Granted. AR-001, AR-002, and AR-004 through AR-011 unchanged. No other AR resolved, reprioritized, or created. |
