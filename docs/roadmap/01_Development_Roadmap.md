# PrintHub Development Roadmap

Title:
PrintHub Development Roadmap

Version:
1.3

Status:
Approval

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-19

---

## 1. Purpose

This document is the initial governed **PrintHub Development Roadmap**. Its role is to be the **governed bridge between frozen architecture and future execution planning** — nothing more and nothing less.

It translates the effective Layered Architecture Freeze ([../implementation/Architecture_Freeze.md](../implementation/Architecture_Freeze.md), Approval 1.2) and the current Module Dependency Matrix ([../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md), Draft 0.5) into:

- **implementation workstreams** (capability groupings, taxonomy only);
- **dependency gates** (what must exist before a workstream can be built);
- **readiness states** (how ready a workstream is, conceptually and architecturally);
- **Architecture Review (AR) gates** (which open decisions constrain which workstreams);
- **traceability requirements** (how each workstream traces back to governing architecture);
- **deferred and excluded boundaries** (what is deliberately out of current governed scope).

This document is **not** an Implementation Plan, a Sprint Plan, a Release Checklist, or a delivery schedule. It contains no dates, durations, estimates, sprint counts, staffing, environments, or delivery commitments, and it authorizes no implementation. It is a planning-input artifact that downstream execution documents consume; it does not itself commit execution.

---

## 2. Document Authority and Governance Boundary

This roadmap is governed by, and subordinate to, the following authorities:

