# Architecture Freeze (Layered)

## 1. Document Control

Version:
1.4

Status:
Approval

Owner:
PrintHub Architecture Team

Last Updated:
2026-09-20

Freeze Type:
Layered Architecture Freeze (not a Full Architecture Freeze)

Authoritative on approval for:
The Frozen scope defined in Section 8 only.

Note on effect:
Formal Project Owner approval has been recorded (Section 22). The Layered Architecture Freeze is **effective** for the Frozen scope defined in Section 8, subject to the Conditional References (Section 9) and Excluded Layers (Section 10). This approval does **not** promote any other document to Published or any equivalent authoritative status; the baseline source documents retain their existing lifecycle statuses unless separately promoted through a subsequent, separate controlled promotion task (Section 16). This approval is not a Full Architecture Freeze (Section 4, 6) and does not authorize implementation (Section 19).

Project Owner Synchronization Approval (factual baseline synchronization only):
The Project Owner has approved a strictly factual synchronization of this Architecture Freeze baseline with the formally completed AR-002 Option A disposition, transitioning this document from Approval, Version 1.1 to Approval, Version 1.2, dated 2026-07-28. This is **not** a repeated or new Architecture Review or Business Review of this document, and it does **not** reopen the Section 22 Validation Checklist or the original Project Owner Approval record (Revision History, Version 1.0), both of which are preserved unchanged as historical record. The approved scope of this synchronization is limited to: (a) AR-002 factual status reconciliation (Open → Resolved, Blocks Development Yes → No, Priority unchanged at Critical); (b) Tenant/Company terminology reconciliation reflecting the governed one-site/one-database-per-Tenant topology; (c) reference to the governed Option A topology decision (Accepted ADR-015, Version 1.0; `../blueprint/25_MultiTenant_Architecture.md`, Approval, Version 1.0, not Published); and (d) directly relevant source-document version references (Module Dependency Matrix Draft 0.5; Architecture Review Register Draft — Open Register, Version 0.4; Naming Registry Draft 1.4; ADR Index Draft 1.2; Blueprint Master Index Draft 2.3). This synchronization preserves, unchanged: every frozen, conditional, deferred, and excluded classification; every dependency direction; every workstream readiness value; every implementation-authorization value; and all existing Architecture Review and Business Review dispositions recorded elsewhere in this document. Full Architecture Freeze remains unachieved (Section 21). No implementation authorization is granted by this synchronization.

---

## 2. Purpose

This document proposes a **layered** Architecture Freeze for PrintHub. It is a scoping and disposition instrument, not an architecture-analysis or decision-making instrument. It answers:

- What architecture is proposed as Frozen?
- What architecture remains Unfrozen / Excluded, and why?
- Which documents form the proposed authoritative Frozen baseline?
- Which documents remain Conditional References or Draft?
- Which Architecture Review items prevent a Full Architecture Freeze?
- Which governance gaps are explicitly deferred?
- What must happen before the Development Roadmap can be finalized?
- What must happen before implementation may begin?

This document resolves no open decision, selects no ERPNext version, selects no tenant/Machine/Quotation/Material strategy, and creates no Architecture Review item or ADR.

---

## 3. Scope

In scope: declaring a Frozen conceptual-architecture baseline, explicitly excluding unresolved layers, recording open Architecture Review and secondary-backlog dependencies, and defining the conditions for a later Full Architecture Freeze.

Out of scope: resolving any Architecture Review item; authoring a Development Roadmap; authorizing implementation; assigning phases, dates, milestones, sprints, owners, or estimates; modifying the status metadata of any other document.

---

## 4. Freeze Model

PrintHub has adopted a **Layered Architecture Freeze** and is **not** subject to a **Full Architecture Freeze**. The layered model freezes the stable conceptual architecture while explicitly excluding the integration, tenancy, ownership, and module-specific design layers that depend on open Architecture Review items.

```
+--------------------------------------------------------------+
|                    FROZEN (conceptual)                       |
|  principles · bounded contexts (w/ open Qs) · Config Studio  |
|  (excl. tenant runtime) · Plugin architecture · dependency   |
|  model · Reporting/Config-Studio ownership · Job Card model  |
+--------------------------------------------------------------+
                 |  excluded, decision-gated  |
                 v                             v
+----------------------------+  +------------------------------+
|  EXCLUDED LAYERS           |  |  CONDITIONAL REFERENCES      |
|  ERPNext integration AR-001|  |  Fit Analysis · DocType Map  |
|  Multi-tenant AR-002       |  |  Module Dependency Matrix    |
|  Machine AR-004            |  |  (stable parts usable;       |
|  Quotation AR-005          |  |   AR-gated parts excluded)   |
|  Material/Prod.Tmpl AR-006 |  +------------------------------+
+----------------------------+
```

Note: AR-001 is now **Resolved** (see Section 10.1); the ERPNext integration layer remains Excluded pending capability-level technical revalidation against the governed v16/Frappe v16 target, not pending version selection.

Note: AR-002 is now **Resolved** (see Section 10.2); the governed topology — one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company — is established via Accepted ADR-015 and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published), but the multi-tenant layer remains Excluded pending implementation design, Publication, and separate scoped implementation authorization, not pending topology selection.

Note: AR-004 is now **Resolved** (see Section 10.3): Machine and Machine Profile are wholly Custom PrintOS DocTypes (Option C, 2026-09-20), per `../decisions/Architecture_Review_Register.md` and `../blueprint/16_Print_Machine_Model.md`, Version 0.1; the Machine ownership layer remains Excluded pending Blueprint 16's own review and a separate scoped implementation-authorization decision, not pending base-object selection.

---

## 5. Freeze Terminology

These terms apply to *freeze disposition within this document only*. They are **separate from** the repository's normal document lifecycle (Draft, Review, Approved, Published) defined in `docs/Documentation_Workflow.md`. This document introduces no new repository-wide status named "Frozen."

