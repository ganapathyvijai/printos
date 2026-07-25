# ADR-012: Estimating Terminology

Status:
Accepted

Date:
2026-07-22

---

## Context

`docs/blueprint/06_Bounded_Contexts.md` is internally inconsistent about this bounded context's name: its Mermaid diagram and prose use **Estimation** (singular — "Sales --> Estimation", "Estimation output"), while its own section header reads "### Estimations" (plural). `docs/blueprint/09_PrintOS_Modules.md` lists the corresponding module not as "Estimation" or "Estimations" but as **Quotation**, with the context column reading "Estimations." `docs/standards/Naming_Registry.md` Section 14 already lists an Approved service name, **EstimateService**, and Section 6 lists the role **Estimator**. A separate task requested resolving "Estimating" as a fifth candidate form. This ADR resolves the activity/process-level naming; the output-document naming (Quotation vs. Quote vs. Estimate vs. Proposal) is resolved separately in [ADR-013-Quotation-Terminology.md](ADR-013-Quotation-Terminology.md).

## Problem Statement

Which single form — Estimating, Estimations, Estimation, Estimator, or Estimate Engine — is canonical for referring to the pricing/estimation activity and its bounded context, and how does the Module name relate to it?

## Decision Drivers

- Naming Principle: single meaning per term, no synonyms in active use (`Naming_Registry.md` Section 2).
- Consistency with the naming pattern already used by every sibling bounded context, all of which are singular nouns: CRM, Sales, Production, Inventory, Procurement, Warehouse, Dispatch, Accounts, GST, HR, Administration, Reporting.
- Minimizing disruption to already-Approved terms that are not actually in conflict (EstimateService, Estimator) — these are retained as-is rather than renamed to match the context noun literally.
- Business-first naming (Section 2): "Estimating" (gerund) reads as an activity description rather than a named business area, which is inconsistent with how every other context is named.

## Options Considered

1. **"Estimation" (singular)**, matching the singular-noun pattern of every sibling context. Requires correcting `06_Bounded_Contexts.md`'s section header (currently "Estimations") in a future editing pass, and aligning `09_PrintOS_Modules.md`'s context column.
2. **"Estimations" (plural)**, matching the existing section header in `06_Bounded_Contexts.md`. Rejected: the plural form is inconsistent with every sibling context name, and is contradicted by the same document's own diagram/prose, which already uses the singular "Estimation."
3. **"Estimating" (gerund)**, as requested in this task. Rejected: describes an activity, not a named business area; inconsistent with the noun-based convention used everywhere else in the Bounded Context Registry (`Naming_Registry.md` Section 12).
4. **"Estimate Engine"**, as requested in this task. Rejected as a context/module name: "Engine" implies a specific technical implementation pattern, which is a Technical Vocabulary concern (`Naming_Registry.md` Section 7), not a Business Vocabulary or Bounded Context name. Retained instead as an acceptable informal description of the underlying pricing logic, not a new approved term (see Consequences).

## Decision

**Estimation** (singular) is the canonical name for the bounded context and, going forward, the module:

| Layer | Approved Term |
|---|---|
| Bounded Context name | Estimation Context (supersedes "Estimations Context") |
| Module name | Estimation (supersedes "Quotation" as the module name — see note below) |
| Role | Estimator (unchanged, already Approved) |
| Service name | EstimateService (unchanged, already Approved — service names follow verb-root + "Service" per `Naming_Registry.md` Section 14, independent of the context noun) |
| Output document | Quotation — resolved separately in [ADR-013](ADR-013-Quotation-Terminology.md), not renamed by this ADR |

**Note on the module name:** `09_PrintOS_Modules.md` currently names this module "Quotation," after its primary output artifact, while every other module in that document is named after its bounded context (CRM/CRM, Sales/Sales, Inventory/Inventory, Accounts/Accounts). This ADR resolves the naming pattern by designating **Estimation** as the module name, consistent with its sibling modules; "Quotation" remains the name of the artifact the module produces (see ADR-013), not the module itself.

**Pluralization rule:** Bounded context and module names are singular nouns, without exception, across the entire Bounded Context Registry (`Naming_Registry.md` Section 12) and Module Registry (Section 11). "Estimating," "Estimations," and any other gerund or plural form are not used for context/module naming.

**Module naming rule:** A module's name matches its bounded context's name exactly (both "Estimation"), per the pattern already followed by CRM, Sales, Inventory, Accounts, HR, Administration, and Dispatch.

**Service naming rule:** Service names are not required to match the context noun; they use a verb-root or concept-root plus "Service" (e.g., EstimateService, PricingService), per `Naming_Registry.md` Section 14. No change to existing Approved service names.

## Consequences

- This ADR resolves `docs/standards/Naming_Registry.md` Naming Decision Matrix item #2 (Estimations vs. Estimating).
- `docs/blueprint/06_Bounded_Contexts.md`'s section header ("### Estimations") and `docs/blueprint/09_PrintOS_Modules.md`'s module name ("Quotation") both require correction to align with this decision — performed in a future documentation task, not by this ADR (see Migration Strategy).
- "Estimate Engine" is not adopted as an approved term at any layer; if a future technical design names an actual computational component, that naming decision belongs to Technical Vocabulary (`Naming_Registry.md` Section 7) and requires its own registration, not reuse of this ADR.
- No change to EstimateService or Estimator, which were already correctly named independent of the context-noun conflict.

## Alternatives Rejected

- "Estimations" (plural) — rejected per Options Considered, item 2.
- "Estimating" (gerund) — rejected per Options Considered, item 3.
- "Estimate Engine" as context/module name — rejected per Options Considered, item 4.
- Keeping "Quotation" as the module name permanently (i.e., not correcting the module-naming-pattern mismatch) — rejected, since it leaves Estimation as the only bounded context whose module is named after its output artifact rather than itself, an inconsistency worth resolving now rather than compounding later.

## Migration Strategy

1. This ADR is accepted as the authority resolving Naming Decision Matrix item #2.
2. In a subsequent documentation task: update `docs/blueprint/06_Bounded_Contexts.md`'s "### Estimations" header to "### Estimation," and `docs/blueprint/09_PrintOS_Modules.md`'s module name from "Quotation" to "Estimation" (retaining "Quotation" as the artifact/output name within that module's description, per ADR-013). Update `docs/standards/Naming_Registry.md` Section 11/12 accordingly and close Matrix item #2.
3. This ADR does not perform those edits itself, consistent with this task's instruction to create ADRs only.

## Related Documents

- `docs/blueprint/06_Bounded_Contexts.md` (Estimations/Estimation context)
- `docs/blueprint/09_PrintOS_Modules.md` (Quotation module)
- `docs/standards/Naming_Registry.md` (Sections 6, 11, 12, 14, 27 item #2)

## Related ADRs

- [ADR-013-Quotation-Terminology.md](ADR-013-Quotation-Terminology.md) (resolves the output-artifact naming this ADR intentionally defers)
- [ADR-004-Domain-Driven-Design.md](ADR-004-Domain-Driven-Design.md) (bounded context naming discipline)
- [ADR-005-Module-Boundaries.md](ADR-005-Module-Boundaries.md) (module-to-context alignment principle)

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
- [x] Decision is unambiguous, including pluralization and module-naming rules
- [x] Consequences stated
- [x] Migration Strategy does not exceed this task's scope (no direct Blueprint/Registry edits)
- [x] Related Documents and Related ADRs cross-referenced
- [ ] Reviewed by Project Owner
