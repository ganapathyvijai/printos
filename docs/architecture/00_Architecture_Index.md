# Architecture Documentation — Master Index

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

# Purpose

This document is the master index for PrintOS's cross-cutting architecture documentation — the technical deep-dives that describe *how* the system is built as a whole, complementing (not replacing) the business-facing Blueprint and the layer-by-layer Technical documentation.

---

# Scope

Covers system architecture, Clean Architecture, DDD application, multi-tenancy, extensibility, eventing, security, performance, deployment, and integration architecture — each as a dedicated deep-dive document. Does not cover business requirements or domain vocabulary (see `docs/blueprint/`), day-to-day engineering conventions (see `docs/standards/`), or the Configuration Studio's designer-level detail (see `docs/configuration/`).

## Relationship to Other Categories

This folder does not duplicate existing documentation. Where a topic already has a home elsewhere, this folder's document narrows to the cross-cutting architectural view and cross-references the authoritative source rather than restating it:

- `docs/blueprint/04_System_Architecture.md` remains the business-architecture-level system overview; [01_System_Architecture.md](01_System_Architecture.md) here is its deeper technical companion.
- `docs/technical/02_Clean_Architecture.md` and `docs/technical/03_Layer_Architecture.md` remain the authoritative layer definitions; [02_Clean_Architecture.md](02_Clean_Architecture.md) here consolidates the cross-cutting rationale and diagrams.
- `docs/blueprint/25_MultiTenant_Architecture.md` (reserved per [ADR-010](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)) remains the eventual authoritative Multi-Tenant Architecture document; [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md) here is a working draft pending that document's creation — see its own Scope note.
- `docs/blueprint/22_Integration_Architecture.md` and `24_Deployment_Architecture.md` (also reserved per ADR-010) are the eventual authoritative documents for those topics; [10_Integration_Architecture.md](10_Integration_Architecture.md) and [09_Deployment_Architecture.md](09_Deployment_Architecture.md) here are working drafts with the same caveat.

---

# Contents

| # | Document | Purpose |
|---|----------|---------|
| 01 | [System Architecture](01_System_Architecture.md) | End-to-end technical system view: ERPNext + printos_core + supporting services |
| 02 | [Clean Architecture](02_Clean_Architecture.md) | Cross-cutting rationale and enforcement of Clean Architecture across PrintOS |
| 03 | [DDD Architecture](03_DDD_Architecture.md) | How Domain-Driven Design is applied structurally, beyond the Blueprint's domain model |
| 04 | [Multi-Tenant Architecture](04_MultiTenant_Architecture.md) | Working draft of single/multi-tenant/hybrid design (see reserved Blueprint path above) |
| 05 | [Extensibility Architecture](05_Extensibility_Architecture.md) | How PrintOS is extended safely without modifying ERPNext core |
| 06 | [Event Architecture](06_Event_Architecture.md) | Domain events, publication, and consumption across bounded contexts |
| 07 | [Security Architecture](07_Security_Architecture.md) | Authentication, authorization, data protection, and secure defaults |
| 08 | [Performance Architecture](08_Performance_Architecture.md) | Performance budgets, caching, and scaling approach |
| 09 | [Deployment Architecture](09_Deployment_Architecture.md) | Working draft of environments, release, and infrastructure topology |
| 10 | [Integration Architecture](10_Integration_Architecture.md) | Working draft of external system integration patterns |
| — | [ERPNext Fit Analysis](ERPNext_Fit_Analysis.md) | Phase 1 classification of every business capability as Native/Extend/Customize/Plugin/Future against ERPNext; primary implementation reference for Architecture Freeze |
| — | [ERPNext Gap Analysis](ERPNext_Gap_Analysis.md) | Sizes the 28 capability gaps ERPNext does not adequately cover, building on the Fit Analysis and Architecture Review Register; primary input to ERPNext DocType Mapping |
| — | [Canonical Domain Model](Canonical_Domain_Model.md) | DDD organization of the Business Entity Inventory into Bounded Contexts, Aggregate Roots, Child Entities, Value Objects, and Domain Services; primary input to ERPNext DocType Mapping, Repository Design, and Application Services |
| — | [Plugin Architecture](Plugin_Architecture.md) | Uniform Ports & Adapters standard for every external integration (MachineIQ, Marketplace, AI Assistant, Communication, Commerce, Storage, Identity, Industrial, Analytics, and future categories) |

---

# Governing Principles

- ERPNext core is never modified; all architecture described here operates through Frappe's supported extension mechanisms.
- Every document here is a technical elaboration of decisions already recorded in `docs/blueprint` and `docs/decisions` — it does not introduce new binding architectural decisions of its own; where one is needed, it is raised as an Open Question and routed to an ADR.
- Documentation precedes implementation, per `CLAUDE.md` and `docs/Documentation_Workflow.md`.

---

# Future Considerations

- As `docs/blueprint/22_Integration_Architecture.md`, `24_Deployment_Architecture.md`, and `25_MultiTenant_Architecture.md` are formally authored, the corresponding documents in this folder should be reconciled with them (merged, superseded, or explicitly scoped apart) rather than left as parallel drafts indefinitely.

---

# Open Questions

- Should this folder ultimately be absorbed into `docs/blueprint`'s reserved 21–30 numbering range (per [ADR-010](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)), or remain a separate cross-cutting category permanently?

---

# Related Documents

- [../blueprint/00_Master_Index.md](../blueprint/00_Master_Index.md)
- [../technical/00_Master_Index.md](../technical/00_Master_Index.md)
- [../decisions/ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)
- [../Documentation_Map.md](../Documentation_Map.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-24 | ERPNext Fit Analysis | Added `ERPNext_Fit_Analysis.md` to Contents — the Phase 1 Native/Extend/Customize/Plugin/Future classification of every business capability, serving as the primary implementation reference for Architecture Freeze. |
| 1.2 | 2026-07-24 | ERPNext Gap Analysis | Added `ERPNext_Gap_Analysis.md` to Contents — sizes the capability gaps identified by the Fit Analysis into 28 detailed gap records, feeding forward into ERPNext DocType Mapping. |
| 1.3 | 2026-07-25 | Canonical Domain Model | Added `Canonical_Domain_Model.md` to Contents — organizes the Business Entity Inventory into Bounded Contexts, Aggregates, Child Entities, Value Objects, and Domain Services ahead of ERPNext DocType Mapping. |
| 1.4 | 2026-07-25 | Plugin Architecture | Added `Plugin_Architecture.md` to Contents — the uniform Ports & Adapters standard governing every current and future external integration. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