1. **Layered Architecture Freeze** — A freeze of stable conceptual architecture while explicitly excluding unresolved integration, tenancy, ownership, and module-specific design layers.
2. **Full Architecture Freeze** — A future state in which all Critical and relevant High-impact architecture decisions have formal disposition and all required architecture layers have an authoritative baseline.
3. **Frozen Baseline** — Documents, or clearly identified portions of documents, proposed as authoritative for the Frozen scope.
4. **Excluded Layer** — An architecture area deliberately left outside the Frozen baseline because it depends on an unresolved decision.
5. **Conditional Reference** — A document usable for analysis or planning but not frozen as a whole because it contains unresolved assumptions or Pending Architecture Review mappings.
6. **Working Assumption** — A documented assumption used for analysis that is not an approved architecture decision and does not authorize implementation.

---

## 6. Layered Freeze Decision

**Decision made:** Adopt a Layered Architecture Freeze, effective by Project Owner approval (Section 22), with the Frozen scope in Sections 7–8, the Conditional References in Section 9, and the Excluded Layers in Section 10.

**Decision explicitly not made:** No Full Architecture Freeze is proposed. No open Architecture Review item (AR-001–AR-011) is resolved. No ERPNext version, tenant model, Machine ownership, Quotation strategy, or Material/Product Template ownership is selected.

---

## 7. Frozen Architecture Principles

The following conceptual architecture is proposed as Frozen. Each is already established and consistent across the repository; none silently encodes an open Architecture Review decision.

- ERPNext core remains immutable; all customization belongs in the PrintHub custom app (`printos_core`), per [ADR-002-PrintOS-Core](../decisions/ADR-002-PrintOS-Core.md).
- Clean Architecture; Domain-Driven Design; Ports and Adapters; Repository Pattern; Unit of Work; Dependency Injection.
- Configuration as Data.
- Documentation-First governance, per `docs/Documentation_Workflow.md` and [ADR-003](../decisions/ADR-003-Documentation-First.md).
- The approved bounded-context structure per [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md), **subject to the explicitly listed Blueprint Open Questions in Section 13** (Machine Scheduling as its own context; GST folding into Accounts) — those context boundaries freeze *with the open question recorded*, not resolved.
- **Estimation feeds Sales** (Estimation → Sales), per the resolved clarification in `06_Bounded_Contexts.md` (v1.2).
- **Configuration Studio owns Report Definition and Dashboard Definition**; **Reporting consumes published Report and Dashboard Definitions** and does not own or duplicate them, per the resolved clarification across `06_Bounded_Contexts.md`, `Canonical_Domain_Model.md`, `Configuration_Studio_Architecture.md`, and `ERPNext_DocType_Mapping.md`.
- **PrintHub Job Card is a custom, print-domain concept and must not reuse ERPNext's Manufacturing Job Card**, per [ADR-014-Production-Terminology](../decisions/ADR-014-Production-Terminology.md).
- **Production Planning, Job Cards, and Machine Scheduling are three distinct modules**, per ADR-014.
- **Configuration Studio is not one monolithic prerequisite** — its native-fallback designers are deferrable; only Feature Flag, Automation Rule, and Module Definition are hard prerequisites, per `Configuration_Studio_Architecture.md`.
- **Plugin architecture is additive; no plugin category is a Phase 1 blocker**, per `Plugin_Architecture.md`.
- The **module dependency classification model**: direct blocker, transitive delay, convergent blocking, and optional dependency, per `Module_Dependency_Matrix.md`.
- The **Job Card Tier A / Tier B distinction is a dependency-lifecycle distinction, not a DocType split** (Section 15).

---

## 8. Proposed Frozen Baseline

**Group A — Frozen Documents.** Each is authoritative for the Frozen conceptual scope by virtue of this freeze's approval. Each has been confirmed not to silently encode an unresolved Architecture Review decision. (Versions are the current repository versions at time of authoring; promotion to Published occurs only via the separate task in Section 16 — this freeze's approval does not itself publish any source document.)

| Document | Current Version | Freeze Disposition |
|---|---|---|
| [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) | 1.0 | Frozen in full |
| [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) | 1.2 | Frozen with explicit exclusions (Open Questions in Section 13 remain unresolved) |
| [../blueprint/08_Master_Data_Model.md](../blueprint/08_Master_Data_Model.md) | 1.0 | Frozen with explicit exclusions — business-level master-data model Frozen; it does not define ERPNext DocType ownership. Unresolved implementation ownership of its entities is deferred to AR-006 (Material/Product Template); Machine and Machine Profile's own ownership question is Resolved (AR-004, Option C, 2026-09-20) |
| [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) | 1.0 (see note below) | Frozen in full |
| [../blueprint/10_Business_Workflows.md](../blueprint/10_Business_Workflows.md) | 1.0 | Frozen in full |
| [../blueprint/04_System_Architecture.md](../blueprint/04_System_Architecture.md) | (repo) | Frozen in full |
| [../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md) | 1.0 | Frozen with explicit exclusions (product-direction intent only — see Section 17 boundary) |
| `../technical/01`–`10` (Clean Architecture, layering, dependency rules, project structure, request lifecycle, error handling, extensibility, implementation guidelines) | (repo) | Frozen in full |
| [../architecture/01_System_Architecture.md](../architecture/01_System_Architecture.md) | 1.0 | Frozen in full |
| [../architecture/02_Clean_Architecture.md](../architecture/02_Clean_Architecture.md) | 1.0 | Frozen in full |
| [../architecture/03_DDD_Architecture.md](../architecture/03_DDD_Architecture.md) | 1.0 | Frozen in full |
| [../architecture/05_Extensibility_Architecture.md](../architecture/05_Extensibility_Architecture.md) | 1.0 | Frozen in full |
| [../architecture/06_Event_Architecture.md](../architecture/06_Event_Architecture.md) | 1.0 | Frozen with explicit exclusions (event-name casing open — Section 13) |
| [../architecture/07_Security_Architecture.md](../architecture/07_Security_Architecture.md) | 1.0 | Frozen with explicit exclusions (conceptual security principles Frozen; the reserved formal `docs/blueprint/23_Security_Architecture.md` is unwritten and Excluded — Section 10.7) |
| [../architecture/08_Performance_Architecture.md](../architecture/08_Performance_Architecture.md) | 1.0 | Frozen in full |
| [../architecture/Canonical_Domain_Model.md](../architecture/Canonical_Domain_Model.md) | 0.2 | Frozen with explicit exclusions (AR-gated entities and the unregistered Marketplace-plugin cluster remain Pending/excluded) |
| [../architecture/Plugin_Architecture.md](../architecture/Plugin_Architecture.md) | 0.1 | Frozen in full |
| [../configuration/Configuration_Studio_Architecture.md](../configuration/Configuration_Studio_Architecture.md) | 0.2 | Frozen with explicit exclusions (tenant-runtime slice excluded — AR-002 Resolved; governed topology established, Tenant Override implementation remains undesigned and unauthorized) |
| `../configuration/00`–`15` (designer catalog) | (repo) | Frozen with explicit exclusions (Tenant Customization runtime excluded — AR-002 Resolved; governed topology established, implementation remains undesigned and unauthorized) |
| Stable `../standards/` conventions (naming *format*, coding, testing, git, documentation, error handling, logging) | (repo) | Frozen in full, except unresolved Naming Registry Decision-Matrix items (Section 13) |

