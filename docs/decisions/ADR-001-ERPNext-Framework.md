# ADR-001: Use ERPNext v16 as the Application Framework

Status:
Accepted

Date:
2026-07-18

---

## Context

PrintHub needs an ERP foundation for its first product, PrintOS, targeting print shops (G2) in Phase 1. Building generic ERP capability (accounting, inventory, sales, HR) from scratch would consume years of effort before any print-industry differentiation could be delivered. PrintHub is a long-term project, so the framework choice must support upgrade-safe customization over many years.

## Decision

Use ERPNext v16 (built on the Frappe Framework) as the application framework underlying PrintOS.

## Reasons

- Long-term project horizon favors a mature, actively maintained ERP foundation over custom-built fundamentals.
- ERPNext provides broad, proven ERP module coverage (accounting, inventory, sales, HR) out of the box.
- Frappe Framework provides supported extension mechanisms (custom apps, custom fields, hooks) that allow upgrade-safe customization.
- Enables PrintHub to focus engineering effort on print-industry differentiation rather than generic ERP plumbing.

## Consequences

- ERPNext core must never be modified directly (see [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md)); all customization is constrained to supported extension points.
- PrintOS inherits ERPNext's release cadence and upgrade considerations as an ongoing maintenance responsibility.
- Some print-industry workflows may require working around generic ERPNext UX rather than a fully bespoke experience, unless justified by clear business need (see `standards/UI_UX_Standards.md`).

## Related Documents

- [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md)
- `docs/blueprint/04_System_Architecture.md`
- `docs/blueprint/07_Technology_Stack.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
