# ADR-013: Quotation Terminology

Status:
Accepted

Date:
2026-07-22

---

## Context

`docs/blueprint/05_Domain_Model.md` and `docs/blueprint/09_PrintOS_Modules.md` already use **Quotation** as the Approved term for the priced proposal document issued to a Customer. `docs/standards/Naming_Registry.md` Section 6 and Section 25 (Synonym Registry) record "Quote" and "Estimate" as unresolved alternate forms, and this task additionally raises "Proposal" as a candidate. `docs/blueprint/06_Bounded_Contexts.md` separately lists "Cost Estimate" as a distinct Owned Business Object of the Estimation context (see [ADR-012-Estimating-Terminology.md](ADR-012-Estimating-Terminology.md)), which is not the same thing as the customer-facing document resolved here.

## Problem Statement

Which term — Quote, Quotation, Estimate, or Proposal — is canonical for the customer-facing priced-proposal document, at the business, customer-facing, internal-workflow, database, and API layers, and what industry-specific naming behavior should be expected?

## Decision Drivers

- Consistency with already-Approved, Published Blueprint content (`05_Domain_Model.md`, `09_PrintOS_Modules.md`), which uses "Quotation" throughout.
- Single meaning per term (`Naming_Registry.md` Section 2) — "Estimate" is also the informal print-industry colloquialism for this same document, creating ambiguity with the distinct "Cost Estimate" internal artifact (`06_Bounded_Contexts.md`).
- Avoiding introduction of a fourth, previously-unused term ("Proposal") that appears nowhere in the Blueprint and would only add vocabulary, not reduce it.

## Options Considered

1. **"Quotation" as canonical at every layer.** Matches existing Approved Blueprint usage; requires no rewrite of Published content.
2. **"Quote" as canonical.** Rejected: shorter and more colloquial, but "Quote" does not appear anywhere in the Blueprint as an approved term, and adopting it would require rewriting `05_Domain_Model.md` and `09_PrintOS_Modules.md` for a purely stylistic preference.
3. **"Estimate" as canonical.** Rejected: creates direct ambiguity with "Cost Estimate," the distinct internal-calculation Owned Business Object already named in `06_Bounded_Contexts.md`; conflating the customer-facing document with the internal costing artifact would violate the single-meaning-per-term principle.
4. **"Proposal" as canonical.** Rejected: introduces a fourth, previously unused term with no existing Blueprint basis, and is generic enough to be ambiguous with non-pricing sales proposals in other ERP contexts.

## Decision

**Quotation** is canonical at every layer:

| Layer | Approved Term |
|---|---|
| Business term | Quotation |
| Customer-facing term | Quotation |
| Internal workflow term | Quotation (no separate internal jargon) |
| Database naming (DocType) | Quotation |
| API naming | Resource `quotations` (plural, per `Naming_Registry.md` Section 15 pluralization rule) |
| Deprecated aliases | Quote, Estimate (as a synonym for this document only — see Print Industry Considerations), Proposal |

"Cost Estimate," the internal costing artifact named in `06_Bounded_Contexts.md`, is a **distinct concept** from Quotation and is not affected by this decision — it is the internal calculation that produces a Quotation's pricing, not the customer-facing document itself.

### Print Industry Considerations

Print, signage, and packaging industry customers and staff commonly say "estimate" colloquially to mean this same document (e.g., "send me an estimate"). This ADR does not require correcting customer speech; UI copy and training material may include a parenthetical such as "Quotation (sometimes called an estimate)" for clarity. However, the system's own vocabulary — field labels, API resources, reports, DocType names — uses "Quotation" exclusively, so that "estimate" never becomes an ambiguous second name for two different artifacts (the document vs. the internal Cost Estimate calculation).

## Consequences

- This ADR resolves `docs/standards/Naming_Registry.md` Naming Decision Matrix item #7 (Quotation vs. Quote vs. Estimate) and the related Synonym Registry entry (Section 25).
- No currently-Published Blueprint document requires content changes, since they already use "Quotation."
- Future UI copy may acknowledge "estimate" as a colloquial customer-facing synonym without promoting it to a system-vocabulary term.
- `docs/decisions/ADR-012-Estimating-Terminology.md`'s module-naming decision (Estimation module producing a Quotation artifact) is fully consistent with this ADR's decision and requires no reconciliation.

## Alternatives Rejected

- "Quote" as canonical — rejected per Options Considered, item 2.
- "Estimate" as canonical — rejected per Options Considered, item 3 (ambiguity with Cost Estimate).
- "Proposal" as canonical — rejected per Options Considered, item 4.
- Maintaining "Estimate" as an equally-valid system-facing synonym alongside "Quotation" — rejected: violates the no-synonyms-in-active-use principle and risks the system using two different labels for the same document depending on screen or report.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #7 and the related Section 25 Synonym Registry entry.
2. In a subsequent documentation task, `docs/standards/Naming_Registry.md` should move "Quote," "Estimate" (as a Quotation-document synonym only), and "Proposal" into the Deprecated Names table (Section 28) with a reference to this ADR, taking care to note that "Estimate"/"Cost Estimate" survives as a distinct, non-deprecated internal concept.
3. This ADR does not perform that update itself, consistent with this task's instruction to create ADRs only.

## Related Documents

- `docs/blueprint/05_Domain_Model.md` (Quotation entity)
- `docs/blueprint/06_Bounded_Contexts.md` (Cost Estimate as a distinct Owned Business Object)
- `docs/blueprint/09_PrintOS_Modules.md` (Quotation module content)
- `docs/standards/Naming_Registry.md` (Sections 6, 15, 25, 27 item #7)

## Related ADRs

- [ADR-012-Estimating-Terminology.md](ADR-012-Estimating-Terminology.md) (resolves the process/module naming that produces this artifact)

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
- [x] Decision is unambiguous, including print-industry-specific handling
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Blueprint/Registry edits)
- [x] Related Documents and Related ADRs cross-referenced
- [ ] Reviewed by Project Owner