The Security entry is deliberately split: **stable security principles are Frozen**; the reserved but unwritten formal Security Architecture Blueprint artifact is Excluded. Security is therefore not architecturally undefined — only its reserved formal artifact is incomplete.

**Version-metadata note — `09_PrintOS_Modules.md`:** This baseline records the source document's current **header** version, **1.0**. That source document contains a header-versus-Revision-History discrepancy (its `Version:` header reads 1.0 while its Revision History carries a 1.1 "ADR Synchronization" entry). This freeze document treats the header value as authoritative and does **not** treat the Revision-History value as the current version. The discrepancy is a metadata issue in the source document and **must be reconciled during the later controlled promotion and status-alignment task** (Section 16); `09_PrintOS_Modules.md` is not modified by this task.

---

## 9. Conditional References

**Group B — Conditional References.** Usable for analysis and planning, but **not** frozen as a whole because each contains both stable conclusions and AR-gated assumptions.

| Document | Version | Stable content that may be relied upon | Excluded content | AR responsible |
|---|---|---|---|---|
| [../architecture/ERPNext_Fit_Analysis.md](../architecture/ERPNext_Fit_Analysis.md) | 0.1 | The Native/Extend/Customize/Plugin/Future *classification method* and the reuse-vs-build reasoning | All version-specific capability claims (rest on ERPNext v16, the AR-001-governed target; still pending capability-level technical revalidation) | AR-001 (Resolved; technical revalidation pending) |
| [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md) | 0.2 | The ~40 rows with definite owners (Native/Extended/Custom/Plugin) and the single-Job-Card mapping | The 13 `Pending Architecture Review` rows; the 5 "No Implementation Owner Assigned" Marketplace-plugin rows | AR-005/006 (Pending rows; AR-004 Resolved and removed from this list); Section 14 (Marketplace cluster) |
| [Module_Dependency_Matrix.md](Module_Dependency_Matrix.md) | 0.5 | Dependency classification model; foundation layer; non-AR-gated module relationships; Job Card Tier A/B analysis | AR-gated module sequencing conclusions; capability-level revalidation underlying ERPNext-facing rows against the AR-001-governed v16 target; Tenant Override and tenant-scoped plugin configuration implementation pending AR-002-governed topology's Publication and separate authorization | AR-001 (Resolved; technical revalidation pending)/002 (Resolved; implementation pending)/004/005/006 |
| [../architecture/ERPNext_Gap_Analysis.md](../architecture/ERPNext_Gap_Analysis.md) | 0.1 | The 28 gap records and their classifications | Gap sizing that depends on AR-gated ownership | AR-005/006 (AR-004 Resolved and removed from this list) |
| [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) | 0.1 | The 75-entity catalog and categories | Print Specification (Not Adopted — AR-003 Resolved 2026-09-19; retained here only as a not-adopted traceability entry), AI cluster (Conversation, Prompt, Knowledge Source, Recommendation, AI Action — unregistered, no covering Architecture Review item, outside AR-003, pending separate future governance), Approval Record (naming/classification outside AR-003, a separate, currently unassigned governance question), Marketplace-plugin cluster | None of these residual questions is governed by AR-003; see Section 14 |

Conditional References must **not** be cited as fully authoritative. Any downstream artifact relying on one must cite the specific stable portion and acknowledge the excluded portion and its responsible AR.

---

## 10. Unfrozen and Excluded Layers

The following are explicitly **Excluded** from the Layered Freeze.

**10.1 ERPNext integration architecture** — Excluded. AR-001 is **Resolved** (Project Owner selected Option A, reaffirming the Accepted ADR-001-ERPNext-Framework decision): **ERPNext v16, with the corresponding Frappe v16 major version, is the governed target**; simultaneous v15/v16 support is not in scope; minor and patch selection remains centrally governed. This layer remains Excluded from the Frozen baseline not because the major version is undecided, but because **capability-level Fit, Gap, DocType, hook, event, permission, deployment, and integration claims still require technical revalidation** against the governed target before this layer can be newly frozen. **This document makes no promotion of the ERPNext integration layer into the Frozen baseline.**

**10.2 Multi-tenant architecture** — Excluded. AR-002 is **Resolved** (Project Owner selected Option A, recorded in the Architecture Review Register, Version 0.4): PrintHub uses one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company (Company is the ERPNext legal/accounting and business-scoping entity inside a Tenant; one Tenant may contain one or more Companies), per Accepted [ADR-015-Tenant-Company-Multi-Tenancy-Model](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) (Version 1.0) and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, **not Published**). This layer remains Excluded from the Frozen baseline not because the topology or the Tenant/Company relationship is undecided, but because provisioning, tenant lifecycle operations, deployment automation, resource entitlement enforcement, and other operational implementation mechanisms remain undesigned, Blueprint 25 remains unpublished, and a separate scoped implementation-authorization decision remains required. The Configuration Studio tenant-runtime slice (Tenant Override) remains outside the Frozen baseline and remains a separate, undesigned Configuration Studio concern; it is not required for Job Card Tier A. Central customer identity and cross-tenant analytics remain explicitly deferred pending separate future governance and are not authorized by AR-002's resolution.

