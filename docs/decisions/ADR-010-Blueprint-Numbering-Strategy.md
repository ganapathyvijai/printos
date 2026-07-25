# ADR-010: Blueprint Numbering Strategy

Status:
Accepted

Date:
2026-07-22

---

## Problem

Several documents (`docs/standards/Naming_Registry.md`, `docs/standards/Performance_Standards.md`, `docs/standards/Release_Process.md`, `docs/standards/Security_Standards.md`, `docs/standards/Logging_Standards.md`, `docs/decisions/ADR-006-MultiTenant-Strategy.md`) reserved the Blueprint numbers 12–15 for a planned "Phase C" architecture set: Integration Architecture, Security Architecture, Deployment Architecture, and Multi-Tenant Architecture (with Data Architecture originally discussed for 11).

Independently of those reservations, `docs/blueprint/` was scaffolded with files 11–20 covering a different set of topics — Print Industry Model, PrintOS Product Catalog, Pricing Engine, Quotation Engine, Production Management, Print Machine Model, Inventory Model, Artwork Management, Job Card Model, and Dashboard Architecture. These occupy the same numbers (11–15) that the Phase C set was reserved under, without either side being aware of the other at the time.

The result: multiple already-Published documents cite future paths (e.g., `docs/blueprint/15_MultiTenant_Architecture.md`) that, if honored literally, would collide with the existing `15_Production_Management.md` scaffold.

## Options Considered

1. **Rename the existing 11–20 scaffold** to free up 12–15 for the originally planned Phase C topics. Rejected: the 11–20 scaffold is already in place and referenced as the current Blueprint structure in navigation (`docs/blueprint/00_Master_Index.md`); renaming published/scaffolded filenames this early would itself create broken links elsewhere and contradicts the explicit instruction not to rename Blueprint files.
2. **Interleave** Phase C topics into unused gaps within 11–20. Rejected: there are no gaps — 11–20 is fully occupied — and interleaving would make the numbering order fail to reflect a coherent reading sequence (Numbering Convention, `docs/blueprint/00_Master_Index.md`).
3. **Reserve new, higher numbers (21+) for the Phase C topics**, continuing the existing sequence rather than reusing any occupied number. Accepted.

## Decision

Data Architecture, Integration Architecture, Security Architecture, Deployment Architecture, and Multi-Tenant Architecture are reserved at numbers **21–25**, immediately following the current highest Blueprint document (`20_Dashboard_Architecture.md`), rather than at their originally discussed 11–15 range:

| Number | Reserved Topic | Status |
|---|---|---|
| 21 | Data Architecture | Future |
| 22 | Integration Architecture | Future |
| 23 | Security Architecture | Future |
| 24 | Deployment Architecture | Future |
| 25 | Multi-Tenant Architecture | Future |

No existing Blueprint file (00–20) is renamed or renumbered as a result of this decision.

## Consequences

- Every document that previously cited `12_Integration_Architecture.md`, `13_Security_Architecture.md`, `14_Deployment_Architecture.md`, or `15_MultiTenant_Architecture.md` as a future/reserved path is updated (Phase 3 of the same task that produced this ADR) to cite `22_Integration_Architecture.md`, `23_Security_Architecture.md`, `24_Deployment_Architecture.md`, and `25_MultiTenant_Architecture.md` respectively; `11_Data_Architecture.md` (never previously written into any file as a concrete path) is reserved directly at `21_Data_Architecture.md`.
- This ADR becomes the authority for future Blueprint numbering: any future document proposal must check this ADR and `docs/blueprint/00_Master_Index.md` before claiming a number, and any new "reserved future path" citation must reference a number confirmed here or in a successor ADR, not an ad hoc guess.
- `docs/blueprint/00_Master_Index.md` is updated in the same task to list 21–25 as Future, per this decision.
- `docs/standards/Naming_Registry.md` is updated to register the 11–20 scaffold's document names as Proposed terminology (see Naming Decision Matrix and Section 35+ additions), referencing this ADR.
- Future Blueprint documents beyond 25 continue the sequence (26+), avoiding any further reservation-vs-scaffold collision by always checking this ADR first.

## Migration Plan

1. Update `docs/blueprint/00_Master_Index.md` to list numbers 21–25 as Future (reserved per this ADR), and to accurately reflect the Published/Placeholder status of 00–20.
2. Update the six citing documents (`Naming_Registry.md`, `Performance_Standards.md`, `Release_Process.md`, `Security_Standards.md`, `Logging_Standards.md`, `ADR-006-MultiTenant-Strategy.md`) to reference the new 21–25 numbers instead of 12–15.
3. Register the 11–20 scaffold's document names in `docs/standards/Naming_Registry.md` as Proposed terminology, citing this ADR.
4. When Data/Integration/Security/Deployment/Multi-Tenant Architecture documents are eventually written, they are written directly to `21_Data_Architecture.md` through `25_MultiTenant_Architecture.md` — no further renumbering is anticipated.

## Related Documents

- `docs/blueprint/00_Master_Index.md`
- `docs/standards/Naming_Registry.md`
- `docs/decisions/ADR-006-MultiTenant-Strategy.md`
- `docs/standards/Performance_Standards.md`
- `docs/standards/Release_Process.md`
- `docs/standards/Security_Standards.md`
- `docs/standards/Logging_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
