# Business Requirements

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-18

---

# Purpose

This document defines what PrintOS must do (functional requirements), the qualities it must have (non-functional requirements), the business rules that constrain it, and the current versus future scope of the product.

---

# Scope

This document covers Phase 1 (PrintOS ERP) requirements and scope, and previews future-phase scope for context.

This document does not cover implementation details, DocType schemas, or API contracts — those belong in future documents (see [00_Master_Index.md](00_Master_Index.md), reserved 05/06).

---

# Background

PrintOS Phase 1 targets print shops (G2) as ERP users, with customers (G1) existing only as ERP customer records — not as portal users. Marketplace and portal functionality for other user groups is intentionally postponed to later phases, per [01_Project_Vision.md](01_Project_Vision.md) and CHATGPT.md.

---

# Main Content

## Target Users

| Group | Description | Status in Current Phase |
|---|---|---|
| G1 | Public Buyers / Customers | Exist only as ERP customer records; no portal access |
| G2 | Print Shops | Primary active users of PrintOS ERP (current phase) |
| G3 | Freelancers | Future phase (Phase 2) |
| G4 | Suppliers | Future phase (Phase 3) |
| G5 | Service Engineers | Future phase (Phase 4) |

## Current Phase

Phase 1 — PrintOS ERP for print shops (G2). See [03_Product_Roadmap.md](03_Product_Roadmap.md) for full phase sequencing.

## Functional Requirements

| ID | Requirement |
|---|---|
| FR-1 | PrintOS shall provide ERP functionality for print shop operations, built on ERPNext. |
| FR-2 | PrintOS shall represent customers (G1) as ERP customer records within the print shop's instance. |
| FR-3 | PrintOS shall isolate all print-industry-specific business logic within `printos_core`. |
| FR-4 | PrintOS shall not require modification of ERPNext core to deliver industry-specific workflows. |
| FR-5 | PrintOS shall expose future extension points for freelancer, supplier, service engineer, and marketplace functionality without requiring re-architecture. |

## Non-Functional Requirements

| ID | Requirement |
|---|---|
| NFR-1 | Upgrade Safety — PrintOS must remain compatible with future ERPNext version upgrades. |
| NFR-2 | Maintainability — Codebase must remain clean, modular, and maintainable for many years. |
| NFR-3 | Security — No secrets, passwords, or credentials hardcoded; environment variables used throughout. |
| NFR-4 | Extensibility — Architecture must support future multi-tenant SaaS delivery. |
| NFR-5 | Documentation Currency — Architecture changes must be reflected in the Blueprint before implementation. |

## Business Rules

- ERPNext core must never be modified; all customization occurs through Frappe's extension mechanisms inside `printos_core`.
- Business concepts must not depend directly on ERPNext internals.
- Marketplace functionality (G1 as active platform users) is explicitly out of scope until Phase 5.

## Current Scope

- ERP functionality for print shops (G2), built on ERPNext v16 / Frappe Framework.
- Customer (G1) records managed as standard ERP customers only.

## Out-of-Scope Items

- Freelancer Portal (G3)
- Supplier Portal (G4)
- Service Engineer tools (G5)
- Marketplace / public buyer-facing platform (G1 as active users)
- Mobile applications
- MachineIQ (machine intelligence/analytics)

## Future Scope

See [03_Product_Roadmap.md](03_Product_Roadmap.md) for the phased introduction of the items listed above.

---

# Architecture Notes

The strict separation of "customer as ERP record" (current) versus "customer as platform user" (future Marketplace phase) is a deliberate scope boundary. It allows Phase 1 to ship a focused ERP product without prematurely committing to multi-sided marketplace architecture.

---

# Future Considerations

As later phases are scoped, this document should be extended (or split into per-phase requirement documents) to avoid becoming unwieldy as functional requirements multiply across five user groups.

---

# Open Questions

- Should later phases each get their own Business Requirements document, or extend this one?
- What are the specific non-functional targets (e.g., uptime, response time) for Phase 1 launch?

---

# Related Documents

- [00_Master_Index.md](00_Master_Index.md)
- [01_Project_Vision.md](01_Project_Vision.md)
- [03_Product_Roadmap.md](03_Product_Roadmap.md)
- [04_System_Architecture.md](04_System_Architecture.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