**10.3 Machine ownership** — AR-004 is Resolved (Option C, 2026-09-20: Machine and Machine Profile are both Custom PrintOS DocTypes). This layer may now be considered for inclusion in a future Frozen baseline revision; it is not automatically promoted by this correction alone. Machine Scheduling implementation and the related operational (Tier B) Job Card dependencies remain conditional pending Blueprint 16's own review and separate scoped implementation authorization.

**10.4 Quotation strategy** — Excluded. AR-005 is Open. Quotation, Quotation Line, and the final Estimation/Sales implementation boundary remain conditional.

**10.5 Material and Product Template ownership** — Excluded. AR-006 is Open. Material, Substrate, Item, and Product Template implementation ownership remains conditional; this has the widest cross-module blast radius (Inventory, Estimation, Purchasing).

**10.6 AR-gated DocType ownership cluster** — Excluded. The `Pending Architecture Review` rows in `ERPNext_DocType_Mapping.md` remain unfrozen.

**10.7 Reserved or unreconciled architecture documents** — Excluded: the reserved-but-unwritten `docs/blueprint/22_Integration_Architecture.md`, `23_Security_Architecture.md`, `24_Deployment_Architecture.md`; `docs/blueprint/25_MultiTenant_Architecture.md` is no longer reserved-but-unwritten — it now exists at Approval, Version 1.0 (not Published), per AR-002 Option A and Accepted ADR-015 — but remains Excluded here because it is not yet Published; and the lower-layer working drafts [../architecture/04_MultiTenant_Architecture.md](../architecture/04_MultiTenant_Architecture.md) (v0.1) — not reconciled with `25_MultiTenant_Architecture.md`, reconciliation remains a separate, later controlled task — [../architecture/09_Deployment_Architecture.md](../architecture/09_Deployment_Architecture.md) (v0.1), and [../architecture/10_Integration_Architecture.md](../architecture/10_Integration_Architecture.md) (v0.1), which remain explicit working drafts pending reconciliation. Note: stable *conceptual* security and integration *principles* are Frozen (Sections 7–8); only these reserved/unreconciled/unpublished formal artifacts are Excluded.

---

## 11. Partial-Document Freeze Rules

Where a document contains both stable and unresolved material, it is **not** declared frozen in full. Each such document carries exactly one disposition:

- **Frozen in full** — no unresolved material.
- **Frozen with explicit exclusions** — the excluded sections, rows, entities, or assumptions are named precisely (Section 8).
- **Conditional reference** — stable and excluded content are separated with the responsible AR (Section 9).
- **Remains Draft** — central purpose depends on an open decision (Section 16 lists these).
- **Later-phase deferred** — scope belongs to a future phase (e.g., Marketplace plugin-entity cluster, Section 14).

No document is described as "mostly frozen," "generally approved," or "ready enough." Every partial freeze names its exclusions.

---

## 12. Architecture Review Freeze Impact

The Register Status, Priority, and Blocks Development columns are reproduced **verbatim** from [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) Section 2 and are not reinterpreted, reclassified, or resolved. The remaining columns are this document's freeze-scoping interpretation.

| AR ID | Topic | Register Status | Priority | Blocks Development | Freeze Disposition | Affected Layer | Requirement Before Full Freeze | Requirement Before Development Roadmap | Requirement Before Implementation |
|---|---|---|---|---|---|---|---|---|---|
| AR-001 | ERPNext Platform Version (15/16 conflict) | Resolved | Critical | No | Resolved — version-selection gate removed; ERPNext v16/Frappe v16 governed target (Option A, reaffirming ADR-001); layer remains Excluded pending technical revalidation | ERPNext integration (cross-cutting) | Technical revalidation of capability-level claims against the governed v16 target | Required before finalizing any ERPNext-facing section | Blocks ERPNext-facing implementation until revalidation, a Published specification, and separate scoped implementation authorization |
| AR-002 | Multi-Tenant Strategy Alignment | Resolved | Critical | No | Resolved — topology-selection gate removed; one isolated Frappe site and operational database per Tenant, Tenant distinct from Company (Option A, Accepted ADR-015, Version 1.0); layer remains Excluded pending implementation design, Publication, and separate authorization | Multi-tenant architecture; Config Studio tenant runtime | Implementation design of provisioning, tenant lifecycle, and deployment mechanisms; Publication of `25_MultiTenant_Architecture.md` | Required before finalizing multi-tenant implementation work; Company-scoped Job Card Tier A work is not gated | Blocks Tenant Override and other tenant-scoped implementation until design, Publication, and separate scoped implementation authorization |
| AR-003 | Naming Registry Alignment (non-Approved module names, unregistered terms) | Resolved | High | No | Resolved — AR-003 naming requirement is satisfied; seven terms mapped/confirmed/rejected against existing Approved concepts, AI Assistant registered as Proposed (Option A, 2026-09-19); AI Assistant remains a separately deferred and excluded capability | AI Assistant (Plugin boundary); no other layer affected | None — naming review complete | Required before finalizing AI Assistant scope: separate future governance for architecture/provider/model — no AI Assistant architecture or implementation is permitted | No other layer remains blocked by AR-003; AI Assistant implementation remains blocked pending separate future governance, Bounded Context assignment, and implementation authorization |
| AR-004 | Machine Domain Ownership | Resolved | High | No | Resolved — Machine and Machine Profile Custom (Option C, 2026-09-20) | Machine, Machine Profile, Machine Scheduling; Tier B Job Card | Satisfied | Machine ownership no longer required before finalizing; Machine Scheduling module design and Tier B (AR-006) remain separately required | No longer blocks Machine/Machine Profile; Machine Scheduling module design and implementation authorization remain separately required |
| AR-005 | Quotation Strategy | Open | High | Partial | Module-level blocker | Estimation/Quotation; Estimation-Sales boundary | Formal disposition | Required before finalizing Estimation/Quotation design | Blocks affected module |
| AR-006 | Item vs. Material/Product Template Mapping | Open | High | Partial | Module-level blocker (widest blast radius) | Inventory, Estimation, Purchasing master data | Formal disposition | Required before finalizing master-data design | Blocks affected modules |
| AR-007 | Purchasing vs. Procurement Module Naming | Open | Medium | No | Documentation/terminology cleanup | Module/context naming | Resolve or formally defer | Not required | Terminology only |
| AR-008 | Dispatch vs. Delivery Terminology | Open | Medium | No | Documentation/terminology cleanup | Field/label naming | Resolve or formally defer | Not required | Terminology only |
| AR-009 | Quality Module Status | Open | Medium | No | Quality scope cleanup / later-phase decision | Quality Check Record scope | Resolve or formally defer | Not required | No practical core block |
| AR-010 | BOM Necessity for Estimation | Open | Medium | Partial | Estimation Engine final-design dependency | Estimation Engine internals | Resolve or formally defer | Not required for structure | Delays final design |
| AR-011 | CRM Enquiry vs. Opportunity Mapping | Open | Low | No | Enquiry-specific final-design dependency | Enquiry CRM behavior | Resolve or formally defer | Not required | Delays Enquiry-only final design |

