# 08 — Performance Architecture

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

Describe the architectural approach to performance in PrintOS — where caching, background processing, and query discipline are applied — consolidating performance-relevant rules that otherwise sit scattered across `docs/standards/Performance_Standards.md` and individual Configuration/Technical documents.

---

# Scope

Covers system-level performance architecture: caching strategy, background job usage, query discipline, and reporting/dashboard performance. Does not restate specific numeric targets or benchmarks (see `docs/standards/Performance_Standards.md`, which remains authoritative for those).

---

# Background

Performance risk in an ERPNext-based system concentrates in a few predictable places: unbounded reports over large tables, synchronous work that should be backgrounded, and dashboards aggregating data on every page load. This document exists to state, at the architecture level, how PrintOS avoids each.

---

# Main Content

## Caching Strategy

- Redis is used for session storage, generic Frappe caching, and background job queuing (see [01_System_Architecture.md](01_System_Architecture.md)).
- Dashboard widgets and reports that aggregate large datasets should use cached or scheduled computation rather than real-time aggregation on every view, per [../configuration/06_Dashboard_Designer.md](../configuration/06_Dashboard_Designer.md) and [../configuration/10_Report_Designer.md](../configuration/10_Report_Designer.md).

## Background Processing

- Any operation not required to complete before the user receives a response (notifications, integration calls, bulk recalculation) runs as a Background Job, per `Naming_Registry.md` Section 8's definition of Background Job, rather than blocking the request path.
- Automation Rules ([../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md)) that fire on high-frequency events should be evaluated for whether they belong in the synchronous request path or should be deferred to a background job.

## Query Discipline

- All data access uses the Frappe ORM or parameterized SQL, never string-interpolated queries (see [../database/01_Data_Architecture.md](../database/01_Data_Architecture.md)) — this is a security rule and a performance rule simultaneously, since parameterized queries are also what allows query plan caching.
- Reports over large, growing tables (e.g. Job Card history) should filter by an indexed, bounded field (date range, Company) by default rather than allowing unbounded full-table scans.

## Reporting & Dashboard Performance

Reports feeding dashboards are explicitly called out in [../configuration/06_Dashboard_Designer.md](../configuration/06_Dashboard_Designer.md) as needing caching consideration when aggregating large datasets — this document elevates that from a per-widget note to a system-wide default: any cross-context aggregate (e.g. company-wide production throughput) is a caching candidate by default, not an exception.

---

# Architecture Notes

This document does not set specific performance budgets (e.g. "p95 response time under 300ms") — those belong in `docs/standards/Performance_Standards.md` once defined with real measurement. This document only establishes where performance-sensitive decisions are architecturally anchored.

---

# Future Considerations

- MachineIQ's future analytical workloads are expected to run against a separate analytical data path (e.g. a read replica or dedicated analytics store) rather than the primary transactional MariaDB instance, to avoid competing with production traffic — to be confirmed once MachineIQ is scoped.
- As multi-tenant operation ([04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md)) is resolved, performance isolation between tenants (a noisy-tenant problem) becomes a first-class concern.

---

# Open Questions

- Should `docs/standards/Performance_Standards.md` define concrete response-time/throughput budgets now, to give this document something measurable to reference, or is that premature before real usage data exists?

---

# Related Documents

- `docs/standards/Performance_Standards.md`
- [../configuration/06_Dashboard_Designer.md](../configuration/06_Dashboard_Designer.md)
- [../configuration/10_Report_Designer.md](../configuration/10_Report_Designer.md)
- [../database/01_Data_Architecture.md](../database/01_Data_Architecture.md)
- [01_System_Architecture.md](01_System_Architecture.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