- **[../implementation/Architecture_Freeze.md](../implementation/Architecture_Freeze.md) — Approval, Version 1.2.** The effective Layered Architecture Freeze. It defines the Frozen conceptual baseline, the Conditional References, and the Excluded Layers. This roadmap operates strictly within the drafting latitude the Freeze grants (Freeze §18) and respects every exclusion it names (Freeze §10).
- **[../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — Draft, Version 0.5.** The current **conditional** dependency reference. It is the authoritative source for this roadmap's dependency-relationship interpretation. It is Draft; its AR-gated conclusions remain conditional.
- **Accepted ADRs** ([../decisions/](../decisions/)), in particular ADR-002 (PrintOS-Core), ADR-008 (MachineIQ), ADR-009 (Marketplace), ADR-014 (Production Terminology).
- **[../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md).** The single source of truth for AR-001 through AR-011. Reproduced verbatim in Section 10; never reinterpreted here.
- **[../Documentation_Workflow.md](../Documentation_Workflow.md).** The lifecycle and review governance this roadmap follows.

**This roadmap explicitly:**

- is **Draft**, and not safe to build against per `Documentation_Workflow.md` §5;
- does **not** resolve, close, reprioritize, or rename any Architecture Review item;
- does **not** promote or publish any source document;
- does **not** authorize implementation of any workstream;
- does **not** declare a Full Architecture Freeze — the Full Architecture Freeze remains **unachieved** (Freeze §21 exit criteria are unmet);
- is **constrained by** `Architecture_Freeze.md` and does **not** replace or amend it. Where this roadmap and the Freeze appear to differ, the Freeze governs.

---

## 3. Relationship to Other Planning Artifacts

The Development Roadmap is one of several planning artifacts. Their responsibilities are distinct and must not merge.

| Artifact | Owns | Must NOT absorb |
|---|---|---|
| **Product Roadmap** ([../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md)) | Strategic product direction; user-group progression (G1–G5); capability intent | Implementation authorization; build sequencing |
| **Development Roadmap** (this document) | Governed workstreams; dependency and readiness gates; AR exposure; conditional implementation-planning inputs | Tasks, owners, estimates, dates, sprints, release commitments |
| **Implementation Plan** ([../implementation/10_Project_Execution_Plan.md](../implementation/10_Project_Execution_Plan.md), Placeholder) | Future tasks, owners, estimates, environments, detailed sequencing, delivery controls | Redefining architecture or dependency model |
| **Sprint Plan** ([../implementation/13_Sprint_Strategy.md](../implementation/13_Sprint_Strategy.md), Placeholder) | Future short-horizon execution commitments | Any commitment beyond conditional readiness |
| **Release Checklist** ([../implementation/14_Release_Checklist.md](../implementation/14_Release_Checklist.md), Placeholder) | Release-readiness evidence | Roadmap readiness; workstream classification |

The Product Roadmap's Phase 1–5 sequence is **strategic context only** in this document; it is never used as an implementation stage (Freeze §17).

---

## 4. Legacy Planning Inputs

Two existing documents in `docs/implementation/` overlap this roadmap's subject matter. Both are **conditional legacy planning inputs**. Neither governs this roadmap; neither is modified, deprecated, or superseded by it.

- **[../implementation/01_Phase_1_Roadmap.md](../implementation/01_Phase_1_Roadmap.md) (Draft).** Technical implementation phases (Phase 0–6). This document **discloses**, without resolving, that `01_Phase_1_Roadmap.md` contains an **acknowledged phase-numbering conflict** — its technical Phase 0–6 numbering is demonstrably not the same sequence as the Blueprint Product Roadmap's business Phase 1–5. That legacy document also contains stale pre-resolution wording describing "AI Assistant" as unregistered; "AI Assistant" is now registered as Proposed only (Naming Registry Section 40, AR-003 Resolved) and remains unscoped and excluded pending separate future governance. This Development Roadmap **does not inherit** that phase numbering.
- **[../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md) (Draft).** Module build sequence. This document **discloses**, without resolving, that `02_Module_Implementation_Order.md` contains **dependency directions that conflict with the corrected Module Dependency Matrix** — specifically, its dependency diagram depicts `CRM → Sales → Estimation` and `Production Planning → Job Cards → Machine Scheduling`, whereas `Module_Dependency_Matrix.md` (v0.3) records the corrected directions **Estimation → Sales**, and Job Cards owning the work-unit contract with **Production Planning consuming** Job Cards and Machine Scheduling (per ADR-014).

**Authoritative dependency interpretation.** For its dependency model, this Development Roadmap follows the effective **`Architecture_Freeze.md`** and the current **`Module_Dependency_Matrix.md`** — not the legacy files. The conflicting legacy files remain **unchanged**.

**Deferred reconciliation.** Formal reconciliation or deprecation of `01_Phase_1_Roadmap.md` and `02_Module_Implementation_Order.md` is **deferred to a separate controlled governance task**. This roadmap neither rewrites nor corrects either legacy document, and it does not convert the disclosed conflicts into new architectural decisions.

Other planning artifacts — `10_Project_Execution_Plan.md`, `12_Project_Milestones.md`, `13_Sprint_Strategy.md`, `14_Release_Checklist.md` (all Placeholder), `docs/milestones/` (M00–M09), and the empty `docs/sprints/` — are downstream execution artifacts and do not govern this roadmap.

---

## 5. Roadmap Organization Model

This roadmap uses a **hybrid, dependency-tiered, capability-workstream model**:

- **dependency-tiered** — organized by what the Module Dependency Matrix establishes must exist before each capability, not by calendar or sprint;
- **capability-workstream based** — grouped into workstream families that cluster related capabilities;
- **non-temporal** — no dates, durations, or ordering-as-schedule;
- **readiness governed** — every workstream carries an explicit readiness and architecture-readiness classification.

**Workstream family names are taxonomy labels only.** They do **not** define, rename, or create any new:

- module;
- bounded context;
- entity;
- aggregate;
- DocType;
- Architecture Review decision.

Where a family label coincides with an approved module or context name (e.g., Estimation, Configuration Studio), it references that existing governed concept; it does not redefine it.

---

## 6. Dependency Model

This roadmap preserves the dependency-type vocabulary of `Module_Dependency_Matrix.md` §3:

- **Business Dependency** — requires a business output (record, approval, decision) from another module.
- **Implementation Dependency** — requires a data model/entity/domain concept to exist first.
- **ERPNext Dependency** — depends on a native or extended ERPNext object.
- **Configuration Dependency** — depends on a Configuration Studio artifact.
- **Plugin Dependency** — depends on an external provider abstraction via Plugin Architecture.
- **Architecture Review Dependency** — blocked or delayed by an open AR item.

It also preserves the three blocking distinctions plus optional dependency (Matrix §9):

- **Direct Blocker** — an AR item names that module's own entity/structural target directly.
- **Transitive Delay** — the module's own target is determined, but it sits downstream of a blocked module.
- **Convergent Blocking** — the module's own entity is not itself Pending, but two or more of its *own required inputs* are each directly blocked by separate AR items.
- **Optional Dependency** — a fuller implementation benefits from an input once available, but minimal implementation does not require it.

**Interpretation rules:**

- Dependency relationships **do not** constitute dates or sprint order (Matrix §5, §8, §15).
- **Parallel work may proceed** wherever prerequisites permit (Section 19).
- **Transitive delay must not be overstated** as a direct blocker; modules such as Dispatch, Accounts, and GST carry no AR item of their own.
- **Job Card Tier B (the operationally schedulable Job Card) is the principal convergence point** of the dependency graph, where AR-004, AR-005, and AR-006 consequences meet (Matrix §8; Section 15).

---

## 7. Roadmap Scope Classifications

The following exact classifications apply to every module/capability in the Scope Matrix (Section 12):

- **Stable Roadmap Scope** — supported by the Frozen Conceptual Baseline; may be structurally defined now, subject only to cross-cutting AR gates (e.g., AR-001 — resolved; technical revalidation pending).
- **Conditional Roadmap Scope** — may be drafted structurally, but AR-gated content cannot be finalized until the responsible AR is dispositioned.
- **Later-Phase Deferral** — deliberately deferred **beyond the current governed roadmap scope**. This term does **not** refer to Product Roadmap Phase 1–5 or technical Phase 0–6 numbering.
- **Excluded Pending Governance** — outside current governed scope pending a formal governance disposition; must not appear as implementation-ready.
- **Supporting Capability** — a capability that supports other workstreams rather than standing as an independent delivery of its own.
- **Not an Independent Workstream** — a capability folded into another workstream rather than tracked separately.

---

## 8. Architecture-Readiness Classifications

The following exact classifications describe the *architectural* readiness of a capability. They are **separate from** document lifecycle Status (Section 2), from workstream readiness (Section 9), and from implementation authorization (Section 9).

- **Frozen Conceptual Baseline** — the conceptual architecture is Frozen per `Architecture_Freeze.md` §7–8.
- **Conditional Architecture** — stable in concept but carrying AR-gated or Working-Assumption-dependent content.
- **Architecture Review Blocked** — a direct AR item names this capability's own entity/structural target.
- **Documentation Cleanup Required** — the blocker is terminology/documentation reconciliation, not a structural decision.
- **Governance Disposition Required** — outside AR coverage; needs a formal governance disposition before it can advance.
- **Later-Phase Unscoped** — deferred and not yet scoped in governed architecture.

---

## 9. Workstream Readiness and Authorization

### 9.1 Workstream Readiness States

- **Conceptually Ready** — the frozen architecture supports structural definition of the workstream now.
- **Conditional** — draftable, but AR-gated content within it cannot be finalized until the responsible AR is dispositioned.
- **Architecture Review Blocked** — a direct AR item names the workstream's own entity, so its core shape is genuinely unknown until that AR resolves.
- **Governance Blocked** — blocked by a governance gap outside the numbered AR register (e.g., no owner, no AR coverage).
- **Design Ready** — the gating decisions are dispositioned and detailed design may begin. *(No workstream is Design Ready in this Draft.)*
- **Deferred** — intentionally out of current governed scope (Later-Phase or Excluded).

### 9.2 Authorization Ladder

Authorization is a distinct axis from readiness. The rungs are:

1. **Roadmap-ready** — may appear as a workstream in this roadmap.
2. **Design-ready** — detailed design may begin (gating ARs dispositioned).
3. **Implementation-ready** — build design is complete and validated.
4. **Implementation-authorized** — a **separate later governance decision** authorizes build. **This roadmap never grants this rung.**
5. **Release-ready** — passes the Release Checklist evidence gates.

**Rules:**

- This roadmap may classify a workstream as Roadmap-ready, Design-ready, or Implementation-ready **only when evidence supports it**.
- This roadmap **never grants Implementation-authorized** status. Implementation authorization requires a separate later governance decision, after the applicable ARs and documentation prerequisites are satisfied (Freeze §19).
- **Release-ready** requires evidence assessed under the Release Checklist, not this roadmap.
- **Default implementation authorization for every workstream in this Draft: `Not Authorized`.**

---

## 10. Architecture Review Gates

The first five columns (**AR ID, Topic, Status, Priority, Blocks Development**) are reproduced **verbatim** from [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) §2 and are **not** reinterpreted, reclassified, resolved, or renamed. The remaining columns are this roadmap's gate interpretation for planning purposes only.

| AR ID | Topic | Status | Priority | Blocks Development | Affected Workstreams | Structural Drafting | Detail Finalization | Sequencing Finalization | Implementation Readiness | Permitted Assumption | Unresolved Decision |
|---|---|---|---|---|---|---|---|---|---|---|---|
| AR-001 | ERPNext Platform Version (15/16 conflict) | Resolved | Critical | No | All ERPNext-facing workstreams (cross-cutting) | Permitted | Not permitted (technical revalidation pending) | Not permitted | Not declarable | "Resolved — Option A selected. ERPNext v16 with the corresponding Frappe v16 major is the governed target, reaffirming Accepted ADR-001. Technical revalidation remains required; implementation remains unauthorized." | No remaining major-version decision. Fit, Gap, DocType, deployment, Docker, CI, hook, event, permission, and integration claims require technical revalidation against the governed target. |
| AR-002 | Multi-Tenant Strategy Alignment | Resolved | Critical | No | Tenant-Scoped Platform; tenant-scoped plugin config | Permitted | Not permitted (tenant runtime implementation) | Not permitted | Not declarable | "Resolved — Option A selected. One isolated Frappe site and one isolated operational database per Tenant is the governed topology; Tenant and Company are distinct, per Accepted ADR-015 (Version 1.0). Implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate scoped authorization remain required; implementation remains unauthorized." | No remaining topology or Tenant/Company decision. Tenant provisioning, tenant lifecycle operations, Tenant Override, and tenant-scoped plugin configuration require implementation design, Publication, and separate scoped implementation authorization. |
| AR-003 | Naming Registry Alignment (non-Approved module names, unregistered terms) | Resolved | High | No | AI Assistant (module-level; Proposed only) | Permitted except AI Assistant | Not permitted for AI Assistant | Not permitted for AI Assistant | Not declarable for AI Assistant | "Resolved — Option A selected 2026-09-19. Seven terms mapped/confirmed/rejected against existing Approved concepts; AI Assistant registered as Proposed only. No architecture, Bounded Context, provider, model, or implementation decision for AI Assistant; implementation remains unauthorized." | AI Assistant's architecture, Bounded Context, and capability design remain undecided, pending separate future governance. |
| AR-004 | Machine Domain Ownership | Open | High | Partial | Machine Scheduling (direct); Job Cards Tier B (convergent) | Permitted | Not permitted (Machine ownership) | Not permitted | Not declarable | "Machine base undetermined (Asset/Workstation/Custom/Hybrid)" | Which ERPNext object, if any, Machine is built upon |
| AR-005 | Quotation Strategy | Open | High | Partial | Estimation (direct); Sales, Production Planning, Job Cards (transitive) | Permitted | Not permitted (Quotation/Estimation boundary) | Not permitted | Not declarable | "Estimation → Sales frozen; document strategy open" | Quotation delivery strategy |
| AR-006 | Item vs. Material/Product Template Mapping | Open | High | Partial | Inventory, Estimation (direct); Purchasing (transitive); Job Cards (convergent) | Permitted | Not permitted (master-data ownership) | Not permitted | Not declarable | "Material/Substrate/Product Template mapping undetermined" | Item vs Material/Product Template mapping |
| AR-007 | Purchasing vs. Procurement Module Naming | Open | Medium | No | Purchasing / Procurement | Permitted | Permitted with dual-name caveat | Permitted | Terminology only | "Both names in use pending disposition" | Canonical term (Purchasing or Procurement) |
| AR-008 | Dispatch vs. Delivery Terminology | Open | Medium | No | Dispatch / Delivery | Permitted | Permitted | Permitted | Terminology only | "Context = Dispatch; DocType = Delivery Note" | Field/label term |
| AR-009 | Quality Module Status | Open | Medium | No | Job Cards (Quality Check Record) | Permitted | Permitted as Job Cards sub-feature | Permitted | No practical core block | "Sub-feature of Job Cards for now" | Standalone Quality module status |
| AR-010 | BOM Necessity for Estimation | Open | Medium | Partial | Estimation Engine internals | Permitted | Not permitted (engine internals) | Not required for structure | Delays final Estimation design | "BOM necessity unconfirmed" | Whether BOM is adopted for Estimation costing |
| AR-011 | CRM Enquiry vs. Opportunity Mapping | Open | Low | No | CRM (Enquiry behavior only) | Permitted | Permitted except Enquiry-specific behavior | Permitted | Delays Enquiry-only final design | "Customer proceeds; Enquiry mapping open" | Whether Enquiry needs modeling distinct from Opportunity |

No AR item above is resolved, closed, reprioritized, or renamed. No nonexistent Architecture Review identifier is introduced.

---

## 11. Workstream Taxonomy

The following workstream families are **taxonomy groupings only** — not modules, bounded contexts, entities, aggregates, DocTypes, or Architecture Review decisions.

- **Platform Foundation** — Clean Architecture scaffold, `printos_core` structure, Plugin Port contracts, and the foundational Configuration Studio subset.
- **ERPNext Foundation** — Administration, HR, Warehouse, and native ERPNext master/reference data.
- **Core Business Operations** — CRM, Sales, Artwork, Purchasing / Procurement, Dispatch / Delivery, Accounts, and GST.
- **Estimation** — the print-domain Estimation capability (references the approved Estimation module/context).
- **Production Operations** — Production Planning, Job Cards, and Machine Scheduling (three distinct workstreams, per ADR-014).
- **Configuration Studio** — the Configuration Studio designer catalog and configuration lifecycle. The primary approved term is **"Configuration Studio"** (not "Configuration Platform").
- **Integration and Plugins** — Plugin Ports and optional provider adapters.
- **Reporting and Analytics** — Reporting and Analytics as cross-cutting consumers.
- **Tenant-Scoped Platform** — *Governed topology established via AR-002 (Resolved).* This is a **taxonomy placeholder only**. **Tenant and Company are now distinct, Approved terms** (per Accepted ADR-015, Version 1.0, and Naming Registry §27 item 11, Resolved): one isolated Frappe site and one isolated operational database per Tenant, with Company as the ERPNext legal/accounting and business-scoping entity inside a Tenant. The label does not settle implementation design, provisioning, or operational tooling, none of which are authorized.
- **Deferred Product Capabilities** — MachineIQ, public-buyer Marketplace, AI Assistant, and the plugin/extension Marketplace entity cluster.
- **Quality Assurance and Governance** — cross-cutting validation and governance discipline. This label means **QA/validation-and-governance discipline**; it is **not** the pending standalone Quality module governed by AR-009.

All family labels above are **roadmap taxonomy only**.

---

## 12. Roadmap Scope Matrix

Every row carries **Implementation Authorization: Not Authorized**. Roadmap treatments are limited to: Define Workstream, Define Conditional Workstream, Reference as Dependency, Defer, Exclude, Include as Cross-Cutting Concern.

| Module / Capability | Workstream Family | Scope Classification | Architecture Readiness | AR Exposure | Roadmap Treatment | Current Readiness | Implementation Authorization | Notes |
|---|---|---|---|---|---|---|---|---|
| Administration | ERPNext Foundation | Stable Roadmap Scope | Frozen Conceptual Baseline | AR-001 resolved; v16 technical revalidation pending | Define Workstream | Conceptually Ready | Not Authorized | Root of dependency graph; no module-specific AR |
| HR | ERPNext Foundation | Stable Roadmap Scope | Frozen Conceptual Baseline | AR-001 resolved; v16 technical revalidation pending | Define Workstream | Conceptually Ready | Not Authorized | Administration-dependent |
| Warehouse | ERPNext Foundation | Stable Roadmap Scope | Frozen Conceptual Baseline | AR-001 resolved; v16 technical revalidation pending | Define Workstream | Conceptually Ready | Not Authorized | Foundation-tier; Administration-dependent |
| CRM | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-011 (Enquiry only); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Conditional | Not Authorized | Customer stable; Enquiry behavior open |
| Estimation | Estimation | Conditional Roadmap Scope | Architecture Review Blocked | AR-005, AR-006, AR-010; AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Architecture Review Blocked | Not Authorized | "Key differentiator"; directly blocked on core entities |
| Sales | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-005 (transitive); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Conditional | Not Authorized | Reorder path independent of Estimation standard path |
| Artwork | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-003 (non-blocking); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Conditional | Not Authorized | Transitively delayed via Sales standard path only |
| Inventory | Core Business Operations | Conditional Roadmap Scope | Architecture Review Blocked | AR-006 (direct); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Architecture Review Blocked | Not Authorized | AR-006 widest blast radius |
| Purchasing / Procurement | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-006 (transitive), AR-007 (naming); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Conditional | Not Authorized | Core PO/Supplier stable; replenishment logic delayed; both names retained |
| Production Planning | Production Operations | Conditional Roadmap Scope | Conditional Architecture | AR-004/005/006 (transitive/convergent via inputs) | Define Conditional Workstream | Conditional | Not Authorized | Distinct module (ADR-014); consumes, does not create Job Card |
| Job Cards | Production Operations | Conditional Roadmap Scope (Tier A / Tier B gates) | Conditional Architecture | AR-004/006 (convergent, Tier B), AR-005 (transitive), AR-009 | Define Conditional Workstream | Conceptually Ready (Tier A) / Conditional (Tier B) | Not Authorized | One aggregate, one DocType; tiers are readiness gates |
| Machine Scheduling | Production Operations | Conditional Roadmap Scope | Architecture Review Blocked | AR-004 (direct) | Define Conditional Workstream | Architecture Review Blocked | Not Authorized | Directly blocked on Machine / Machine Profile |
| Dispatch / Delivery | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-008 (naming); AR-001 resolved, v16 technical revalidation pending | Define Conditional Workstream | Conditional | Not Authorized | Target determined (Delivery Note, Extended); transitively delayed via Job Cards |
| Accounts | Core Business Operations | Conditional Roadmap Scope | Conditional Architecture | AR-001 resolved, v16 technical revalidation pending; transitive via Dispatch | Define Conditional Workstream | Conditional | Not Authorized | Advance-billing path enables early testability |
| GST | Core Business Operations | Supporting Capability | Conditional Architecture | AR-001 resolved, v16 technical revalidation pending; Blueprint open question (fold into Accounts) | Include as Cross-Cutting Concern | Conditional | Not Authorized | Tightly coupled to Accounts; not an independent workstream |
| Reporting | Reporting and Analytics | Supporting Capability | Frozen Conceptual Baseline | None | Include as Cross-Cutting Concern | Conceptually Ready | Not Authorized | Consumes Config Studio Published definitions; gates nothing |
| Analytics | Reporting and Analytics | Supporting Capability (basic) / Later-Phase Deferral (advanced) | Frozen (basic) / Later-Phase Unscoped (advanced) | None | Include as Cross-Cutting Concern / Defer (advanced) | Conceptually Ready (basic) / Deferred (advanced) | Not Authorized | Basic in scope; advanced/predictive deferred to MachineIQ |
| Configuration Studio | Configuration Studio | Supporting Capability (foundational + parallelizable subsets) | Conditional Architecture | AR-002 (Resolved; Tenant Override implementation only) | Define Workstream (foundational) / Reference as Dependency (designers) | Conceptually Ready (foundational) / Conditional (tenant-aware) | Not Authorized | Non-tenant Configuration Studio baseline is frozen; Tenant Override and tenant-aware runtime behavior remain conditional pending implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate authorization — the governed Tenant/Company topology itself is resolved (AR-002, Accepted ADR-015). Not monolithic; see Section 16 |
| Plugin Architecture | Integration and Plugins | Stable Roadmap Scope (Ports); adapters optional | Frozen Conceptual Baseline | AR-002 (Resolved; tenant-scoped config implementation only) | Define Workstream (Ports) / Reference as Dependency (adapters) | Conceptually Ready (Ports) | Not Authorized | No plugin category is a blocker |
| MachineIQ | Deferred Product Capabilities | Later-Phase Deferral | Later-Phase Unscoped | AR-004 (indirect); ADR-008 scope deferred | Defer | Deferred | Not Authorized | Platform ships completely without it |
| Marketplace (public-buyer) | Deferred Product Capabilities | Later-Phase Deferral | Later-Phase Unscoped | ADR-009 scope deferred | Defer | Deferred | Not Authorized | Governed by ADR-009; business-sequenced |
| Marketplace plugin/extension entity cluster | Deferred Product Capabilities | Excluded Pending Governance | Governance Disposition Required | No AR coverage (Freeze §14) | Exclude | Governance Blocked | Not Authorized | Distinct from public Marketplace; no owner; naming-collision risk |
| AI Assistant | Deferred Product Capabilities | Excluded Pending Governance | Governance Disposition Required | AR-003 (Resolved; Proposed name only) | Exclude | Governance Blocked | Not Authorized | Registered as Proposed (AR-003 Resolved, 2026-09-19); excluded pending separate future governance for architecture, Bounded Context, provider, and model — not by unresolved naming |

---

## 13. Stable Workstream Definitions

Workstreams supported by the Frozen Conceptual Baseline may be described more fully. No tasks, estimates, dates, owners, sprint order, or detailed technical design appear here.

### 13.1 Platform Foundation

- **Purpose:** Establish the architectural substrate on which every business workstream is built.
- **Included capabilities:** Clean Architecture scaffold (`domain/`, `application/`, `infrastructure/`, `interface/`); `printos_core` app structure per ADR-002; Plugin Port contract definitions; the foundational Configuration Studio subset (Feature Flag, Automation Rule, Module Definition).
- **Typed dependencies:** Implementation (Clean Architecture layering); Configuration (Feature Flag, Automation Rule, Module Definition are hard prerequisites for feature-gating/automation/module enable-disable).
- **Architecture prerequisites:** Frozen technical/architecture layer (`architecture/02_Clean_Architecture.md`, `architecture/03_DDD_Architecture.md`, `architecture/05_Extensibility_Architecture.md`); `Plugin_Architecture.md`.
- **AR exposure:** AR-001 (cross-cutting, ERPNext-facing elements only) — Resolved; technical revalidation pending; AR-002 for tenant-scoped plugin configuration implementation only — Resolved (governed topology established via Accepted ADR-015; implementation pending).
- **Current readiness:** Conceptually Ready.
- **Deliverable categories:** architecture scaffold; Port contract catalog; foundational configuration capability definitions.
- **Validation / exit-gate categories:** layering-conformance validation; Port-contract completeness; circular-dependency detection for Automation Rule and Module Definition graphs.
- **Deferred items:** provider adapters (Section 17); tenant-aware configuration implementation (AR-002 topology resolved; implementation design, Publication, and authorization pending).
- **Traceability:** ADR-002; `architecture/02`–`03`, `05`; `Plugin_Architecture.md`; `Configuration_Studio_Architecture.md`; Matrix §5, §11–12.
- **Implementation authorization:** Not Authorized.

### 13.2 ERPNext Foundation

- **Purpose:** Establish the low-risk foundation layer that carries no module-specific AR exposure.
- **Included capabilities:** Administration (Company, Branch); HR (Employee, Department); Warehouse; native ERPNext reference/master data (UOM, Currency, Tax Template, Payment Terms, Supplier, Purchase Order structure).
- **Typed dependencies:** Business/Implementation (HR and Warehouse depend on Administration); ERPNext (native objects).
- **Architecture prerequisites:** Frozen bounded-context and module structure (`blueprint/06_Bounded_Contexts.md`, `blueprint/09_PrintOS_Modules.md`); `ERPNext_DocType_Mapping.md` definite-owner rows.
- **AR exposure:** AR-001 cross-cutting only (ERPNext version-sensitive) — Resolved; technical revalidation pending. No module-specific AR.
- **Current readiness:** Conceptually Ready. **Governed target:** ERPNext v16 per AR-001 (Resolved, Option A), reaffirming Accepted ADR-001; final ERPNext-facing implementation still awaits capability-level technical revalidation (Matrix §5).
- **Deliverable categories:** foundation master-data setup definitions; Administration/HR/Warehouse structural scope.
- **Validation / exit-gate categories:** foundation master-data validation; native-object conformance (subject to technical revalidation against the governed v16 target).
- **Deferred items:** none specific; ERPNext-facing finalization pending capability-level technical revalidation.
- **Traceability:** `blueprint/09_PrintOS_Modules.md`; `ERPNext_DocType_Mapping.md`; `ERPNext_Fit_Analysis.md`; Matrix §5, §7.
- **Implementation authorization:** Not Authorized.

### 13.3 Configuration Studio (foundational subset) — see Section 16 for full decomposition

- **Purpose:** Provide the platform configuration capabilities that have no native ERPNext fallback.
- **Included capabilities (foundational):** Feature Flag, Automation Rule, Module Definition.
- **Typed dependencies:** Configuration (this is the configuration layer itself).
- **Architecture prerequisites:** `Configuration_Studio_Architecture.md` (frozen with tenant-runtime slice excluded).
- **AR exposure:** AR-002 for Tenant Override implementation only — Resolved (governed topology established via Accepted ADR-015, Version 1.0; implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate authorization pending); the foundational subset is unblocked.
- **Current readiness:** Conceptually Ready (foundational subset).
- **Deliverable / validation categories:** configuration artifact definitions; mandatory circular-reference detection (Automation Rule, Module Definition).
- **Deferred items:** designer catalog (parallelizable, Section 16); Tenant Override implementation (AR-002 topology resolved; not required for Job Card Tier A).
- **Traceability:** `Configuration_Studio_Architecture.md`; `Canonical_Domain_Model.md`; `ERPNext_DocType_Mapping.md`; Matrix §11.
- **Implementation authorization:** Not Authorized.

### 13.4 Integration and Plugins (Ports) — see Section 17

- **Purpose:** Define provider-agnostic Plugin Port contracts independent of any business module.
- **Included capabilities:** Plugin Port contract definitions across categories (Email, Payment, Shipping, Storage, Identity, Industrial, Analytics/BI).
- **Typed dependencies:** none — a Port's contract does not depend on which module calls it first (Matrix §10).
- **AR exposure:** AR-002 for tenant-scoped plugin configuration implementation only — Resolved (governed topology established; implementation pending).
- **Current readiness:** Conceptually Ready (Ports). Adapters optional, requirement-driven.
- **Traceability:** `Plugin_Architecture.md`; Matrix §12.
- **Implementation authorization:** Not Authorized.

### 13.5 Reporting and Analytics — Cross-Cutting Supporting Capabilities

Reporting and Analytics are **Supporting Capabilities included as cross-cutting concerns**, not an independent stable module workstream and not a new module or bounded context. Their scope-matrix classification (Section 12) is unchanged. See Section 16 for the Configuration Studio ownership relationship.

- **Purpose:** Provide reporting and basic analytics as consumers of transactional data and Configuration Studio Published definitions.
- **AR exposure:** None. **Current readiness:** Conceptually Ready (basic). Advanced/predictive analytics deferred (Section 18).
- **Traceability:** `blueprint/09_PrintOS_Modules.md`; `Configuration_Studio_Architecture.md`; Matrix §7.
- **Implementation authorization:** Not Authorized.

---

## 14. Conditional Workstream Definitions

Structural-only entries. AR-gated design gaps are **not** filled with recommendations, and no assumption is converted into a decision.

### 14.1 Estimation

- **Purpose:** Deliver print-domain cost estimation (substrate/finishing/machine-time costing, multi-version quoting).
- **Stable conceptual content:** Estimation → Sales direction (frozen); non-blocked reference data (Job Types, Finishing Types, Paper Sizes).
- **Conditional content:** Quotation/Quotation Line and Product Template ownership; Estimation Engine internal cost-breakdown approach.
- **Direct AR gates:** AR-005 (Quotation strategy), AR-006 (Material/Product Template).
- **Transitive/convergent gates:** AR-010 (BOM necessity — Engine internals); AR-001 (cross-cutting) — Resolved; technical revalidation pending.
- **Permitted Working Assumptions:** Estimation → Sales boundary as frozen. ERPNext v16 is no longer an assumption — it is the AR-001-governed target (reaffirming Accepted ADR-001); capability-level Estimation claims remain conditional pending technical revalidation.
- **Prohibited finalization:** Quotation delivery strategy; Material/Product Template mapping; whether BOM is adopted.
- **Traceability:** `blueprint/05_Domain_Model.md`; `blueprint/06_Bounded_Contexts.md`; AR-005/006/010; ADR-013; `ERPNext_DocType_Mapping.md` (Pending rows); Matrix §7.
- **Implementation authorization:** Not Authorized.

### 14.2 CRM

- **Purpose:** Customer and enquiry-stage relationship management.
- **Stable conceptual content:** Customer (Extended ERPNext) proceeds.
- **Conditional content:** Enquiry behavior distinct from Opportunity.
- **Direct AR gates:** AR-011 (Enquiry only). **Cross-cutting:** AR-001 — Resolved; technical revalidation pending.
- **Permitted Working Assumptions:** Customer as Extended ERPNext.
- **Prohibited finalization:** Enquiry-specific modeling.
- **Traceability:** `blueprint/05_Domain_Model.md`; AR-011; Matrix §7.
- **Implementation authorization:** Not Authorized.

### 14.3 Sales / Artwork / Dispatch / Accounts (transitive)

- **Purpose:** Core order-to-cash operational modules whose own targets are determined but sit downstream of blocked upstreams.
- **Stable conceptual content:** Sales Order (Extended); Sales reorder path; Artwork approval gate; Delivery Note (Extended) for Dispatch; native Accounts objects; advance-billing path.
- **Conditional content:** standard-path completion transitively delayed via Estimation (AR-005) and the Job Cards convergence.
- **Direct AR gates:** none of their own. **Transitive:** AR-005 (Sales/Artwork standard path); via Job Cards for Dispatch. **Naming:** AR-008 (Dispatch/Delivery, non-blocking).
- **Permitted Working Assumptions:** native/extended objects as classified (pending technical revalidation). ERPNext v16 is no longer an assumption — it is the AR-001-governed target (reaffirming Accepted ADR-001).
- **Prohibited finalization:** anything dependent on the unresolved Quotation/Job Card convergence for the standard path.
- **Traceability:** `blueprint/06_Bounded_Contexts.md`; `blueprint/10_Business_Workflows.md`; AR-005/008; Matrix §7, §9.
- **Implementation authorization:** Not Authorized.

### 14.4 Inventory / Purchasing / Procurement

- **Purpose:** Material master data and replenishment/procurement.
- **Stable conceptual content:** UOM; Media Profiles; core Purchase Order/Supplier plumbing (native).
- **Conditional content:** Material/Substrate ownership (Inventory); replenishment-trigger logic (Purchasing).
- **Direct AR gates:** AR-006 (Inventory, Material/Substrate). **Transitive:** AR-006 via Material (Purchasing). **Naming:** AR-007 (Purchasing/Procurement, non-blocking).
- **Permitted Working Assumptions:** native PO/Supplier as classified (pending technical revalidation). ERPNext v16 is no longer an assumption — it is the AR-001-governed target (reaffirming Accepted ADR-001).
- **Prohibited finalization:** Item vs Material/Product Template mapping; the canonical Purchasing/Procurement term.
- **Traceability:** `blueprint/08_Master_Data_Model.md`; AR-006/007; `ERPNext_DocType_Mapping.md`; Matrix §7, §14.
- **Implementation authorization:** Not Authorized.

### 14.5 Production Operations (Production Planning, Job Cards, Machine Scheduling)

See Section 15 for the full Job Card / Production model. Summary:

- **Direct AR gates:** AR-004 (Machine Scheduling — Machine/Machine Profile).
- **Convergent gates:** AR-004 + AR-006 converge on Job Cards Tier B.
- **Transitive gates:** AR-005 via Sales Order standard path; AR-009 (Quality Check Record, non-blocking).
- **Prohibited finalization:** Machine ownership design; operational (Tier B) Job Card design; anything requiring the convergent inputs.
- **Implementation authorization:** Not Authorized.

### 14.6 Tenant-Scoped Platform

- **Purpose:** *Taxonomy placeholder only* for tenant-aware platform behavior.
- **Conditional content:** implementation of the governed topology. **AR-002 is Resolved** (Option A, Accepted ADR-015, Version 1.0): one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company. The label settles no implementation design, provisioning, or operational tooling — none of which is authorized.
- **Direct AR gates:** AR-002 (Resolved; implementation design, Publication of `25_MultiTenant_Architecture.md` — currently Approval, Version 1.0, not Published — and separate scoped authorization remain required). **Prohibited finalization:** tenant provisioning and deployment mechanisms; Tenant Override implementation; tenant-scoped configuration and plugin implementation. Central customer identity and cross-tenant analytics remain explicitly deferred pending separate future governance and are not authorized by AR-002's resolution.
- **Traceability:** ADR-006; ADR-015; AR-002; Naming Registry §27 item 11 (Resolved); `blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published); `architecture/04_MultiTenant_Architecture.md` (working draft, unreconciled, Excluded); Matrix §11.
- **Implementation authorization:** Not Authorized.

---

## 15. Job Card and Production Operations

**Official model, preserved (Freeze §15; ADR-014; `ERPNext_DocType_Mapping.md`):**

- there is **one PrintHub Job Card aggregate**;
- there is **one custom PrintHub Job Card DocType**;
- **Tier A and Tier B are readiness levels, not separate entities, aggregates, or DocTypes**;
- the PrintHub Job Card **does not reuse ERPNext's Manufacturing Job Card**, per ADR-014.

**Tier A — work-unit contract / scaffolding.**
- The minimum custom PrintHub Job Card model/interface required so Machine Scheduling and Production Planning can reference a production work unit.
- Prerequisites: **Sales Order** (confirmed) and **Approved Artwork**.
- May be **structurally detailed under the Frozen Conceptual Baseline**; it does not depend on Production Planning or Machine Scheduling to exist. Current readiness: **Conceptually Ready**.

**Tier B — operationally schedulable Job Card.**
- The fully usable, executable Job Card.
- Requires: **inventory allocation**, **machine assignment**, **Production Planning output** (Scheduled Job Card), and **Machine Scheduling**.
- Remains **Conditional** — the convergence point where **AR-004** (Machine, via Machine Scheduling), **AR-006** (Material, via allocation), and **AR-005** (Quotation, transitive via Sales Order) meet. This is **convergent blocking**, not direct blocking of the Job Card entity.

**Distinct workstreams (per ADR-014), no circular dependency introduced:**

- **Job Cards** own the work-unit contract.
- **Machine Scheduling** provides machine assignment against the Job Card contract and Machine Profile.
- **Production Planning** **consumes** Job Cards and Machine Scheduling inputs and **produces Scheduled Job Cards**; it **does not create the Job Card**.

**Traceability:** ADR-014; `blueprint/09_PrintOS_Modules.md`; `Canonical_Domain_Model.md`; `ERPNext_DocType_Mapping.md` (single Job Card row); AR-004/005/006/009; Matrix §6–9.

---

## 16. Configuration Studio Treatment

Configuration Studio is **not** a single monolithic prerequisite (Freeze §7; Matrix §11). Four treatments:

- **Foundational (no native fallback):** Feature Flag, Automation Rule, Module Definition — true hard prerequisites for feature-gating, safe automation, and module enable/disable.
- **Parallelizable through native Frappe/ERPNext fallback:** Workflow Designer, Approval Designer, Form Designer, Dashboard Designer, Report Designer, Notification Designer, Integration Designer — each configures an existing native mechanism usable directly first.
- **AR-002-dependent:** Tenant Override and tenant-aware configuration behavior — gated by AR-002.
- **Later maturity:** advanced lifecycle governance, validation sophistication, versioning, audit, import/export, deployment, dependency management.

**Clarifications:**

- **Reporting consumes** Published Report Definitions and Dashboard Definitions; **Configuration Studio owns** those definitions (Freeze §7).
- **Native fallback** may allow business workstreams to proceed on the raw native mechanism.
- **Later migration** from raw-native configuration to governed Configuration Studio definitions is **not free** — it may require governance, validation, and conversion work (Matrix §11).

No implementation tasks are provided.

---

## 17. Plugin and Integration Treatment

- **No plugin category is a current foundational blocker** (Freeze §7; Matrix §12).
- **Plugin Ports may be defined independently** of any business module (Matrix §10).
- **Provider adapters are optional until a real requirement exists.**
- **Native fallback** may serve where governed sources permit (e.g., native Frappe SMTP for Email; manual Payment Entry before Payment Providers).

| Category | Roadmap treatment | Fallback / note |
|---|---|---|
| Email | Port definable; adapter optional | Native Frappe or ERPNext SMTP may serve as the initial governed fallback until a provider-specific adapter is required |
| Payment Providers | Port definable; adapter optional | Manual Payment Entry first |
| Shipping | Port definable; adapter optional | Additive |
| Storage | Port definable; adapter optional | Native File capability may be sufficient at the initial governed operating scale until an external storage requirement is established |
| Identity | Port definable; adapter optional | Additive; risk of speculative build |
| Industrial integrations | Port definable; adapter optional | Additive (MachineIQ-adjacent) |
| Analytics / BI | Port definable; adapter optional | Additive |

**Risk:** speculative adapter construction ahead of a real requirement (Matrix §14). Ports are roadmap-definable; adapters are optional or requirement-driven.

---

## 18. Later-Phase and Excluded Capabilities

**Later-Phase Deferral** (deferred beyond current governed roadmap scope; not a Product/technical phase number):

- **MachineIQ** — additive; scope deferred per ADR-008; platform ships without it.
- **Public-buyer Marketplace** — governed by ADR-009; business-sequenced.
- **Advanced or predictive Analytics** — deferred to MachineIQ.

**Excluded Pending Governance** (must **not** appear as implementation-ready):

- **Marketplace Package, Marketplace Template, Extension, Publisher, Marketplace Package Version**, and other plugin/extension Marketplace cluster entities;
- **AI Assistant** — registered as Proposed (AR-003 Resolved, 2026-09-19); architecture, Bounded Context, and capability design remain pending separate future governance.

**Clarifications (Freeze §14):**

- the **plugin/extension Marketplace cluster is distinct** from the public-buyer Marketplace governed by ADR-009;
- it currently has **no formal implementation owner** and **no numbered AR coverage**;
- it **must not appear as implementation-ready** and requires a formal governance disposition before Full Architecture Freeze.

No new governance decision is created here.

---

## 19. Parallelization and Convergence

Structurally permitted parallel work (Matrix §10), stated **without** scheduling or numbered build order:

- foundation architecture (Platform Foundation; Administration + HR + Warehouse);
- Plugin Port definitions;
- native-fallback Configuration Studio designer work;
- non-blocked Estimation/Production reference data (Job Types, Finishing Types, Paper Sizes, Media Profiles);
- Sales **reorder path**;
- Accounts/GST **advance-billing** paths.

**Principal convergence point:** the **operationally schedulable Job Card (Tier B)** (Matrix §8).

The four dependency distinctions are preserved and not converted into build order:

- **Direct blocking** — Estimation, Inventory, Machine Scheduling, Configuration Studio Tenant Override, AI Assistant.
- **Transitive delay** — Sales, Artwork, Production Planning (standard path), Dispatch, Accounts, GST, Purchasing replenishment.
- **Convergent blocking** — Job Cards Tier B.
- **Optional dependency** — provider adapters; Configuration designers via native fallback.

---

## 20. Traceability Model

Each workstream carries a **compact mandatory traceability block**. Minimum required references:

- governing **Blueprint module or workflow** (`blueprint/09_PrintOS_Modules.md`, `blueprint/10_Business_Workflows.md`);
- relevant **bounded context** (`blueprint/06_Bounded_Contexts.md`);
- **Module Dependency Matrix** entry;
- relevant **AR IDs**;
- governing **ADRs**;
- **DocType mapping disposition** (`database/ERPNext_DocType_Mapping.md`).

Additional references where applicable: `architecture/Canonical_Domain_Model.md`; `architecture/ERPNext_Fit_Analysis.md`; `architecture/ERPNext_Gap_Analysis.md`; Configuration Studio requirement; Plugin Port; `standards/Naming_Registry.md`; testing/acceptance category (`implementation/06_Testing_Strategy.md`).

**Rules:**

- this roadmap **references** architecture; it does **not redefine** it;
- it must **not resolve Pending terms inline**;
- downstream documents **must not overwrite upstream decisions** (the Documentation Hierarchy, `Documentation_Workflow.md` §3).

---

## 21. Workstream Entry Schema

The standard schema for later, fuller roadmap entries (not populated at task level in this Draft):

- Workstream
- Capability
- Purpose
- Scope
- Scope Classification (Section 7)
- Architecture Readiness (Section 8)
- Current Readiness (Section 9)
- Dependencies by type (Section 6)
- Architecture Prerequisites
- AR Gates
- ERPNext Dependencies
- Configuration Dependencies
- Plugin Dependencies
- Conditional Assumptions (Working Assumptions, explicitly flagged)
- Deliverable Categories
- Validation Evidence
- Exit Criteria
- Deferred Items
- Risks
- Traceability (Section 20)
- Implementation Authorization (default: Not Authorized)

---

## 22. Roadmap Maintenance

- When an **AR changes status**, revisit **only the affected workstreams** (Matrix §13, §15), not the whole roadmap.
- **Substantive changes** re-enter the documentation lifecycle at **Draft** with a version increment (`Documentation_Workflow.md` §5, §8).
- **Legacy planning reconciliation** (Section 4) remains a **separate controlled governance task**.
- **Product Roadmap changes do not automatically authorize Development Roadmap changes.**
- The roadmap must remain **synchronized with `Architecture_Freeze.md` and `Module_Dependency_Matrix.md`**.
- **Detailed execution** moves downstream into the Implementation Plan, milestones, and sprint artifacts.

---

## 23. Explicit Exclusions

This Draft does **not** define, decide, or authorize any of the following:

- dates; durations; sprint counts; estimates; delivery owners; staffing; environments; release commitments;
- implementation authorization;
- capability-level ERPNext technical revalidation (AR-001 is Resolved — ERPNext v16/Frappe v16 governed target, reaffirming Accepted ADR-001 — but Fit, Gap, DocType, deployment, Docker, CI, hook, event, permission, and integration claims remain outside this synchronization and require separate revalidation);
- tenant implementation design, provisioning, and operational tooling (AR-002 is Resolved — governed topology and Tenant/Company distinction established via Accepted ADR-015; implementation, Publication of `25_MultiTenant_Architecture.md`, and separate scoped authorization remain outside this synchronization);
- Machine ownership (AR-004);
- Quotation strategy (AR-005);
- Material/Product Template ownership (AR-006);
- BOM decision (AR-010);
- unresolved Enquiry behavior (AR-011);
- formal deprecation of legacy planning artifacts.

---

## 24. Open Governance Items

Listed without resolution and without introducing any new identifier:

- **AR-004 through AR-011** — all Open (Section 10; register verbatim). **AR-001** — Resolved (Option A; ERPNext v16/Frappe v16 governed target, reaffirming Accepted ADR-001); capability-level technical revalidation remains outstanding and is outside this roadmap's scope. **AR-002** — Resolved (Option A; one isolated Frappe site and operational database per Tenant, Tenant distinct from Company, per Accepted ADR-015, Version 1.0); implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate scoped authorization remain outstanding and are outside this roadmap's scope. **AR-003** — Resolved (Option A, 2026-09-19; seven terms mapped/confirmed/rejected against existing Approved concepts, AI Assistant registered as Proposed only); AI Assistant's architecture, Bounded Context, provider, and model remain outstanding, pending separate future governance, and are outside this roadmap's scope.
- **Phase-numbering conflict** in `implementation/01_Phase_1_Roadmap.md` — acknowledged, unresolved.
- **Dependency-direction conflict** in `implementation/02_Module_Implementation_Order.md` versus the corrected Module Dependency Matrix — disclosed, unresolved.
- **Roadmap index creation** — eventual, if `docs/roadmap/` gains multiple governed artifacts (the `00_` slot is reserved; no index created here).
- **Formal legacy-document reconciliation or deprecation** — deferred to a separate controlled governance task.
- **Naming Registry and Blueprint Open Questions** relevant to roadmap terminology — e.g., Purchasing/Procurement (AR-007), Dispatch/Delivery (AR-008), Machine Scheduling context boundary, GST-into-Accounts (`blueprint/06_Bounded_Contexts.md` Open Questions). Tenant/Company (Naming Registry §27 item 11) is Resolved via Accepted ADR-015; remaining tenant-scoped items are implementation, Publication, and authorization, not terminology.

---

## 25. Review Record

**Architecture Review:** Completed
**Disposition:** Accepted with non-blocking observations
**Review date:** 2026-07-26

**Result:**

- no Critical findings;
- no High findings;
- no architecture correction required before Business Review;
- non-blocking editorial observations deferred;
- no Architecture Review item was resolved;
- no implementation authorization was granted.

**Business Review:** Completed
**Disposition:** Accepted with non-blocking observations
**Review date:** 2026-07-26

**Result:**

- no Critical findings;
- no High findings;
- no business correction required before Project Owner review;
- the end-to-end PrintHub business operating model is represented accurately;
- core business capabilities and operational handoffs are proportionately represented;
- unresolved business decisions remain conditional;
- non-blocking editorial observations are deferred;
- no Architecture Review item or business-governance question was resolved;
- no implementation authorization was granted.

**Project Owner Approval:** Completed
**Decision:** Approved for the Approval lifecycle stage
**Approval date:** 2026-07-26
**Approved document baseline:** Version 1.0

**Approval scope:**

- the roadmap is approved as the governed bridge between the effective Layered Architecture Freeze and future execution planning;
- its dependency-tiered workstreams, readiness model, AR gates, traceability model, and conditional boundaries are approved as roadmap governance;
- the recorded Architecture Review and Business Review dispositions are accepted, including their non-blocking observations, which remain deferred.

**Explicit boundaries:**

- the roadmap is not Published;
- roadmap approval does not authorize implementation;
- every workstream remains Not Authorized;
- the Full Architecture Freeze remains unachieved;
- `Architecture_Freeze.md` continues to govern and constrain this roadmap;
- AR-003 through AR-011 remain Open and unresolved; AR-001 and AR-002 are Resolved (Option A each; technical revalidation and implementation-authorization prerequisites remain outstanding, respectively) as recorded in the synchronization notes below;
- Working Assumptions remain assumptions, not decisions, for every AR other than AR-001 and AR-002;
- no Machine ownership, Quotation strategy, Material/Product Template ownership, BOM strategy, or Enquiry model is decided by this roadmap (ERPNext major-version selection is resolved by AR-001, reaffirming Accepted ADR-001, capability-level technical revalidation remains outstanding; the multi-tenant topology and Tenant/Company distinction are resolved by AR-002, per Accepted ADR-015, implementation design/Publication/separate scoped authorization remain outstanding);
- no legacy planning artifact is deprecated;
- no date, estimate, sprint, milestone, staffing, or delivery commitment is approved.

**Implementation Authorization:** Not Granted. Roadmap approval is document-governance approval; it permits the roadmap to govern future planning and does not grant permission to build. Implementation authorization requires a separate later governance decision. Any workstream blocked or conditioned by an Architecture Review item remains blocked or conditional. This approval does not make any workstream Implementation-ready or Release-ready.

**Project Owner Synchronization Note (AR-001 Disposition):**

- **Synchronization approved:** 2026-07-26
- The Project Owner approved a factual synchronization of this roadmap from Approval Version 1.0 to Approval Version 1.1, recording the formally completed AR-001 disposition (Option A: ERPNext v16, with the corresponding Frappe v16 major, is the governed target, reaffirming Accepted ADR-001).
- No Architecture Review or Business Review was repeated for this synchronization, because no roadmap scope, dependency direction, readiness classification, workstream taxonomy, or business content changed — only AR-001's official-field status and derived interpretive wording were reconciled with its resolved disposition.
- The existing Architecture Review and Business Review dispositions recorded above remain valid and unchanged.
- No implementation authorization was granted by this synchronization; every workstream remains Not Authorized; capability-level technical revalidation against the governed v16 target remains outstanding and outside this synchronization's scope.

**Project Owner Synchronization Note (AR-002 Disposition):**

- **Approval type:** Factual baseline synchronization.
- **Authority:** Project Owner.
- **Synchronization approved:** 2026-07-28.
- **Approved transition:** Development Roadmap Approval Version 1.1 → Approval Version 1.2, recording the formally completed AR-002 disposition (Option A: one isolated Frappe site and one isolated operational database per Tenant; Tenant formally distinct from Company; Company retained as the ERPNext legal/accounting and business-scoping entity inside a Tenant), per Accepted ADR-015-Tenant-Company-Multi-Tenancy-Model (Version 1.0) and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published).
- **Approved scope:** AR-002 factual-status reconciliation (Section 10 official-field row: Open → Resolved, Blocks Development Yes → No, Priority preserved at Critical); Tenant/Company terminology reconciliation reflecting the governed topology; reference to the governed Option A topology decision; synchronization of directly maintained source-document version references (`Architecture_Freeze.md` to Approval 1.2; `Module_Dependency_Matrix.md` to Draft 0.5; Architecture Review Register to Version 0.4; Naming Registry to Version 1.4; ADR Index to Version 1.2; Blueprint Master Index to Version 2.3).
- **Preserved, unchanged:** every workstream definition (Sections 13–15); every Scope Matrix classification, Roadmap Treatment, Architecture Readiness, and Current Readiness value (Section 12); every dependency direction and sequencing statement (Sections 6, 19); every conditional, deferred, and excluded classification; every Implementation Authorization value (every workstream remains Not Authorized); the existing Architecture Review and Business Review dispositions recorded above, their dates, and their non-blocking-observation treatment; the original Project Owner Approval record (Version 1.0) and the prior AR-001 Synchronization Note (Version 1.1), both preserved as historical record.
- No Architecture Review or Business Review was repeated for this synchronization, because no roadmap scope, dependency direction, readiness classification, workstream taxonomy, or business content changed — only AR-002's official-field status and derived interpretive wording were reconciled with its resolved disposition.
- Job Card Tier A remains Company-scoped inside an isolated Tenant site, with site identity implicit and no separate Tenant field required by default; central customer identity, cross-tenant analytics, and Tenant Override implementation are explicitly deferred pending separate future governance and are not Job Card Tier A dependencies.
- No implementation authorization was granted by this synchronization; every workstream remains Not Authorized; Full Architecture Freeze remains unachieved; tenant provisioning, deployment, Docker, CI, schema, migration, hook, fixture, DocType, Tenant Override, plugin, central-identity, and analytics work all remain unauthorized.

**Subsequent current-state synchronization (2026-09-19) — Project Owner Synchronization Note (AR-003 Disposition):**

- **Approval type:** Factual baseline synchronization.
- **Authority:** Project Owner.
- **Synchronization approved:** 2026-09-19.
- **Approved transition:** Development Roadmap Approval Version 1.2 → Approval Version 1.3, recording the formally completed AR-003 disposition (Option A: seven terms — Print Specification, Approval Management, Production Workflow, Machine Management, Finishing, Quality Control, Production Orchestration — mapped, confirmed, or rejected against existing Approved concepts; AI Assistant registered as Proposed only), per `../standards/Naming_Registry.md` Section 40 and `../decisions/Architecture_Review_Register.md`, Version 0.5.
- The historical Architecture Review, Business Review, and Project Owner Approval record above — including the 2026-07-26 approval date and its statement that "AR-003 through AR-011 remain Open and unresolved" at the time of that approval — remains **unchanged**, exactly as originally recorded.
- AI Assistant **remains Proposed, excluded, unscoped, and unauthorized** pending separate future governance for its architecture, Approved Bounded Context, provider, model, and plugin design.
- **AR-004 through AR-011 remain Open and unchanged.**
- No Architecture Review or Business Review was repeated for this synchronization, because no roadmap scope, dependency direction, readiness classification, workstream taxonomy, or business content changed — only AR-003's official-field status and derived interpretive wording were reconciled with its resolved disposition.
- No implementation authorization was granted by this synchronization; every workstream remains Not Authorized; Full Architecture Freeze remains unachieved.

---

## 26. Roadmap Validation Checklist

Items are marked only where objectively verifiable from this authored Draft. Architecture, Business, and Owner review items remain unchecked in the initial Draft.

- [x] Architecture Freeze scope preserved
- [x] AR register fields reproduced accurately
- [x] Working Assumptions clearly distinguished from decisions
- [x] No Product Roadmap phase reused as implementation stage
- [x] No technical legacy phase numbering inherited
- [x] Legacy dependency contradictions disclosed
- [x] No dates, estimates, sprint commitments, or delivery owners
- [x] No implementation authorization granted
- [x] Job Card remains one aggregate and one custom DocType
- [x] Job Card Tier A and Tier B represented only as readiness gates
- [x] Production Planning, Job Cards, and Machine Scheduling remain distinct
- [x] Configuration Studio is not treated as monolithic
- [x] Plugin categories are not treated as blockers
- [x] Marketplace entity clusters remain distinct
- [x] Conditional and excluded workstreams are visibly classified
- [x] Required traceability references are present
- [x] Architecture review completed
- [x] Business review completed
- [x] Project Owner approval completed

---

## Related Documents

- [../implementation/Architecture_Freeze.md](../implementation/Architecture_Freeze.md)
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md)
- [../implementation/00_Implementation_Index.md](../implementation/00_Implementation_Index.md)
- [../implementation/01_Phase_1_Roadmap.md](../implementation/01_Phase_1_Roadmap.md)
- [../implementation/02_Module_Implementation_Order.md](../implementation/02_Module_Implementation_Order.md)
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md)
- [../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md)
- [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md)
- [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md)
- [../configuration/Configuration_Studio_Architecture.md](../configuration/Configuration_Studio_Architecture.md)
- [../architecture/Plugin_Architecture.md](../architecture/Plugin_Architecture.md)
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md)
- [../Documentation_Workflow.md](../Documentation_Workflow.md)

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-26 | Initial | Initial governed Development Roadmap draft. Derived from the effective Layered Architecture Freeze (`Architecture_Freeze.md`, Approval 1.0) and the current Module Dependency Matrix (`Module_Dependency_Matrix.md`, Draft 0.3). Establishes governance boundaries, the legacy-planning relationship (cross-referencing `01_Phase_1_Roadmap.md` and `02_Module_Implementation_Order.md` and disclosing their conflicts without resolving them), the hybrid dependency-tiered capability-workstream model, scope and architecture-readiness classifications, the workstream readiness/authorization ladder, the AR-001–AR-011 gate table (register fields verbatim), the roadmap scope matrix, stable and conditional workstream definitions, Job Card Tier A/Tier B treatment (one aggregate, one DocType), Configuration Studio decomposition, plugin/later-phase treatment, and the traceability and validation models. AR-gated work remains conditional. No Architecture Review item resolved, reprioritized, or renamed; no source document published; no dates, estimates, execution commitments, or implementation authorization introduced. |
| 0.2 | 2026-07-26 | Architecture Review Recorded | Formal Architecture Review completed with disposition Accepted with non-blocking observations, recorded in the new Section 25 Review Record; the Architecture review checklist item is marked complete. The roadmap is approved to proceed to Business Review. No architecture content, AR-001–AR-011 field, dependency direction, scope classification, or workstream definition was changed. No Architecture Review item was resolved. No implementation authorization occurred. Business Review and Project Owner Approval remain pending and unchecked. |
| 0.3 | 2026-07-26 | Business Review Recorded | Formal Business Review completed with disposition Accepted with non-blocking observations, recorded in Section 25 Review Record alongside the existing Architecture Review record; the Business review checklist item is marked complete. The roadmap is approved to proceed to Project Owner review. No architecture or business content, AR-001–AR-011 field, dependency direction, scope classification, or workstream definition was changed. No Architecture Review item or business-governance question was resolved. No implementation authorization occurred. Project Owner Approval remains pending and unchecked. |
| 1.0 | 2026-07-26 | Project Owner Approval | Formal Project Owner approval completed, recorded in Section 25 Review Record alongside the existing Architecture Review and Business Review records; the Project Owner approval checklist item is marked complete. Lifecycle transitioned from Draft 0.3 to Approval 1.0, establishing this document as the first formally approved PrintHub Development Roadmap baseline. The Architecture Review and Business Review dispositions (both Accepted with non-blocking observations) are accepted; their non-blocking observations remain deferred and unapplied. No architecture or business content, AR-001–AR-011 field, dependency direction, scope classification, or workstream definition was changed. No Architecture Review item or unresolved business-governance decision was resolved. No implementation authorization was granted — every workstream remains Not Authorized. The roadmap remains unpublished. |
| 1.1 | 2026-07-26 | Project Owner-Approved AR-001 Factual Synchronization | Project Owner-approved factual synchronization following the formal Project Owner disposition of AR-001 (Option A, recorded in `Architecture_Review_Register.md`, reaffirming Accepted ADR-001-ERPNext-Framework). Updated AR-001's official-field reproduction in Section 10 from Open/Critical/Yes to Resolved/Critical/No, with Priority preserved at its historical Critical value. Replaced active "Working Assumption"/unresolved-version wording throughout the Scope Matrix (Section 12), the Stable and Conditional Workstream Definitions (Sections 13.1–13.2, 14.1–14.4), the Explicit Exclusions (Section 23), the Open Governance Items (Section 24), and the Project Owner Approval boundaries (Section 25) with wording recording ERPNext v16, with the corresponding Frappe v16 major version, as the AR-001-governed target, while explicitly preserving that capability-level Fit, Gap, DocType, deployment, Docker, CI, hook, event, permission, and integration claims remain conditional pending technical revalidation. Synchronized source-document version references to `Architecture_Freeze.md` (Approval 1.1) and `Module_Dependency_Matrix.md` (Draft 0.4). Added a Project Owner Synchronization Note within Section 25's Review Record (approval date 2026-07-26) stating that no Architecture Review or Business Review was repeated, since no roadmap scope, dependency direction, readiness classification, workstream taxonomy, or business content changed. No roadmap scope, workstream taxonomy, scope classification, readiness classification, dependency direction, Job Card Tier A/Tier B treatment, Production Planning/Job Cards/Machine Scheduling relationship, Configuration Studio decomposition, Plugin Architecture treatment, or Marketplace/MachineIQ/AI Assistant treatment was changed. AR-002 through AR-011 remained unchanged and Open. The existing Architecture Review and Business Review records, their dates, dispositions, and non-blocking-observation treatment are preserved unchanged. No technical Fit, Gap, or DocType revalidation was performed or claimed. No implementation authorization was granted; every workstream remains Not Authorized. The roadmap remains unpublished. |
| 1.2 | 2026-07-28 | Project Owner-Approved AR-002 Factual Synchronization | Project Owner-approved factual synchronization following the formal Project Owner disposition of AR-002 (Option A, recorded in `Architecture_Review_Register.md`, Version 0.4). Updated AR-002's official-field reproduction in Section 10 from Open/Critical/Yes to Resolved/Critical/No, with Priority preserved at its historical Critical value. Recorded one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company, per Accepted ADR-015-Tenant-Company-Multi-Tenancy-Model (Version 1.0) and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published). Replaced active "Tenant vs Company unresolved" / "tenant model unresolved" wording throughout the Workstream Taxonomy (Section 11), the Scope Matrix (Section 12, Configuration Studio and Plugin Architecture rows), the Stable and Conditional Workstream Definitions (Sections 13.1, 13.3, 13.4, 14.6), the Explicit Exclusions (Section 23), the Open Governance Items (Section 24), and the Project Owner Approval boundaries (Section 25) with wording recording the governed topology and Tenant/Company distinction as resolved, while explicitly preserving that Tenant Override, tenant-scoped plugin configuration, tenant provisioning, and other tenant-scoped implementation remain conditional pending implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate scoped implementation authorization. Synchronized source-document version references to `Architecture_Freeze.md` (Approval 1.2) and `Module_Dependency_Matrix.md` (Draft 0.5). Added a Project Owner Synchronization Note within Section 25's Review Record (approval date 2026-07-28) stating that no Architecture Review or Business Review was repeated, since no roadmap scope, dependency direction, readiness classification, workstream taxonomy, or business content changed. Recorded that Job Card Tier A is Company-scoped inside an isolated Tenant site, with site identity implicit and no separate Tenant field required, and that central customer identity, cross-tenant analytics, and Tenant Override implementation are explicitly deferred pending separate future governance rather than permanently rejected. No roadmap scope, workstream taxonomy, scope classification, readiness classification, dependency direction, sequencing, Job Card Tier A/Tier B treatment, Production Planning/Job Cards/Machine Scheduling relationship, Configuration Studio decomposition, Plugin Architecture treatment, or Marketplace/MachineIQ/AI Assistant treatment was changed. AR-001 remains Resolved/Critical/No; AR-003 through AR-011 remained unchanged and Open. The existing Architecture Review, Business Review, original Project Owner Approval, and prior AR-001 Synchronization Note records, their dates, dispositions, and non-blocking-observation treatment are preserved unchanged. No technical Fit, Gap, or DocType revalidation was performed or claimed. No implementation authorization was granted; every workstream remains Not Authorized; Full Architecture Freeze remains unachieved. The roadmap remains unpublished. |
| 1.3 | 2026-09-19 | Project Owner-Approved AR-003 Factual Synchronization | Project Owner-approved factual synchronization following the formal Project Owner disposition of AR-003 (Option A, recorded in `Architecture_Review_Register.md`, Version 0.5). Updated AR-003's official-field reproduction in Section 10 from Open/High/Partial to Resolved/High/No. Recorded that seven terms are mapped/confirmed/rejected against existing Approved concepts and AI Assistant is registered as Proposed only, with no Bounded Context, architecture, provider, model, or implementation decision. Corrected the active Open Governance Items statement (Section 24) from "AR-003 through AR-011 — all Open" to "AR-004 through AR-011," reflecting AR-003's new Resolved status; updated the AI Assistant Workstream Taxonomy row (Section 11) and the AI Assistant exclusion statement (Section 23) accordingly. Added a Project Owner Synchronization Note within Section 25's Review Record (approval date 2026-09-19), explicitly preserving the entire dated 2026-07-26 Architecture Review, Business Review, and Project Owner Approval record — including its historical "AR-003 through AR-011 remain Open and unresolved" boundary bullet — verbatim and unchanged. Corrected the Legacy Planning Inputs description of `../implementation/01_Phase_1_Roadmap.md` so it no longer states "AI Assistant" is unregistered, clarifying instead that this legacy document contains stale pre-resolution wording, while AI Assistant is now registered as Proposed only and remains unscoped and excluded pending separate future governance. No roadmap scope, workstream taxonomy, dependency direction, sequencing, or readiness classification changed beyond recording AR-003 as Resolved. No Architecture or Business Review was repeated. AI Assistant remains Proposed, excluded, unscoped, and unauthorized pending separate future governance. AR-001 and AR-002 remain Resolved as previously recorded; AR-004 through AR-011 remained unchanged and Open. No implementation authorized. |

---

## Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified against Naming Registry
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