No item above is resolved, reclassified, or reprioritized.

---

## 13. Naming Registry and Blueprint Governance Backlogs

Governance work exists **outside** the numbered Architecture Review Register. These backlogs must be cross-referenced during future freeze and implementation reviews so they are not hidden by reliance on the AR Register alone. None is resolved here.

**13.1 Naming Registry Decision Matrix** (`docs/standards/Naming_Registry.md` Section 27, Pending-ADR items):
- Event-name casing (snake_case vs. PascalCase) — relevant to `06_Event_Architecture.md`.
- Customer / Client / Party.
- Supplier / Vendor.
- Other Pending-ADR terminology recorded in the Naming Registry.

**13.2 Blueprint Open Questions** (`docs/blueprint/06_Bounded_Contexts.md` Open Questions):
- Whether Machine Scheduling is a separate bounded context distinct from Production.
- Whether GST is folded into Accounts as a single context.
- Other currently open Blueprint questions relevant to the Frozen scope.

These items are **visible but unresolved**. The bounded-context structure freezes *with these questions recorded as open*, not as if they were settled.

---

## 14. Marketplace Plugin-Entity Governance Gap

The following entities are explicitly recorded as an uncovered governance gap:

- Marketplace Package
- Marketplace Template
- Extension
- Publisher
- Marketplace Package Version

Findings:
- They describe a **plugin/extension-distribution marketplace**.
- They are **distinct from the public-buyer Marketplace** governed by [ADR-009-Marketplace](../decisions/ADR-009-Marketplace.md).
- They currently have **no final implementation owner** (marked "No Implementation Owner Assigned" in `ERPNext_DocType_Mapping.md`).
- They currently have **no numbered Architecture Review coverage**.
- Their **naming collision** with the Approved Marketplace creates governance risk if left implicit.
- They are **Excluded** from the Layered Frozen baseline.
- They are **later-phase** and must **not** appear as implementation-ready Development Roadmap work.
- They **require a formal governance disposition before Full Architecture Freeze.**

This document does **not** create a new Architecture Review item and does **not** choose whether the disposition should be a new AR, a Naming Registry proposal, or an explicit later-phase deferral.

---

## 15. Job Card Tier A / Tier B Clarification

Approved interpretation, recorded here as Frozen:

- **Tier A — Job Card work-unit contract / scaffolding:** the minimum custom PrintHub Job Card aggregate/interface contract required so Production Planning and Machine Scheduling can reference a production work unit. Defined from the Sales Order and Approved Artwork.
- **Tier B — Operationally schedulable Job Card:** requires the convergent inputs for operational execution — Sales, Artwork approval, material allocation, machine assignment, and Production Planning output.

Clarifications:
- Tier A and Tier B are **not separate entities, not separate aggregates, and not separate DocTypes.**
- `ERPNext_DocType_Mapping.md` remains **one Custom PrintHub Job Card DocType**.
- The distinction exists **only** to explain the dependency lifecycle and to break the apparent Job Cards ↔ Machine Scheduling circular dependency.
- Upstream documentation clarification (into `Canonical_Domain_Model.md` and a `ERPNext_DocType_Mapping.md` note) is **recommended but is not a freeze blocker**.

---

## 16. Document Status and Publication Governance

**Recorded inconsistency (not modified by this task):** `docs/Documentation_Status.md` states **Published: 0** ("no document has completed the full Review → Approval → Owner Approval cycle"), while `docs/blueprint/00_Master_Index.md` and `docs/decisions/00_ADR_Index.md` both carry `Status: Published`.

Governance clarifications:
- **Index publication does not automatically publish indexed content.** A Published index/navigation document is not proof that the architecture corpus it indexes is Published. The two index documents' status must not be treated as evidence that the Frozen-candidate documents are already authoritative.
- **No status metadata is changed by this task.** This document does not promote any document to Published.
- **Status changes that should occur only after this freeze document is approved:** in a subsequent, separate controlled promotion task, the Group A (Proposed Frozen) documents — and only the frozen scope of the partially-frozen ones — should be carried through the documented Review → Approval → Published cycle; and `docs/Documentation_Status.md` should be reconciled so its Published count reflects the actual post-promotion state and the index-vs-content distinction.
- **Source of truth for the reconciliation:** `docs/Documentation_Status.md` is the document that should be updated to record the reconciled Published/Frozen state in that later task, cross-referencing this Architecture Freeze document.

---

## 17. Product Roadmap Boundary

`docs/blueprint/03_Product_Roadmap.md` is included in the Frozen baseline **only for its product-direction intent** (the phased platform vision from PrintOS ERP through Marketplace, and the user-group sequencing). Its phases, ordering, and any implied timing are **not** frozen as implementation authorization. The future Development Roadmap is a separate implementation-planning artifact and is not created, sequenced, or authorized here.

---

## 18. Development Roadmap Readiness

**18.1 Roadmap drafting that may begin.** A future Development Roadmap may reference, in structure:
- the stable conceptual architecture (Sections 7–8);
- foundation capabilities (Administration, HR, Warehouse, Native ERPNext reference/master data);
- non-AR-gated module relationships;
- Configuration Studio native-fallback designers;
- Plugin Port contracts;
- later-phase deferrals (MachineIQ, Marketplace, AI Assistant, the Marketplace plugin-entity cluster).

