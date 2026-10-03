# Implementation Documentation — Master Index

Version:
0.11

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-10-04

---

# Purpose

Master navigation hub for `docs/implementation/` — the planning documents that translate Blueprint, Business, Architecture, Technical, Database, and Configuration documentation into a concrete, sequenced delivery plan. This category answers "how and in what order do we build this," never "what is this" or "why is it designed this way."

---

# Scope

Covers navigation, phase overview, document dependencies, and status tracking for all documents in `docs/implementation/`. Does not define architecture, business rules, or naming — every substantive claim in this folder must trace to an existing Blueprint, Business, Architecture, Technical, Database, Configuration, Standards, or ADR document. Where no such source exists, the gap is documented as an Open Question, never invented.

---

# Background

Per `docs/Documentation_Workflow.md` Section 3, Implementation sits below Reviews and above Testing/Deployment in the Documentation Hierarchy — it is downstream of every other category. This index exists because, until now, no document translated the (now substantial) Blueprint/Business/Architecture/Technical/Database/Configuration documentation set into an actual build sequence.

---

# Main Content

## Document Status Legend

| Status | Meaning |
|---|---|
| Draft | Actively being written; not yet safe to build against, per `docs/Documentation_Workflow.md` Section 5 |
| Placeholder | File exists but is empty; reserved for planned content |

## Document Index

| # | Document | Purpose | Status |
|---|---|---|---|
| 00 | 00_Implementation_Index.md | This document — navigation and index | Draft |
| 01 | [01_Phase_1_Roadmap.md](01_Phase_1_Roadmap.md) | Technical implementation phases (distinct from Blueprint's business roadmap phases — see that document's Terminology & Sequencing Conflicts section) | Draft |
| 02 | [02_Module_Implementation_Order.md](02_Module_Implementation_Order.md) | Module-by-module build sequence and dependencies | Draft |
| 03 | [03_ERPNext_Mapping.md](03_ERPNext_Mapping.md) | Business concept → ERPNext object mapping (Reuse/Extend/Custom) | Draft |
| 04 | [04_Customization_Strategy.md](04_Customization_Strategy.md) | How ERPNext is customized, governed, and reviewed | Draft |
| 05 | [05_Data_Migration_Strategy.md](05_Data_Migration_Strategy.md) | Migration principles, phases, validation, rollback | Draft |
| 06 | [06_Testing_Strategy.md](06_Testing_Strategy.md) | Testing philosophy and pyramid across Clean Architecture layers | Draft |
| 07 | [07_Deployment_Strategy.md](07_Deployment_Strategy.md) | Deployment planning: environments, backup, monitoring, CI/CD readiness | Draft |
| 08 | [08_Go_Live_Checklist.md](08_Go_Live_Checklist.md) | Go-live readiness checklist and sign-off matrix | Draft |
| 09 | 09_Coding_Standards_Implementation.md | Applying `docs/standards/Coding_Standards.md` in practice | Placeholder |
| 10 | 10_Project_Execution_Plan.md | Overall execution plan tying phases to delivery | Placeholder |
| 11 | 11_Risk_Register.md | Project-level risk tracking | Placeholder |
| 12 | 12_Project_Milestones.md | Project Milestones governance framework — two-track status model, Not Assessed/Assessment In Progress/Complete/Blocked vocabulary, contributor-propose/Owner-accept authority (see also `docs/milestones/`); records adopted Project Owner policy | Draft |
| 13 | 13_Sprint_Strategy.md | Sprint planning approach | Placeholder |
| 14 | 14_Release_Checklist.md | Per-release readiness checklist (distinct from one-time Go-Live) | Placeholder |
| 15 | 15_Post_GoLive_Support.md | Hypercare and post-go-live support model | Placeholder |
| — | [Module_Dependency_Matrix.md](Module_Dependency_Matrix.md) | What must exist before each module can be implemented, by dependency category; direct blockers vs. transitive delays; formalizes the approved implementation dependency review | Draft |
| — | [Environment_Version_Manifest.md](Environment_Version_Manifest.md) | Proposed version-identity evidence schema for Frappe, ERPNext, and `printos_core` — no value populated; per `Documentation_Workflow.md:118` cannot yet be relied upon and does not resolve any milestone criterion; review outcomes recorded in `docs/reviews/Environment_Version_Manifest_Review.md` | Revision |
| — | [Architecture_Freeze.md](Architecture_Freeze.md) | Effective Layered Architecture Freeze, applying only to the frozen conceptual scope the document defines, defining AR-gated excluded layers, conditional references, governance backlogs, and Full Freeze exit criteria. It is not a Full Architecture Freeze, does not authorize implementation, and constrains but does not replace the future Development Roadmap. (Approval, Version 1.3.) | Approval |

