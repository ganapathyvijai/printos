# Performance Standards

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-22

---

# Purpose

Define baseline expectations for performance-conscious design in `printos_core`, so the platform remains responsive as data volume and tenant count grow.

---

# Scope

Covers coding and query practices that affect performance. Does not cover infrastructure sizing or scaling strategy (reserved for future `docs/blueprint/24_Deployment_Architecture.md` and `25_MultiTenant_Architecture.md`, per [ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)).

---

# Standards

- Avoid N+1 query patterns; batch-fetch related records where Frappe's ORM allows it.
- Expensive computations (e.g., large report aggregation) should be designed to run asynchronously (background jobs) rather than blocking user-facing requests.
- Indexes are added deliberately for fields used in frequent filtering/sorting (e.g., Job Card status, Sales Order customer), following Frappe's indexing mechanisms.
- Pagination is used for any list/report endpoint that could return unbounded result sets.
- Redis caching (already part of the stack per `docs/blueprint/07_Technology_Stack.md`) is used for expensive, frequently-read, rarely-changed data rather than recomputing on every request.
- Performance-sensitive changes are profiled before being considered complete, not assumed acceptable.

---

# Rationale

Print shop operations (job scheduling, inventory checks) are used interactively on the shop floor; slow responses directly harm adoption. Addressing performance patterns early avoids costly rework once data volumes grow across multiple tenants.

---

# Related Documents

- [Coding_Standards.md](Coding_Standards.md)
- [Database_Standards.md](Database_Standards.md)
- `docs/blueprint/07_Technology_Stack.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved paths from `14_Deployment_Architecture.md`/`15_MultiTenant_Architecture.md` to `24_Deployment_Architecture.md`/`25_MultiTenant_Architecture.md` per ADR-010|