**18.2 Roadmap sections that cannot be finalized.** The following must **not** be finalized in a Development Roadmap until the named condition is satisfied:
- ERPNext-facing implementation commitments — AR-001 is Resolved (v16/Frappe v16 governed target), but finalization still awaits capability-level technical revalidation, a Published specification, and separate scoped implementation authorization;
- multi-tenant work — AR-002 is Resolved (Option A, one isolated Frappe site and operational database per Tenant, Tenant distinct from Company), but finalization of Tenant Override and other tenant-scoped implementation still awaits implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate scoped implementation authorization; Company-scoped Job Card Tier A work is not gated by this item;
- Machine Scheduling / operational (Tier B) Job Card ownership design — AR-004 is Resolved (Option C, 2026-09-20: Machine and Machine Profile are wholly Custom PrintOS DocTypes), but finalization of Machine Scheduling's own design and operational (Tier B) Job Card completion still await Blueprint review of `16_Print_Machine_Model.md`, DocType-level design, and separate scoped implementation authorization; Job Cards Tier B additionally remains gated by AR-006 (Material);
- Estimation / Quotation implementation design — AR-005;
- Material / Product Template implementation design — AR-006;
- Marketplace plugin-marketplace entity work — pending formal governance disposition (Section 14).

Roadmap drafting is **not** implementation authorization.

---

## 19. Implementation Authorization Boundary

This Architecture Freeze document **does not authorize implementation.** Explicitly:
- Although AR-001 has resolved ERPNext v16 (with the corresponding Frappe v16 major) as the governed target, this document does **not** authorize implementation on that basis — capability-level claims remain pending technical revalidation, and a Published specification plus a separate scoped implementation-authorization decision are still required.
- Although AR-002 has resolved the governed multi-tenant topology (one isolated Frappe site and operational database per Tenant, Tenant distinct from Company), this document does **not** authorize implementation on that basis — tenant provisioning, deployment, Docker, or CI changes; Tenant Override; central customer identity; and cross-tenant analytics are all **not** authorized. `25_MultiTenant_Architecture.md` remains unpublished, and a separate scoped implementation-authorization decision remains required.
- It does **not** authorize implementation against unresolved DocType ownership.
- It does **not** authorize schema, migration, hook, fixture, or DocType work of any kind, including for Job Card Tier A, which remains Not Authorized.
- It does **not** resolve any Architecture Review item (AR-001 and AR-002 were resolved separately, by the Project Owner, in the Architecture Review Register).

Implementation authorization requires a **separate decision** after the applicable Architecture Review items and documentation prerequisites have been satisfied.

---

## 20. Risks and Constraints

- **Freezing ERPNext-facing architecture ahead of capability-level technical revalidation.** Mitigated by Excluding the integration layer (10.1) even though AR-001 has resolved the governed major version (v16/Frappe v16); capability-level claims remain conditional pending revalidation, not merely a version assumption.
- **Treating single-tenant assumptions as final multi-tenant architecture.** Mitigated by Excluding the multi-tenant layer (10.2).
- **Prematurely fixing Machine, Quotation, or Material ownership.** Mitigated by Excluding those layers (10.3–10.6) and marking DocType rows Conditional (Section 9).
- **Treating Conditional References as fully authoritative.** Mitigated by the partial-freeze rule (Section 11) and the stable/excluded split (Section 9).
- **Allowing Naming Registry and Blueprint open questions to remain invisible.** Mitigated by Section 13's explicit backlog visibility and cross-reference requirement.
- **Confusing the public-buyer Marketplace with the plugin marketplace.** Mitigated by Section 14's explicit distinction and exclusion.
- **Interpreting Job Card Tier A/B as two DocTypes.** Mitigated by Section 15's one-aggregate/one-DocType statement.
- **Allowing the freeze document to become an implementation roadmap.** Mitigated by Sections 17–19 boundaries; no dates, phases, or sequencing appear here.
- **Marking documents Published without completing the documented approval lifecycle.** Mitigated by Section 16 — no status is changed by this task; promotion is a separate controlled task.
- **Partial freezes becoming unclear or untraceable.** Mitigated by the precise per-document exclusions in Sections 8–9 and the partial-freeze rule (Section 11).

---

## 21. Full Architecture Freeze Exit Criteria

A later Full Architecture Freeze requires, at minimum:
- AR-001 formally dispositioned — **satisfied** (Resolved, Option A, reaffirming ADR-001); capability-level technical revalidation against the governed v16 target remains outstanding and is separately required before the ERPNext integration layer itself can be newly frozen.
- AR-002 formally dispositioned — **satisfied** (Resolved, Option A, Accepted ADR-015, Version 1.0); implementation design, Publication of `25_MultiTenant_Architecture.md`, and separate scoped implementation authorization remain outstanding and are separately required before the multi-tenant layer itself can be newly frozen.
- AR-004 formally dispositioned — **satisfied** (Resolved, Option C, 2026-09-20); Machine Scheduling module design and Job Cards Tier B's remaining AR-006 gate remain separately required before this layer itself can be newly frozen.
- AR-005 formally dispositioned.
- AR-006 formally dispositioned.
- AR-gated DocType mappings in `ERPNext_DocType_Mapping.md` updated consistently.
- ERPNext integration architecture reconciled (reserved `22_Integration_Architecture.md` and the `10_Integration_Architecture.md` working draft).
- Multi-tenant architecture reconciled (reserved `25_MultiTenant_Architecture.md` and the `04_MultiTenant_Architecture.md` working draft).
- The Marketplace plugin-entity cluster formally dispositioned (Section 14).
- The required Naming Registry Decision-Matrix items and Blueprint Open Questions (Section 13) either resolved or formally deferred.
- Frozen-document statuses and `docs/Documentation_Status.md` reconciled (Section 16).
- All proposed baseline documents complete the documented Review → Approval → Published process in `docs/Documentation_Workflow.md`.

Medium/Low terminology items (AR-007, AR-008, AR-009, AR-011, and the Naming Registry items) do **not** all require full resolution where formal deferral is acceptable under project governance.

---

## 22. Validation Checklist

