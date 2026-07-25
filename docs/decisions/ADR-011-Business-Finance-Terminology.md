# ADR-011: Business/Finance Terminology

Status:
Accepted

Date:
2026-07-22

---

## Context

`docs/blueprint/06_Bounded_Contexts.md` and `docs/blueprint/09_PrintOS_Modules.md` both use **Accounts** as the approved bounded context and module name for financial transaction recording (invoicing, payments, GST). Separately, `docs/standards/Naming_Registry.md` (Section 11, Module Registry; Section 27, Naming Decision Matrix, item #4) recorded a request to use **Finance** instead, and `docs/business/14_Financial_Workflows.md` (Placeholder) uses **Financial Workflows** as its filename. No document has used "Financial Management" as a concrete name, though it was raised as a term to resolve in this task.

This left three candidate names in circulation for the same concept — Accounts, Finance, Financial Management — with no ADR resolving which is canonical.

## Problem Statement

Which name — **Accounts**, **Finance**, or **Financial Management** — is the canonical business, module, UI, API, and documentation term for PrintOS's financial transaction domain, and what happens to the other two?

## Decision Drivers

- ERPNext compatibility (Naming Principles, `Naming_Registry.md` Section 2): ERPNext's own core module for this domain is literally named **Accounts**.
- Consistency with already-Published, Approved Blueprint terminology (`06_Bounded_Contexts.md`, `09_PrintOS_Modules.md`), which use Accounts throughout.
- Avoiding a rename that would touch more already-Published content than necessary (Term Change Policy, `Naming_Registry.md` Section 33, favors minimal disruption when an existing Approved term is already well-established).
- Business readability: "Finance" is a common general-business term, but its breadth (treasury, budgeting, financial planning) exceeds what this bounded context actually does (transactional invoicing/payment/GST recording).

## Options Considered

1. **Adopt "Accounts" as canonical**, deprecating "Finance" and "Financial Management" as informal aliases. Consistent with ERPNext, requires no change to already-Published Blueprint content.
2. **Adopt "Finance" as canonical**, requiring `06_Bounded_Contexts.md`, `09_PrintOS_Modules.md`, and related cross-references to be updated. Rejected: contradicts ERPNext's own module naming, and would require rewriting multiple Published Blueprint documents for a purely stylistic preference.
3. **Adopt "Financial Management" as canonical.** Rejected: broader in scope than what this bounded context actually covers (invoicing, payment, GST), and introduces a three-word name inconsistent with the single-word convention used by every sibling context (CRM, Sales, Production, Inventory, Procurement, Warehouse, Dispatch, HR, Administration).

## Decision

**Accounts** is the canonical name at every layer:

| Layer | Approved Term |
|---|---|
| Business/Domain term | Accounts |
| Bounded Context name | Accounts Context |
| Module name | Accounts |
| UI wording | "Accounts" (menus, screens) |
| API wording | `printos_core.accounts.*` (namespace), resource `invoices`, `payments` |
| Documentation wording | Accounts |

**Deprecated aliases:** Finance, Financial Management. Neither is used in code, UI, API, or new documentation going forward. Existing informal use (e.g., the filename `docs/business/14_Financial_Workflows.md`) is not renamed by this ADR (see Migration Strategy) but should adopt Accounts-based terminology in its content once written.

## Consequences

- No currently-Published Blueprint document requires content changes, since they already use "Accounts."
- `docs/standards/Naming_Registry.md` Naming Decision Matrix item #4 is resolved by this ADR; the Registry itself is updated in a separate governance step (see Migration Strategy), not by this ADR directly.
- Future UI copy, API endpoints, and reports must use "Accounts," not "Finance."
- `docs/business/14_Financial_Workflows.md`'s eventual content should refer to the "Accounts" context/module by name even though its filename retains "Financial" for now.

## Alternatives Rejected

- "Finance" as canonical — rejected per Options Considered, item 2 (ERPNext incompatibility, unnecessary rewrite cost).
- "Financial Management" as canonical — rejected per Options Considered, item 3 (scope mismatch, naming-pattern inconsistency).
- Introducing both "Accounts" (technical) and "Finance" (business-facing) as a deliberate two-tier vocabulary — rejected: the Naming Principles (`Naming_Registry.md` Section 2) require single meaning per term and no synonyms in active use; a deliberate two-tier vocabulary would institutionalize exactly the ambiguity this ADR exists to remove.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #4.
2. In a subsequent, separate documentation task, `docs/standards/Naming_Registry.md` should be updated: move "Finance" and "Financial Management" into the Deprecated Names table (Section 28) with a reference to this ADR, and close out Matrix item #4. This ADR does not perform that update itself, consistent with this task's instruction to create ADRs only.
3. No Blueprint or Business document content is changed by this ADR. `docs/business/14_Financial_Workflows.md` may be renamed to an Accounts-consistent title in a future documentation governance pass, at the Project Owner's discretion — this ADR does not mandate a filename change.

## Related Documents

- `docs/blueprint/06_Bounded_Contexts.md` (Accounts Context)
- `docs/blueprint/09_PrintOS_Modules.md` (Accounts module)
- `docs/standards/Naming_Registry.md` (Section 6, Section 11, Section 27 item #4)
- `docs/business/14_Financial_Workflows.md` (Placeholder)

## Related ADRs

- [ADR-002-PrintOS-Core.md](ADR-002-PrintOS-Core.md) (ERPNext core never modified — supports ERPNext-compatibility rationale)
- [ADR-010-Blueprint-Numbering-Strategy.md](ADR-010-Blueprint-Numbering-Strategy.md) (precedent for this task's ADR-driven resolution pattern)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|

---

# Quality Checklist

- [x] Problem Statement clearly scoped
- [x] Decision Drivers stated
- [x] Options Considered documented, including rejected options
- [x] Decision is unambiguous and traceable to Naming Registry conflict
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Registry/Blueprint/Business edits)
- [x] Related Documents and Related ADRs cross-referenced
- [ ] Reviewed by Project Owner
