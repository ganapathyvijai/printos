# Technology Stack

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

This document records the current and anticipated future technology stack for PrintHub/PrintOS, along with the reasoning, trade-offs, and migration considerations behind each choice.

---

# Scope

This document covers technology selection at the platform level: operating systems, development tooling, backend, frontend, and data storage technologies, current and future.

This document does not cover detailed DevOps/deployment architecture, which is reserved for a future document.

---

# Background

PrintOS is built as a long-term, upgrade-safe ERP product on ERPNext/Frappe. Technology choices are made to balance proven stability today (ERPNext, MariaDB) against a clear path toward future scale and capability (PostgreSQL, ClickHouse, FastAPI, MachineIQ).

---

# Main Content

## Current Technology Stack

| Category | Technology | Reason |
|---|---|---|
| Operating System | Windows 11 | Primary developer environment |
| Operating System | Ubuntu 24.04 (WSL2) | Linux-compatible runtime for ERPNext/Frappe development |
| Development | VS Code | Primary IDE |
| Development | Docker Desktop | Containerized local development |
| Development | Git / GitHub | Version control and collaboration |
| Backend Framework | ERPNext v16 | Mature, proven ERP framework; avoids building ERP fundamentals from scratch |
| Backend Framework | Frappe Framework | Underlying application framework for ERPNext; provides extension mechanisms |
| Backend Language | Python | Native language of Frappe/ERPNext |
| Database | MariaDB | Default, supported database for Frappe/ERPNext |
| Cache/Queue | Redis | Used by Frappe for caching and background jobs |

## Future Technology Stack

| Category | Technology | Reason |
|---|---|---|
| Frontend | Next.js | Anticipated for future web-facing portals (freelancer, supplier, marketplace) |
| Mobile | React Native | Anticipated for future mobile applications |
| Database | PostgreSQL | Considered for future services requiring capabilities beyond MariaDB |
| Analytics Database | ClickHouse | Considered for future MachineIQ analytics workloads |
| API Framework | FastAPI | Considered for future standalone Python services outside `printos_core` |
| Analytics/AI | MachineIQ | Future machine intelligence and analytics capability |
| Edge/CDN | Cloudflare | Considered for future edge delivery and security |

## Current Stack: Pros and Cons

| Technology | Pros | Cons |
|---|---|---|
| ERPNext v16 | Mature ERP fundamentals; large module coverage; active community | Framework constraints must be respected to preserve upgrade safety |
| MariaDB | Well-supported by Frappe; stable, predictable | Less suited to analytical/OLAP workloads at scale |
| Redis | Fast caching and queueing; native Frappe integration | Adds an operational dependency to manage |

## Future Stack: Pros and Cons

| Technology | Pros | Cons |
|---|---|---|
| Next.js | Modern React-based framework; strong ecosystem for future portals | Introduces a second frontend stack alongside ERPNext's default UI |
| PostgreSQL | Richer feature set for future services | Would run alongside, not replace, MariaDB — added operational complexity |
| ClickHouse | Purpose-built for analytics at scale | Additional infrastructure to operate and secure |
| FastAPI | Lightweight, fast for standalone Python services | Only justified once logic genuinely needs to live outside `printos_core` |

## Future Migration Strategy

No migration away from ERPNext/MariaDB is planned for Phase 1. Future technologies (PostgreSQL, ClickHouse, FastAPI, Next.js) are expected to be introduced additively — as new services or interfaces alongside the existing ERPNext/`printos_core` foundation — rather than as replacements. Any future migration affecting the core ERP data store would require its own Blueprint document and Architecture Decision Record before implementation, per the Documentation-First philosophy in [00_Master_Index.md](00_Master_Index.md).

---

# Architecture Notes

Technology choices consistently favor proven stability for the ERP core (ERPNext, MariaDB) while keeping the door open for specialized future technology where the current stack is not fit for purpose (e.g., ClickHouse for analytics). This mirrors the Framework vs Product separation described in [04_System_Architecture.md](04_System_Architecture.md): new technology is added around `printos_core`, not injected into ERPNext core.

---

# Future Considerations

As future phases (Freelancer Portal, Supplier Portal, Service Engineers, Marketplace) are built, this document should be updated to reflect which future technologies have moved from "anticipated" to "adopted," with corresponding Architecture Decision Records in DECISIONS.md.

---

# Open Questions

- At what trigger point does the platform adopt PostgreSQL or ClickHouse rather than continuing with MariaDB alone?
- Will Next.js-based portals be a single unified frontend or separate applications per user group (G3–G5)?

---

# Related Documents

- [00_Master_Index.md](00_Master_Index.md)
- [04_System_Architecture.md](04_System_Architecture.md)
- [03_Product_Roadmap.md](03_Product_Roadmap.md)

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