- [x] The document declares a Layered Freeze, not a Full Freeze (Sections 4, 6).
- [x] The document itself is Approval, Version 1.0, and the Layered Freeze is effective within its defined scope (Section 1).
- [x] No other file was modified; no baseline source document status was promoted (Section 16).
- [x] AR-001 through AR-011 are represented; official register statuses preserved verbatim (Section 12).
- [x] ERPNext v16 is described only as a Working Assumption (Sections 5, 10.1, 19).
- [x] AR-gated layers are explicitly Excluded (Section 10).
- [x] The Marketplace plugin-entity cluster is explicitly deferred and recorded as uncovered (Section 14).
- [x] The Naming Registry backlog and Blueprint Open Questions are visible (Section 13).
- [x] Job Card Tier A/B is explicitly one aggregate and one DocType (Section 15).
- [x] Reporting / Configuration Studio ownership is consistent (Section 7).
- [x] Estimation → Sales is consistent (Section 7).
- [x] ERPNext Manufacturing Job Card is not reused (Section 7).
- [x] No Development Roadmap or implementation schedule is created (Sections 17–18).
- [x] No implementation authorization is implied (Section 19).
- [x] Full-freeze exit criteria are defined (Section 21).
- [x] Reviewed by Project Owner.

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-26 | Architecture Freeze (proposed) | Initial proposed Layered Architecture Freeze. Declares a layered (not full) freeze; records the Frozen conceptual baseline (Group A), Conditional References (Group B), and documents remaining Draft; Excludes the ERPNext-integration, multi-tenant, Machine, Quotation, and Material/Product-Template layers pending AR-001/002/004/005/006; reproduces AR-001–AR-011 register fields verbatim without resolving any; surfaces the Naming Registry and Blueprint governance backlogs; records the uncovered Marketplace plugin-entity cluster as later-phase deferred requiring formal disposition; clarifies Job Card Tier A/B as one aggregate and one DocType; records the Documentation_Status/index Published inconsistency without modifying any file; defines Development Roadmap readiness, the implementation-authorization boundary, and Full-Freeze exit criteria. No AR resolved, no ADR created, no status metadata changed, no roadmap or schedule created. |
| 0.1 | 2026-07-26 | Pre-approval correction | Targeted factual-consistency correction (no scope or disposition change). Removed self-referential wording that named and denied a non-existent Architecture Review identifier, from both the coverage text and the validation checklist. Corrected the `09_PrintOS_Modules.md` baseline entry to its authoritative source header version (1.0), and recorded that the source document's header-versus-Revision-History discrepancy must be reconciled in the later controlled promotion and status-alignment task. Refined the `08_Master_Data_Model.md` exclusion note to state it does not define ERPNext DocType ownership and that entity ownership is deferred to AR-006 and, where Machine/Machine Profile are involved, AR-004. No source document modified; no other file modified; document remains Draft, Version 0.1; the Layered Freeze remains a proposal and is not made effective; no Architecture Review item resolved or reclassified. |
| 1.0 | 2026-07-26 | Project Owner Approval | Formal Project Owner approval recorded. Document Control transitioned from Status: Draft to Status: Approval and Version: 0.1 to Version: 1.0, marking the first formally approved and effective governance baseline. The Layered Architecture Freeze (not a Full Architecture Freeze) is now effective within the scope and exclusions this document defines: the Frozen Baseline (Section 8), Conditional References (Section 9), and Excluded Layers (Section 10) are unchanged in membership and remain as previously defined. This approval does not authorize implementation, does not settle ERPNext v16 as a decided version, does not resolve any AR-001–AR-011 item, does not resolve unresolved DocType ownership or a tenant strategy, does not create a Development Roadmap, and does not promote any baseline source document to Published. Updated the title and the Section 1 "Note on effect," Section 4, Section 6, and Section 8 preamble to state the freeze is effective rather than proposed; marked the Section 22 "Reviewed by Project Owner" checklist item complete. No other checklist item, table, AR entry, exclusion, or source-document reference was altered. |
| 1.1 | 2026-07-27 | AR-001 Disposition Factual Synchronization | Synchronized this document with the formal Project Owner disposition of AR-001 (Option A, recorded in `Architecture_Review_Register.md`), reaffirming the Accepted ADR-001-ERPNext-Framework decision. Recorded ERPNext v16, with the corresponding Frappe v16 major version, as the governed target; simultaneous v15/v16 support noted as out of scope; minor/patch selection noted as centrally governed. Updated the Section 4 diagram (added an explanatory note, diagram box itself unchanged), Section 8 Conditional References table (Fit Analysis and Module Dependency Matrix rows, including the Matrix's cited Version 0.3 → 0.4), Section 10.1 Excluded-layer statement, Section 12 official AR-001 table row (Register Status Open → Resolved, Blocks Development Yes → No, Priority preserved at Critical), Section 18.2 roadmap-finalization list, Section 19 implementation-authorization boundary, Section 20 risk-mitigation bullet, and Section 21 Full-Freeze exit criteria (AR-001 item marked satisfied) to replace stale "Open / Critical / development-blocking / Working Assumption" wording with governed-target and pending-technical-revalidation wording. The ERPNext integration layer's Excluded status (Section 10) is explicitly **preserved and not promoted** to the Frozen baseline: it remains Excluded because capability-level Fit, Gap, DocType, hook, event, permission, deployment, and integration claims still require technical revalidation against the governed v16 target, not because the major version is undecided. The Frozen Baseline (Section 8's Group A membership), Conditional References membership, and all other Excluded Layers (10.2–10.7) are unchanged. The Section 6 "Decision explicitly not made" scope statement, the Section 22 Validation Checklist, and all prior Revision History entries are preserved unchanged as historical record. No AR item was resolved or reprioritized by this document (AR-001 was resolved separately, by the Project Owner, in the Architecture Review Register); AR-002 through AR-011 are unaffected. No technical Fit, Gap, or DocType revalidation was performed or claimed. No document was published. No implementation was authorized. |
| 1.2 | 2026-07-28 | AR-002 Disposition Factual Synchronization | Project Owner-approved factual baseline synchronization of this document with the formal disposition of AR-002 (Option A, recorded in `Architecture_Review_Register.md`, Version 0.4). Recorded one isolated Frappe site and one isolated operational database per Tenant, with Tenant formally distinct from Company, per Accepted ADR-015-Tenant-Company-Multi-Tenancy-Model (Version 1.0) and `../blueprint/25_MultiTenant_Architecture.md` (Approval, Version 1.0, not Published). Added a Project Owner Synchronization Approval note to Section 1 recording the approved scope and Version 1.1 → 1.2 transition, without reopening or repeating the Section 22 Validation Checklist or the original Project Owner Approval record (Revision History, Version 1.0), both preserved unchanged. Updated the Section 4 diagram (added an explanatory note, diagram box itself unchanged), Section 8 Conditional References table (Configuration Studio and designer-catalog rows, and Module Dependency Matrix row, including the Matrix's cited Version 0.4 → 0.5), Section 10.2 Excluded-layer statement (rewritten to record the resolved topology and Tenant/Company distinction while preserving the layer's Excluded status pending implementation design, Publication, and separate authorization), Section 10.7 (corrected the stale "reserved-but-unwritten" description of `25_MultiTenant_Architecture.md`, which now exists at Approval, Version 1.0, while preserving its Excluded status pending Publication, and preserving `04_MultiTenant_Architecture.md`'s unreconciled-working-draft status as a separate, later controlled task), Section 12 official AR-002 table row (Register Status Open → Resolved, Blocks Development Yes → No, Priority preserved at Critical), Section 18.2 roadmap-finalization list, Section 19 implementation-authorization boundary (added an AR-002 sentence and a Job Card Tier A sentence), and Section 21 Full-Freeze exit criteria (AR-002 item marked satisfied) to replace stale "Open / Critical / development-blocking / not frozen" wording with governed-topology and pending-implementation-authorization wording. Recorded that Job Card Tier A is Company-scoped inside an isolated Tenant site, with site identity implicit and no separate Tenant field required, and that central customer identity, cross-tenant analytics, and Tenant Override implementation are explicitly deferred pending separate future governance rather than permanently rejected. The multi-tenant layer's Excluded status (Section 10) is explicitly **preserved and not promoted** to the Frozen baseline: it remains Excluded because provisioning, tenant lifecycle, deployment, and other implementation mechanisms remain undesigned and unauthorized, and Blueprint 25 remains unpublished, not because the topology is undecided. The Frozen Baseline (Section 8's Group A membership), Conditional References membership, and all other Excluded Layers (10.1, 10.3–10.6) are unchanged. The Section 6 "Decision explicitly not made" scope statement, the Section 22 Validation Checklist, and all prior Revision History entries are preserved unchanged as historical record. No AR item was resolved or reprioritized by this document (AR-001 and AR-002 were resolved separately, by the Project Owner, in the Architecture Review Register); AR-003 through AR-011 are unaffected. No Architecture or Business Review was repeated for this factual update. No document was published. No implementation was authorized; every roadmap workstream remains Not Authorized; Job Card Tier A remains Not Authorized; Full Architecture Freeze remains unachieved. |
| 1.4 | 2026-09-20 | AR-004 Disposition Factual Synchronization | Project Owner-approved bounded current-state synchronization of this Approval-stage document with the formal disposition of AR-004 (Option C, recorded in `Architecture_Review_Register.md`, Version 0.6). Updated the AR-004 table row's Register Status/Blocks Development columns (reproduced verbatim from the Register) from Open/Partial to Resolved/No, Priority unchanged at High. Recorded that Machine and Machine Profile are both Custom PrintOS DocTypes, per `../blueprint/16_Print_Machine_Model.md`, Version 0.1 (an existing, previously empty Placeholder — no new file created). Added a Section 4 diagram Note recording AR-004's Resolution, matching the AR-001/AR-002 note precedent. Corrected Section 10.3 (Machine ownership), the Group A row for `08_Master_Data_Model.md`, the Conditional References rows for `ERPNext_DocType_Mapping.md` and `ERPNext_Gap_Analysis.md`, Section 18.2's roadmap-readiness item, and Section 21's exit criterion. This is not a repeated or new Architecture Review or Business Review of this document; a bounded Consistency Review and Documentation Governance check were applied, confirming the correction does not reopen the Frozen baseline or contradict any other current-state citation. The Section 22 Validation Checklist and original Project Owner Approval record are preserved unchanged as historical record. No frozen scope, Excluded-layer classification, or Full-Freeze exit criterion changed beyond formally satisfying AR-004's own exit criterion; Machine Scheduling module design and Job Cards Tier B's remaining AR-006 gate remain separately required. This document's own Status remains **Approval**; no implementation authorized. AR-001 through AR-003 remain Resolved as previously recorded; AR-005 through AR-011 unchanged. |
| 1.3 | 2026-09-19 | AR-003 Disposition Factual Synchronization | Project Owner-approved factual synchronization of this document with the formal disposition of AR-003 (Option A, recorded in `Architecture_Review_Register.md`, Version 0.5). Updated the AR-003 table row's Register Status/Blocks Development columns (reproduced verbatim from the Register) from Open/Partial to Resolved/No, Priority unchanged at High. Recorded that seven terms are mapped/confirmed/rejected against existing Approved concepts and AI Assistant is registered as Proposed only. Corrected the Conditional References table's Business Entity Inventory row: Print Specification is retained only as a Not-Adopted traceability entry; the AI cluster (Conversation, Prompt, Knowledge Source, Recommendation, AI Action) remains unregistered but outside AR-003, with no covering Architecture Review item; Approval Record's naming remains outside AR-003, a separate, currently unassigned governance question; AR-003 was removed as the responsible unresolved review item for these residual questions, with the conditional-reference membership and frozen/excluded scope preserved unchanged. This is not a repeated or new Architecture Review or Business Review of this document; the Section 22 Validation Checklist and original Project Owner Approval record are preserved unchanged as historical record. No frozen scope, Excluded-layer classification, or Full-Freeze exit criterion changed beyond these two corrections; no AI Assistant architecture or implementation is permitted; no other layer remains blocked by AR-003. No implementation authorized. AR-001, AR-002, and AR-004 through AR-011 unchanged. |