## Document Dependency Graph

```mermaid
flowchart TB
    Idx["00 Implementation Index"] --> Roadmap["01 Phase 1 Roadmap"]
    Roadmap --> ModOrder["02 Module Implementation Order"]
    ModOrder --> ERPMap["03 ERPNext Mapping"]
    ERPMap --> Custom["04 Customization Strategy"]
    ModOrder --> Migration["05 Data Migration Strategy"]
    Custom --> Testing["06 Testing Strategy"]
    Migration --> Testing
    Testing --> Deploy["07 Deployment Strategy"]
    Migration --> Deploy
    Deploy --> GoLive["08 Go-Live Checklist"]
    Testing --> GoLive
    Migration --> GoLive
    GoLive --> Support["15 Post Go-Live Support (Placeholder)"]
    Roadmap --> ExecPlan["10 Project Execution Plan (Placeholder)"]
    ExecPlan --> Sprint["13 Sprint Strategy (Placeholder)"]
    ExecPlan --> Milestones["12 Project Milestones (Draft)"]
    ExecPlan --> Risk["11 Risk Register (Placeholder)"]
```

## Roadmap Overview

This folder plans delivery of Blueprint Phase 1 (PrintOS ERP, per [../blueprint/03_Product_Roadmap.md](../blueprint/03_Product_Roadmap.md)) using the technical phase structure defined in [01_Phase_1_Roadmap.md](01_Phase_1_Roadmap.md). That document explicitly documents — rather than resolves — a phase-numbering conflict between its own technical phases and the Blueprint's business phases; readers should consult it directly before assuming any phase alignment.

## Status Tracking

| Category | Documents Complete | Documents Placeholder | Overall |
|---|---|---|---|
| Implementation (this folder) | 10 of 16 (00, 01–08, Module_Dependency_Matrix) | 7 of 16 (09–15) | Early Draft |

## Cross-Cutting Rule (Non-Negotiable)

Every document in this folder:

- Must not redefine architecture (`docs/architecture/`, `docs/technical/`) — it may only reference and sequence it.
- Must not redefine business rules (`docs/blueprint/`, `docs/business/`) — it may only reference and plan against them.
- Must not introduce new terminology (`docs/standards/Naming_Registry.md` is the sole naming authority) — any apparent gap or conflict is documented, with a citation to the governing ADR or Naming Registry entry, and left unresolved for the Project Owner/Architecture Review.

---

# Architecture Notes

This index and its constituent documents are deliberately downstream-only: they consume Blueprint, Business, Architecture, Technical, Database, and Configuration documentation as fixed inputs. Where implementation planning surfaces a gap in one of those upstream categories (e.g. Machine vs. Asset mapping in [03_ERPNext_Mapping.md](03_ERPNext_Mapping.md)), the correct response is to raise it as an Open Question there and escalate to Architecture Review — never to quietly decide it within an Implementation document.

---

# Future Considerations

- Documents 09–15 remain Placeholder and should be populated once 00–08 have been reviewed and the phase-numbering conflict in [01_Phase_1_Roadmap.md](01_Phase_1_Roadmap.md) has at least been acknowledged by Architecture Review, so later documents (Sprint Strategy, Project Execution Plan) aren't built on an unresolved numbering ambiguity.
- As Pending ADR items referenced throughout this folder (Tenant vs. Company, Purchasing vs. Procurement, Item vs. Material, Machine vs. Asset) are resolved, the affected Implementation documents ([02_Module_Implementation_Order.md](02_Module_Implementation_Order.md), [03_ERPNext_Mapping.md](03_ERPNext_Mapping.md)) must be updated accordingly.

---

# Open Questions

- Should documents 09–15 be drafted now as working outlines, or intentionally held back until 00–08 pass Architecture Review, given several depend on decisions ([01_Phase_1_Roadmap.md](01_Phase_1_Roadmap.md)'s phase-numbering conflict) that are not yet resolved?
- Who owns closing the Pending ADR items this folder repeatedly surfaces as blocking (see [03_ERPNext_Mapping.md](03_ERPNext_Mapping.md) Open Questions in particular)?

---

# Related Documents

- [../Documentation_Map.md](../Documentation_Map.md)
- [../Documentation_Status.md](../Documentation_Status.md)
- [../blueprint/00_Master_Index.md](../blueprint/00_Master_Index.md)
- [../architecture/00_Architecture_Index.md](../architecture/00_Architecture_Index.md)
- [../configuration/00_Master_Index.md](../configuration/00_Master_Index.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)
- [../decisions/00_ADR_Index.md](../decisions/00_ADR_Index.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-23 | Initial | Initial working draft. Indexed documents 00–08 (populated) and 09–15 (Placeholder), documented status tracking and the cross-cutting non-redefinition rule. |
| 0.1 | 2026-07-26 | Freeze Registration | Registered the Draft Layered Architecture Freeze proposal (`Architecture_Freeze.md`, Draft 0.1) in the Document Index. Navigation update only; no lifecycle status changed and no freeze activation occurred. Header Version preserved at 0.1 (this index's prior registration changes were not version-incremented; convention unclear). |
| 0.2 | 2026-07-26 | Freeze Activation Synchronization | Synchronized the `Architecture_Freeze.md` registration from Draft 0.1 to Approval 1.0, reflecting formal Project Owner approval. The Layered Architecture Freeze is now recorded as effective for its declared frozen conceptual scope; the entry states it is not a Full Architecture Freeze and does not authorize implementation. No other Document Index entry changed. No document was published and no implementation was authorized. |
| 0.3 | 2026-09-20 | Architecture Freeze Version Synchronization | Updated the `Architecture_Freeze.md` Document Index entry's version citation from Version 1.0 to **Approval, Version 1.3**, reflecting that document's subsequent bounded reference-only corrections (AR-001/AR-002 disposition synchronization, and this task's AR-003 disposition synchronization). The entry continues to state it remains a **Layered Architecture Freeze, not a Full Freeze**, is **not Published**, and **grants no implementation authority**. No document count, navigation structure, dependency graph, or other Document Index entry was changed. No document was published and no implementation was authorized. |
| 0.4 | 2026-09-27 | Project Milestones Registration | Updated the `12_Project_Milestones.md` Document Index entry (row 12) from Placeholder to **Draft**, reflecting its population as the Project Milestones governance framework document (Version 0.1; two-track status model, Not Assessed/Assessment In Progress/Complete/Blocked vocabulary, contributor-propose/Owner-accept authority, and framework-authored-first sequencing all adopted as Project Owner decisions). Updated the corresponding Mermaid Document Dependency Graph node label from "(Placeholder)" to "(Draft)". No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.5 | 2026-09-29 | Environment Version Manifest Registration | Added a new unnumbered Document Index row for `Environment_Version_Manifest.md` (Draft, Version 0.1), a schema-only, Draft version-identity evidence document with no value populated; noted it cannot yet be relied upon per `Documentation_Workflow.md:118`. No existing Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.6 | 2026-10-02 | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Draft to Review, reflecting its lifecycle transition and the new durable review record `docs/reviews/Environment_Version_Manifest_Review.md`. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.7 | 2026-10-03 | Environment Version Manifest Revision Status | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Review to Revision, reflecting its return to Revision at Version 0.6 to address required changes recorded in `docs/reviews/Environment_Version_Manifest_Review.md`. The committed Version 0.6 row above has three cells in this four-column table (its title cell is missing); it is preserved unchanged as historical record. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.8 | 2026-10-03 | Environment Version Manifest Review Status | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Revision to Review, reflecting its return to Review at Version 0.7 after the required changes recorded in `docs/reviews/Environment_Version_Manifest_Review.md` were addressed; re-review outcomes are pending. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.9 | 2026-10-03 | Environment Version Manifest Revision Status After AI Review | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Review to Revision, reflecting its return to Revision at Version 0.8 after AI Review of Version 0.7 found a required naming correction, recorded in `docs/reviews/Environment_Version_Manifest_Review.md`. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.10 | 2026-10-03 | Environment Version Manifest Review Status | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Revision to Review, reflecting its return to Review at Version 0.9 after the required naming correction recorded in `docs/reviews/Environment_Version_Manifest_Review.md` was addressed; review outcomes for Version 0.9 are pending. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |
| 0.11 | 2026-10-04 | Environment Version Manifest Returned to Revision | Updated the `Environment_Version_Manifest.md` Document Index row's Status from Review to Revision, reflecting its return to Revision at Version 0.10 after independent review of Version 0.9, recorded in `docs/reviews/Environment_Version_Manifest_Review.md`, found a required Consistency clarification that is corrected in Version 0.10 and a Standards finding on missing historical revision authorship that remains open. No other Document Index entry, dependency edge, or navigation structure changed. No document was published and no implementation was authorized. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
