# PrintHub Artwork Authority DocType Specification

## 1. Document Control

Version:
1.1

Status:
Draft

Date:
2026-08-22

Owner:
PrintHub Architecture Team

Target Lifecycle:
Published

---

## 2. Specification Authority and Coding Boundary

This document is intended to become a **direct coding specification only after Publication.** While Draft:

- it is **not safe for coding**;
- **direct coding reliance is prohibited**;
- **no implementation authorization exists**;
- the unresolved pre-Publication gates listed in Section 22 must remain visible and must not be silently treated as closed.

**Governance status:** formal Architecture Review and Business Review conducted **2026-07-31**; second narrow targeted re-review conducted **2026-07-31**. **Architecture Review: Accepted with non-blocking corrections. Business Review: Accepted with non-blocking corrections.** Original blocking findings **ART-ARCH-F1** and **ART-BIZ-F1**: Closed. **ART-TRR-BIZ-F1 and ART-TRR-ARCH-F1: Closed by second targeted re-review.** **No blocking review finding remains.** Residual non-blocking findings ART-TRR2-ARCH-F1, ART-TRR2-ARCH-F2 and ART-TRR2-BIZ-F1 are corrected in this revision. Project Owner document lifecycle approval **Not Granted**; Publication **Not Granted**; Implementation Authorization **Not Granted**. This Accepted-with-non-blocking-corrections disposition is a **review outcome**, not document lifecycle promotion, not Publication, and not implementation authorization.

**The Artwork production gate remains open** until the required runtime validations (Section 21) close.

**First targeted re-review:** completed read-only **2026-07-31**. Original Architecture findings ART-ARCH-F1 through ART-ARCH-F11: Closed. Original Business finding ART-BIZ-F1: Partially Closed. **Second narrow targeted re-review:** completed read-only **2026-07-31** — Architecture Review and Business Review both **Accepted with non-blocking corrections**; ART-TRR-BIZ-F1 and ART-TRR-ARCH-F1 **Closed**. The Project Owner approved the Customer Approval Evidence uniqueness rule on **2026-07-31** (Section 7.1b); ART-TRR2-ARCH-F1, ART-TRR2-ARCH-F2 and ART-TRR2-BIZ-F1 were **corrected in the prior revision**. **Post-correction verification:** completed read-only **2026-07-31** — disposition **Post-correction verification: Passed with non-blocking observations**. Verified: Customer Approval Evidence uniqueness; the `approved_revision_key` permanent uniqueness claim; uniqueness retention after revocation; the database-constraint and migration contract. Identified **ART-PCV-F1** (System Design), **ART-PCV-F2** (this document's stale governing-reference version/status, Section 3 — corrected in the prior revision), **ART-PCV-F3** (Documentation Status). **Final cleanup verification:** completed read-only **2026-07-31** — disposition **Final cleanup verification: Passed**.

**Governing Artwork System Design lifecycle status:** the Project Owner granted Document Lifecycle Approval to `../blueprint/18_Artwork_Management.md` on **2026-07-31** at **Approval, Version 1.0**. Following completed Architecture Review, Business Review, Documentation Governance, and independent verification of its Section 7.2 addition (all six local findings Closed, no material finding remaining), the Project Owner granted a further, independent Document Lifecycle Approval on **2026-08-19**, promoting that document to **Approval, Version 1.1**, which then received a bounded reference-only correction on **2026-08-22** (correcting an `AR-003` scope misattribution, changing no normative content) to become **Approval, Version 1.2**, and this same day's bounded naming-status synchronization to become **Approval, Version 1.3** — its **current approved baseline**, with **Approval, Version 1.2, Approval, Version 1.1, and, before them, Approval, Version 1.0 preserved as prior historical baselines**. This is a **bounded governing-reference synchronization only**: it is **not** Publication of this DocType Specification, **not** runtime-validation authorization, and **not** implementation authorization. This specification's own **Project Owner lifecycle approval remains Not Granted**; Publication **Not Granted**; direct coding reliance **prohibited while Draft**; Implementation Authorization **Not Granted**.

---

## 3. Governing Decisions and Source Documents

- [../blueprint/18_Artwork_Management.md](../blueprint/18_Artwork_Management.md) — currently **Approval, Version 1.3** (Project Owner Document Lifecycle Approval **Granted 2026-08-19** at Version 1.1, following completed Architecture Review — Accepted with non-blocking corrections — and Business Review — Accepted with non-blocking corrections — of the Version 1.1 package, with all six local Artwork findings Closed; carried forward through a bounded reference-only correction and a bounded naming-status synchronization, both on **2026-08-22**); prior historical baselines **Approval, Version 1.2**, **Approval, Version 1.1**, and **Approval, Version 1.0** (Granted 2026-07-31). This is the governing architecture/design authority for this specification. This Approval is **not** Publication of this DocType Specification, **not** runtime-validation authorization, and **not** implementation authorization.
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) — domain entities and cardinality.
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) — Artwork context ownership.
- [Business_Entity_Inventory.md](Business_Entity_Inventory.md) — entity inventory.
- [ERPNext_DocType_Mapping.md](ERPNext_DocType_Mapping.md) — implementation ownership mapping.
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — module dependency direction.
- [../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) — Accepted; Tenant/Company distinction.
- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — the consuming Production design; currently **Approval, Version 1.7** (Project Owner Document Lifecycle Approval granted 2026-08-19 at Version 1.5; carried forward through its own bounded reference-only correction and naming-status synchronization, both on 2026-08-22), prior historical baselines **Approval, Version 1.6**, **Approval, Version 1.5**, and **Approval, Version 1.4**. Its Version 1.5 content records the `production_artwork_set` reference contract, now approved.

**Project Owner design defaults approved 2026-07-31.** That approval is an approved design input only — it is not Architecture Review, Business Review, document lifecycle Approval, Publication, or implementation authorization.

---

## 4. Naming Boundary

All five technical DocType names below are **Naming Registry Proposed** (per Project Owner decision dated 2026-08-22; `../standards/Naming_Registry.md` Section 13a) — **none is Approved; Under Review has not begun; source-based collision verification remains outstanding and unauthorized**:

| Business term (approved) | Technical name — Naming Registry status: Proposed |
|---|---|
| Artwork | `PrintHub Artwork` |
| Artwork Revision | `PrintHub Artwork Revision` |
| Customer Approval Evidence | `PrintHub Customer Approval Evidence` — first documented provisional technical identity for this existing, already-governed entity (Section 5, Section 7.1); the entity and its business rules are unchanged |
| Production Artwork Set | `PrintHub Production Artwork Set` |
| — (set membership row) | `PrintHub Production Artwork Set Item` |

No claim is made that these names are Approved by the Naming Registry. This specification does not modify the Naming Registry beyond citing its Section 13a Proposed entries, and `AR-003` is neither resolved nor modified.

---

## 5. Aggregate Boundaries

| Concept | Structure | Role |
|---|---|---|
| Artwork | Aggregate root, standard DocType | Company- and Sales-Order-scoped artwork identity with production-requirement classification |
| Artwork Revision | **Standalone** aggregate, standard DocType — **not a child table** | The independently approvable unit carrying authoritative file evidence |
| Customer Approval Evidence *(technical name `PrintHub Customer Approval Evidence` — Naming Registry status: Proposed, not Approved)* | **Standalone**, Artwork-internal aggregate, standard DocType | The durable, mandatory record of customer approval for one exact Artwork Revision (Section 7.1) |
| Production Artwork Set | Aggregate root, standard DocType | **Final production-release authority consumed by the Job Card** |
| Production Artwork Set Item | **Child table** of Production Artwork Set | Immutable membership value binding one Artwork to one exact Artwork Revision |

**Artwork Revision is standalone** because it is independently approvable, requires its own state, requires independent permissions, must be lockable, requires file-integrity evidence, may be referenced by set membership, may require unique database constraints, and must preserve immutable approval evidence.

**Production Artwork Set Item is the only child table introduced.** It is acceptable because its rows are immutable aggregate membership values — **it is not the approval authority itself.**

**The Job Card eventually stores one Production Artwork Set Link** (Section 15). It does not acquire an Artwork child table.

**File is storage only and is not approval authority.**

---

## 6. Proposed DocType — Artwork

Technical name `PrintHub Artwork` — Naming Registry status: Proposed, not Approved. App-owned standard DocType in `printos_core`; **per the Project Owner's 2026-09-09 module-path decision (`../standards/Naming_Registry.md` Section 13h; `ART-RVR-B02`, resolved and moved to `ART-RVR-A12`), conceptual module: `Artwork`, normalized DocType name `printhub_artwork` — a design/governance resolution only, not yet implemented (Section 22)**.

| Field | Type | Required | Unique | Hidden | Read-only to clients | Importable | Notes |
|---|---|---|---|---|---|---|---|
| `company` | Link → Company | Yes | No | No | Yes (server-derived) | No | Derived from the Sales Order; **immutable after creation**; must equal Sales Order Company |
| `sales_order` | Link → Sales Order | Yes | No | No | Yes after creation | No | **Submitted Sales Orders only**; immutable after creation |
| `title` | Data | No | No | No | No (Draft-editable) | No | Operator-readable description |
| `required_for_production` | Check | Yes (default 1) | No | **Internal** | **Yes — system-managed after creation** | **No** | **Authoritative indicator used when calculating set completeness. Not directly client-writable; not importable; not bulk-editable; changes only through the controlled requirement operation (Section 6a).** |

**Rules.** Company must equal the Sales Order Company. Cross-Company reuse is prohibited. Tenant identity remains implicit at the site/database level; **no Tenant field**. A required Artwork must be represented in an approved Production Artwork Set before Job Card release. Optional Artwork does not block release unless later marked required through the controlled requirement operation (Section 6a). Changes to production requirements after an approved set exists require a **new** Production Artwork Set.

### 6a. Controlled Requirement-Change Operation (corrected 2026-07-31 — ART-ARCH-F1)

`required_for_production` is **not** directly editable through ordinary form save, REST field mutation, import, bulk edit, background field assignment, Administrator field mutation, or `ignore_permissions=True`. It changes only through one controlled Artwork-domain operation, conceptually equivalent to `change_artwork_production_requirement(artwork, required, reason)` (exact technical name not prescribed).

**Specification:**

1. Authenticate; reject Guest.
2. Require a dedicated Artwork production-requirement-management capability, plus applicable Role Permissions and Company User Permissions.
3. Validate a **mandatory reason** under the bounded reason-field rule (Section 7.2): reject empty/whitespace-only/leading-or-trailing-whitespace; allow internal newline/carriage-return/tab; reject other C0 control characters; maximum 500 Unicode code points; never silently truncate; plain text only.
4. Acquire a **current locking read on the Artwork row**.
5. Acquire a **locking read on the currently Approved Production Artwork Set for the same Sales Order, where one exists.**
6. Use the locked values as authoritative for the decision.
7. Update the requirement classification.
8. **Atomically move that currently Approved Set to Withdrawn** (not Superseded — no replacement Set has yet been approved), recording withdrawal actor, timestamp and reason on the Set.
9. Persist **durable audit evidence**: Artwork; prior value; new value; actor; timestamp; reason. **The exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated** (Section 21.3).
10. Commit once.
11. Any failure rolls back all changes together.

**Lock order: (1) Artwork; (2) currently Approved Production Artwork Set.**

**Consequences:** the withdrawn Set cannot be used for a new Job Card release; Released and In Progress Job Cards already bound to it follow the governed withdrawal progression guards (Section 17); Completed Job Cards remain historical; a replacement Set must be created and approved before new release; no Job Card may be silently rebound.

A **Draft** Set is not production authority and must recompute completeness before submission. A **Submitted** Set whose completeness no longer matches the current required-Artwork set **must not be approved** — it must be rejected or replaced through the governed Set lifecycle.

The previously undefined phrase "governed out of scope" is **removed**. For Tier A, `required_for_production` is the **sole** authoritative requirement-classification mechanism.

---

## 7. Proposed DocType — Artwork Revision (Standalone)

Technical name `PrintHub Artwork Revision` — Naming Registry status: Proposed, not Approved. **Standalone standard DocType; explicitly not a child table.**

| Field | Type | Required | Unique | Hidden/internal | Read-only to clients | Importable | Notes |
|---|---|---|---|---|---|---|---|
| `artwork` | Link → `PrintHub Artwork` (Naming Registry status: Proposed, not Approved) | Yes | No | No | Yes after creation | No | Immutable |
| `company` | Link → Company | Yes | No | No | Yes (server-derived) | No | Derived from Artwork; immutable; **retained for native Company User Permission scoping** |
| `revision_label` | Data | Yes | **Within one Artwork** | No | Yes (system-assigned) | No | Human-readable sequence such as R1/R2; **not the authoritative record identity** |
| `state` | Select | Yes | No | No | **Yes — system-managed** | No | Section 9 state model |
| `artwork_file` | Attach (or equivalent governed private-file reference) | Yes at submission | No | No | Draft-editable only | No | **Exactly one authoritative production file for Tier A**; must be private |
| `file_sha256` | Data | Yes at submission | No | Internal | **Yes — system-managed** | No | Exact SHA-256 digest; **immutable from submission onward** |
| `approved_key` | Data-compatible, **nullable** | No | **Yes (UNIQUE)** | **Internal** | **Yes — system-managed** | No | Holds the Artwork identity **only** while Approved for Production; prevents more than one Approved revision per Artwork |
| `approved_by` | Link → User | On approval | No | No | **Yes — immutable approval evidence** | No | |
| `approved_on` | Datetime | On approval | No | No | **Yes — immutable approval evidence** | No | |
| `rejected_reason` | Small Text (bounded plain text, Section 7.2) | On rejection | No | No | Yes | No | |
| `withdrawn_by` | Link → User | On withdrawal | No | No | Yes | No | |
| `withdrawn_on` | Datetime | On withdrawal | No | No | Yes | No | |
| `withdrawal_reason` | Small Text (bounded plain text, Section 7.2) | **Mandatory for withdrawal** | No | No | Yes | No | |
| `superseded_by` | Link → `PrintHub Artwork Revision` (Naming Registry status: Proposed, not Approved) | On supersession | No | No | Yes | No | Records the replacement revision |

`modified`, `modified_by` and Version history **must not** be relied upon as the authoritative approval event, because later supersession or withdrawal changes the record. Version history is **supplemental only**.

### 7.1 Customer Approval Evidence Contract — Artwork-Internal (corrected 2026-07-31 — ART-BIZ-F1)

**Customer approval for the exact Artwork Revision is mandatory before internal production approval. There is no Tier A waiver.** Referred to normatively as **`Customer Approval Evidence`**; its provisional technical name is `PrintHub Customer Approval Evidence` — **Naming Registry status: Proposed only, not Approved** (per Project Owner decision dated 2026-08-22; `../standards/Naming_Registry.md` Section 13a; not governed by `AR-003`, whose own recorded scope is unrelated). Under Review has not begun and source-based collision verification remains unauthorized and outstanding; this document does not resolve or establish the name's final status.

**Minimum conceptual fields:**

| Field | Type | Notes |
|---|---|---|
| `artwork_revision` | Link → `PrintHub Artwork Revision` (Naming Registry status: Proposed, not Approved) | Required; immutable |
| `company` | Link → Company | Required; immutable; must match the Revision and Artwork |
| `sales_order` | Link → Sales Order | Required; immutable; must match the Revision's Artwork |
| `outcome` | Select (system-managed) | Approved or Rejected |
| `customer_identity` | Governed customer or representative identity reference | Required |
| `decided_on` | Datetime | Required |
| `evidence_reference` | Data (immutable evidence-source reference) | Required |
| `revoked` | Check (system-managed) | Revocation is supported in Tier A (Section 7.1a); set only by the controlled revocation operation |
| `revoked_by` | Link → User | Written once by the controlled revocation operation; immutable |
| `revoked_on` | Datetime | Written once by the controlled revocation operation; immutable |
| `revocation_reason` | Small Text (bounded plain text, Section 7.2) | Mandatory for revocation; immutable once written |
| `approved_revision_key` | Data-compatible, **conditionally nullable** | **Yes (UNIQUE, single-column)** — proposed internal field name, not final; equals the exact validated Artwork Revision identity only where `outcome = Approved`; NULL where `outcome = Rejected`; **retained (never cleared) after revocation** (Section 7.1b); system-managed; server-derived only; not client-writable, form-editable, REST-writable, importable or bulk-editable; immutable after persistence; no normalization, trimming or case conversion; Company/Sales Order/Tenant deliberately excluded from the key |

This record is **Artwork-internal**. `Proof` is the customer-facing review artifact; **`Proof` is not approval authority**, and an Approved outcome **cannot be inferred from Proof status alone, nor from a file merely existing**. **Ordinary REST writes cannot fabricate approval.** The record is immutable after recording except through a governed revocation or correction process.

The controlled Artwork Revision approval operation (Submitted for Approval → Approved for Production) **must verify current valid Customer Approval Evidence for the exact locked Revision** before permitting the transition — see the updated transition requirement in Section 9. **Customer Approval Evidence is not added to the Job Card aggregate** (Section 15).

### 7.1a Customer Approval Evidence Revocation — Controlled Operation and Atomic Propagation (corrected 2026-07-31 — ART-TRR-BIZ-F1; Project Owner decision approved 2026-07-31)

**Customer Approval Evidence revocation is supported in Tier A.** An Approved Customer Approval Evidence record may be revoked **only** through one controlled Artwork-domain operation, conceptually equivalent to `revoke_customer_approval(evidence, reason)` (exact technical name not prescribed). Revocation preserves the original approval payload (`artwork_revision`, `company`, `sales_order`, `outcome`, `customer_identity`, `decided_on`, `evidence_reference`) unchanged and **cannot be reversed in place**. A later customer reapproval requires new Customer Approval Evidence, a new Artwork Revision, renewed internal production approval and a new Production Artwork Set.

**Specification:**

1. Authenticate; reject Guest.
2. Require a dedicated Customer Approval Evidence revocation capability, plus applicable Role Permissions and Company User Permissions.
3. Validate a **mandatory reason** under the bounded reason-field rule (Section 7.2).
4. Only an **Approved** Customer Approval Evidence record may be revoked; a Rejected record is terminal historical evidence and is never revoked (`CustomerApprovalNotApproved` on violation); an already-revoked record is rejected (`CustomerApprovalAlreadyRevoked`).
5. Acquire current locking reads in the deterministic order: (a) Sales Order; (b) every currently Approved Production Artwork Set containing the Revision, sorted by record identity; (c) Artwork Revision; (d) Customer Approval Evidence.
6. Decide only from the locked values.
7. Mark the evidence revoked; write `revoked_by`, `revoked_on`, `revocation_reason`.
8. Where the Revision is **Approved for Production**: move it to Withdrawn; clear its nullable `approved_key`; write `withdrawn_by`/`withdrawn_on`/`withdrawal_reason`; identify and move **every** currently Approved Set containing that Revision to Withdrawn; clear each Set's nullable `approved_key`; write each Set's withdrawal evidence — **no silent winner selection where more than one inconsistent Approved Set is found; process or fail visibly on every one.**
9. Where the Revision is **Submitted for Approval**: the Revision itself remains Submitted for historical traceability but becomes permanently ineligible for internal production approval; no Set propagation occurs (none can legally exist yet).
10. Where the Revision is **Draft**: fail visibly (`CustomerApprovalRevisionMismatch` or equivalent) rather than silently treating the Draft Revision as approved — this represents inconsistent data.
11. Where the Revision is **Superseded** or **Withdrawn**: the revocation is recorded as an auditable change to the evidence only; the Revision's terminal state is **not** reopened or reversed; any currently Approved Set still containing that terminal Revision represents inconsistent data and must be withdrawn or must cause visible failure through this same operation.
12. Commit once.
13. Any failure rolls back the evidence revocation, Revision state, Revision `approved_key`, Revision withdrawal evidence, and every affected Set's state, `approved_key` and withdrawal evidence together.

**Invariant on internal approval:** the controlled Revision-approval operation (Section 9.1) **must reject** a Submitted Revision whose Customer Approval Evidence has been revoked.

**Direct-bypass guards:** ordinary save, REST resource mutation, standard document method endpoints, import, bulk edit, background field assignment, Administrator field mutation and `ignore_permissions=True` must not fabricate or partially execute revocation, or directly assign `revoked`, `revoked_by`, `revoked_on`, `revocation_reason`, Revision Withdrawn state, Revision `approved_key`, Set Withdrawn state, Set `approved_key`, or withdrawal evidence.

**Locking baseline is design-level only.** Section 6a's Artwork-then-Set order, Section 16's Job-Card-then-Set order, and this operation's Sales-Order/Set/Revision/Evidence order must be reconciled into **one non-contradictory global lock-order policy before Publication**; none of this is claimed runtime validated (Section 21.3).

**Stable domain errors:** `CustomerApprovalAlreadyRevoked`; `CustomerApprovalNotApproved`; `CustomerApprovalRevisionMismatch`; `CustomerApprovalCompanyMismatch`; `CustomerApprovalSalesOrderMismatch`; `CustomerApprovalRevocationReasonInvalid`; `ArtworkRevisionConcurrentModification`; `ProductionArtworkSetConcurrentModification`; `CustomerApprovalRevocationPropagationFailed`; `CustomerApprovalEvidenceNotAccessible`. Domain rejection and uniqueness conflicts are never auto-retried; lock-wait/deadlock retry remains gated on the global lock-order policy above.

A Production Artwork Set withdrawn through this operation follows exactly the **same** consequences as a Set withdrawn by direct governed withdrawal, requirement-change (Section 6a), or member-Revision withdrawal (Section 17): it cannot support new release, cannot be silently returned to Approved, and cannot be modified in place — a new Set must be constructed and approved. A Job Card bound to such a Set follows the existing withdrawal progression guards (Section 17) with no distinct procedure introduced for the revocation cause.

### 7.1b Customer Approval Evidence Uniqueness (corrected 2026-07-31 — ART-TRR2-BIZ-F1; Project Owner decision approved 2026-07-31)

**Each Artwork Revision may have at most one Customer Approval Evidence record whose immutable outcome is Approved.** This is a **permanent historical uniqueness claim**, distinct from the current-authority "active key" pattern (Revision `approved_key`, Set `approved_key`, both cleared on withdrawal/supersession): `approved_revision_key` (Section 7.1) is **never cleared by revocation**. A second Approved record must never be created for the same Revision, even after the first was revoked. Reapproval against the same Revision is prohibited; it requires a new Artwork Revision, new Customer Approval Evidence, renewed internal approval and a new Production Artwork Set (Section 7.1a). Duplicate historical Approved evidence is invalid data.

**Invariants:** (1) Approved: `approved_revision_key == artwork_revision`; (2) Rejected: `approved_revision_key IS NULL`; (3) revoked Approved evidence retains outcome Approved and `approved_revision_key`, sets `revoked = true`, and no longer satisfies the internal production-approval predicate; (4) `approved_revision_key` immutable after persistence; (5) revocation must not clear it; (6) no Approved → Rejected conversion; (7) no Rejected → Approved in-place conversion; (8) no reassignment to a different Revision; (9) no second Approved record for a Revision, ever; (10) the database UNIQUE constraint is independently authoritative; (11) an existence query alone is not sufficient concurrency control; (12) concurrent Approved-evidence creation attempts for one Revision commit at most one record.

**Recording operation** (conceptually `record_customer_approval(artwork_revision, outcome, evidence_payload)`, exact name not prescribed). For Approved: authenticate; reject Guest; require the governed customer-approval-recording capability, applicable Role and Company User Permissions; acquire a current locking read on the Artwork Revision; validate Company/Sales Order context and Revision eligibility; derive `approved_revision_key` **server-side only**; insert transactionally; rely on the **database UNIQUE constraint** for final race-safe enforcement; commit once; roll back fully on failure. For Rejected: evidence remains historical; `approved_revision_key` is NULL; the record can never later become Approved. No ordinary save or REST field update may fabricate an Approved outcome or its key.

**Updated internal production-approval predicate.** Before Submitted for Approval → Approved for Production, the controlled approval operation must establish: exactly one Customer Approval Evidence record for the exact locked Revision with outcome Approved and `approved_revision_key == artwork_revision`; that record is not revoked; Company and Sales Order match; the Artwork Revision Link is the exact locked Revision; the evidence is structurally valid; and no corrupt duplicate Approved evidence exists. **Zero** Approved evidence → reject. **Exactly one, unrevoked** → continue with the remaining checks in Section 9.1. **Exactly one, revoked** → reject. **More than one** (historical corruption) → **fail visibly, require governed remediation**. The operation must never silently select the newest, oldest, most-recently-modified, an unrevoked record from a duplicate group, or a client-chosen record.

**Database-conflict treatment.** A duplicate-key conflict on `approved_revision_key` is translated into a stable domain conflict — conceptually `CustomerApprovalAlreadyApprovedForRevision` (exact class name not prescribed) — without exposing SQL, table or index names. Uniqueness conflicts are never auto-retried; no different record is silently substituted absent a separately approved future idempotency contract; no request-ID or idempotency field is introduced by this correction.

**Migration and historical safety.** Before creating the `approved_revision_key` UNIQUE constraint: find every Approved evidence record; group by exact Artwork Revision identity; detect every group with more than one Approved record; **fail the migration visibly** on any duplicate group, with diagnostic identity information sufficient for governed remediation without exposing unnecessary customer-sensitive evidence. Migration must never select a winner, auto-revoke duplicates, convert Approved to Rejected, clear outcomes or evidence references, reassign a Revision, fabricate timestamps/actors, populate `approved_revision_key` as unflagged historical proof, or auto-delete duplicates. Only after governed remediation may `approved_revision_key` be populated for every Approved record (NULL retained for Rejected), the UNIQUE constraint created, and absence of duplicates verified. This mirrors, and must remain consistent with, the equivalent System Design migration rule (Section 19).

**Direct-bypass guards.** Ordinary save, REST resource mutation, document method endpoints bypassing the governed operation, import, bulk edit, background direct assignment, Administrator field mutation and `ignore_permissions=True` must not assign or change `outcome`, `artwork_revision` after creation, `company`, `sales_order`, `approved_revision_key`, `revoked`, `revoked_by`, `revoked_on`, or `revocation_reason`. Raw SQL by a database administrator remains outside normal application guarantees.

### 7.2 Bounded Reason-Field Rule

Applies to `rejected_reason`, `withdrawal_reason` (this section and Section 8), and the requirement-change reason (Section 6a): reject `None`, empty, or whitespace-only input; **reject leading or trailing whitespace rather than silently trimming it**; allow internal newline, carriage-return and tab characters; reject other C0 control characters; enforce a maximum of **500 Unicode code points**; **reject over-length input — never silently truncate**; store plain text only; escape on every rendered surface.

---

## 8. Proposed DocType — Production Artwork Set and Set Item

### 8.1 Production Artwork Set

Technical name `PrintHub Production Artwork Set` — Naming Registry status: Proposed, not Approved. **The final production-release authority consumed by the Job Card.**

| Field | Type | Required | Unique | Hidden/internal | Read-only to clients | Importable | Notes |
|---|---|---|---|---|---|---|---|
| `company` | Link → Company | Yes | No | No | Yes (server-derived) | No | Immutable |
| `sales_order` | Link → Sales Order | Yes | No | No | Yes after creation | No | **Submitted only**; immutable |
| `state` | Select | Yes | No | No | **Yes — system-managed** | No | Section 10 state model |
| `approved_key` | Data-compatible, **nullable** | No | **Yes (UNIQUE)** | **Internal** | **Yes — system-managed** | No | Contains the exact Sales Order identity **only** while Approved for Production; permits **at most one** currently Approved set per Sales Order |
| `approved_by` | Link → User | On approval | No | No | **Yes — immutable** | No | |
| `approved_on` | Datetime | On approval | No | No | **Yes — immutable** | No | |
| `withdrawn_by` | Link → User | On withdrawal | No | No | Yes | No | |
| `withdrawn_on` | Datetime | On withdrawal | No | No | Yes | No | |
| `withdrawal_reason` | Small Text (bounded plain text, Section 7.2) | **Mandatory for withdrawal** | No | No | Yes | No | |
| `superseded_by` | Link → `PrintHub Production Artwork Set` (Naming Registry status: Proposed, not Approved) | On supersession | No | No | Yes | No | |

### 8.2 Production Artwork Set Item (Child Table)

Technical name `PrintHub Production Artwork Set Item` — Naming Registry status: Proposed, not Approved.

| Field | Type | Required | Notes |
|---|---|---|---|
| `artwork` | Link → `PrintHub Artwork` (Naming Registry status: Proposed, not Approved) | Yes | |
| `artwork_revision` | Link → `PrintHub Artwork Revision` (Naming Registry status: Proposed, not Approved) | Yes | The exact approved revision |
| `revision_label` | Data | No | **Read-only display value only** — the authoritative value remains on the revision |

**Set-item rules:** one row per required Artwork; no duplicate Artwork membership; every linked Artwork belongs to the same Company and Sales Order; every linked revision belongs to the linked Artwork; every linked revision must be Approved for Production at set approval; membership becomes **immutable when the set is submitted for approval**; membership is **not modified in place after approval**; changed content requires a **new set**.

---

## 9. Artwork Revision State Model

| State | Meaning | Permits use in an approved set | File/hash |
|---|---|---|---|
| Draft | File and metadata may be prepared | No | File may be replaced |
| Submitted for Approval | Frozen for review | No — does not permit Job Card release | File identity and hash immutable |
| **Approved for Production** | **Only state allowed in an approved set** | **Yes** | Immutable; approval actor and timestamp recorded |
| Rejected | Review declined; terminal for that revision | Never | Immutable |
| Superseded | Replaced by a later approved revision | No — historical references remain intact | Immutable |
| Withdrawn | Approval explicitly revoked | No | Immutable |

### 9.1 Artwork Revision Transition Matrix (corrected 2026-07-31 — ART-ARCH-F4, ART-BIZ-F1)

| Current | Allowed transition | Result | Mechanism / Authority |
|---|---|---|---|
| Draft | Submit for Approval | Submitted for Approval | Controlled submit operation; submit-for-approval capability |
| Submitted for Approval | Approve — **requires exact, current, valid Customer Approval Evidence for this locked Revision (Section 7.1)** | Approved for Production | Controlled approve operation; **Approve-revision capability** |
| Submitted for Approval | Reject | Rejected | Controlled reject operation; reject capability |
| Approved for Production | Supersede | Superseded | System consequence of a later approval, same transaction |
| Approved for Production | Withdraw / revoke | Withdrawn | Controlled withdraw operation; mandatory reason; withdraw/revoke capability |
| **Rejected** | **None** | **Terminal** | A Rejected Revision cannot return to Draft, cannot be resubmitted, cannot be approved, and cannot enter a Production Artwork Set. **Corrections require creating a new Artwork Revision.** |
| Superseded | None | Terminal | — |
| Withdrawn | None | Terminal | — |

**The Submitted for Approval → Approved for Production transition additionally requires:** frozen `artwork_file` and `file_sha256`; exact valid, **non-revoked** Customer Approval Evidence for the exact locked Revision (corrected 2026-07-31 — ART-TRR-BIZ-F1: a Submitted Revision whose Customer Approval Evidence has been revoked is permanently rejected from this transition, Section 7.1a); the internal production-approval capability; current locking reads; execution within one transaction; and immutable `approved_by`/`approved_on` written exactly once.

**No Expired state exists. Tier A approvals do not expire.** Withdrawing a revision requires any currently Approved set containing it to be withdrawn or invalidated **in the same governed transaction**.

---

## 10. Production Artwork Set State Model

### 10.1 Production Artwork Set Transition Matrix (corrected 2026-07-31 — ART-ARCH-F6)

| Current | Allowed transition | Result |
|---|---|---|
| Draft | Submit for Approval | Submitted for Approval |
| Submitted for Approval | Approve | Approved for Production |
| Submitted for Approval | Reject | Rejected |
| Approved for Production | Supersede through **replacement Set approval** **or member-Revision supersession (Section 10.2)** | Superseded |
| Approved for Production | Withdraw / revoke | Withdrawn |
| Rejected | None | Terminal |
| Superseded | None | Terminal |
| Withdrawn | None | Terminal |

**Only Approved for Production permits a new Registered → Released Job Card transition.**

Rules: at most one set may be Approved for Production per Sales Order; approving a replacement set **supersedes the currently Approved set in the same transaction**; **Superseded Sets remain historical and may remain bound to Job Cards that were already Released before supersession, including those that later progress to In Progress or Completed; they cannot authorize a new Job Card release**, and no Job Card is silently rebound; withdrawn sets may not be used for new release; withdrawal requires actor, timestamp and reason; approved membership is immutable; **no silent in-place replacement is permitted**. **A requirement change (Section 6a) may also move an Approved Set to Withdrawn through its dedicated controlled operation**, independent of a normal withdraw/revoke action.

**Governed causes of each terminal authority state (normative summary).** An Approved Set becomes **Superseded** through either (a) approval of a replacement Set for the same Sales Order, or (b) **member-Revision supersession under Section 10.2** — a newer Artwork Revision for a member Artwork is internally approved, superseding a Revision held by this Set. An Approved Set becomes **Withdrawn** through customer-approval revocation affecting a member Revision (Section 7.1a), explicit governed Set withdrawal, withdrawal of an approved member Revision (Section 9.1), or a governed `required_for_production` requirement change (Section 6a). **Neither terminal state permits a new Job Card release**, and neither rewrites historical Set membership or an existing Job Card binding. They differ for **already-bound** Job Cards: a merely **Superseded** Set permits continued Released/In Progress progression (Section 17), whereas a **Withdrawn** Set blocks it under the governed withdrawal guards (Section 17).

### 10.2 Set Consequence of Revision Supersession (Project Owner decision approved 2026-08-08; governed by `../blueprint/18_Artwork_Management.md` Section 7.2)

Where a newer Artwork Revision for the same Artwork is internally approved (Section 9.1), and the previously current Approved Revision it replaces is a member of a currently Approved Production Artwork Set, that Set moves Approved for Production → **Superseded** (not Withdrawn) in the **same governed transaction** as the Revision approval, and its `approved_key` is cleared. The superseded Set is never rewritten in place; a new Set is required for future release; Released or In Progress Job Cards bound to it are unaffected and follow the existing Superseded-not-Withdrawn progression rule (Section 17); no Job Card is silently rebound. This is covered by the existing runtime-validation gates for atomic revision and set supersession (Section 21.3); no new gate is added by this decision.

**Complete propagation — no silent winner selection.** The database constraints normally permit only **one** Approved Production Artwork Set per Sales Order (Section 8.1, Section 13), and this rule does **not** authorize more than one. Where inconsistent data nonetheless presents more than one currently Approved Set containing the previous Revision, the operation must **detect every such Set** and either **supersede every affected Set in the same transaction**, or **fail visibly and roll the entire operation back**, leaving the newer Revision unapproved. It must **never** silently select a winner, process only one match, or leave partial propagation. This mirrors the revocation-propagation guard in Section 7.1a.

---

## 11. Completeness Predicate (Set Approval)

Evaluated at Production Artwork Set approval time:

1. Load the Sales Order and Company.
2. Determine the complete current set of Artwork where `sales_order` equals the set Sales Order, `company` equals the set Company, and `required_for_production` is true. **`required_for_production` is the sole authoritative requirement-classification mechanism; the previously undefined "governed out of scope" phrase is removed.**
3. **Require at least one required Artwork** (`required Artwork count >= 1`) before the Set may be submitted or approved (corrected 2026-07-31 — ART-ARCH-F3). An empty Production Artwork Set is invalid; a Set with zero membership rows cannot be submitted; a Set cannot be approved where the authoritative required-Artwork identity set is empty; Registered → Released cannot succeed with zero required Artworks. **There is no Tier A no-Artwork exception; any future no-Artwork production path requires a separate governed design change.**
4. Compare that authoritative required-Artwork set with the set-item Artwork membership.
5. Require **exact equality** — no missing required Artwork; no duplicate Artwork; no unrelated Artwork; no wrong Sales Order; no wrong Company.
6. Validate every selected revision: belongs to the corresponding Artwork; state is Approved for Production; **valid Customer Approval Evidence exists for this exact Revision** (Section 7.1); authoritative private File exists; stored SHA-256 exists; file identity is immutable; not Withdrawn; not Superseded at the authoritative decision point.
7. Approve only when every condition passes.

**A mere count comparison is insufficient — exact identity-set equality with a minimum required-Artwork cardinality of one is required.**

### 11.1 Production Artwork Set Approval Rationale (ART-BIZ-F4)

Production Artwork Set approval is **not** a second file-content review. Its purpose is to: recompute the current required-Artwork identity set; verify exact completeness; verify valid customer approval for every selected Revision; verify internal production approval for every selected Revision; freeze the selected combination; and create **one atomic, server-verifiable production-release authority**. Content review already occurred at Revision approval (Section 9.1); Set approval freezes *which combination* of already-approved Revisions constitutes one production package.

---

## 12. Mutability Matrices

### 12.1 Artwork Revision

| Field | Draft | Submitted | Approved | Rejected / Superseded / Withdrawn |
|---|---|---|---|---|
| `artwork`, `company` | Immutable | Immutable | Immutable | Immutable |
| `revision_label` | System-assigned | Immutable | Immutable | Immutable |
| `state` | System-managed | System-managed | System-managed | Terminal or system-managed |
| `artwork_file` | Replaceable | **Immutable** | **Immutable** | **Immutable** |
| `file_sha256` | Not yet set | **Immutable from submission** | Immutable | Immutable |
| `approved_key` | NULL | NULL | Artwork identity (system) | NULL |
| `approved_by` / `approved_on` | Empty | Empty | **Written once; immutable** | Preserved |
| `withdrawn_*` | Empty | Empty | Empty | Written once by withdrawal; immutable |
| `superseded_by` | Empty | Empty | Empty | Written once by supersession |

### 12.2 Production Artwork Set

| Field | Draft | Submitted | Approved | Rejected / Superseded / Withdrawn |
|---|---|---|---|---|
| `company`, `sales_order` | Immutable | Immutable | Immutable | Immutable |
| `state` | System-managed | System-managed | System-managed | Terminal or system-managed |
| Membership (child rows) | Editable | **Immutable** | **Immutable** | **Immutable** |
| `approved_key` | NULL | NULL | Sales Order identity (system) | NULL |
| `approved_by` / `approved_on` | Empty | Empty | **Written once; immutable** | Preserved |
| `withdrawn_*` | Empty | Empty | Empty | Written once by withdrawal; immutable |
| `superseded_by` | Empty | Empty | Empty | Written once by supersession |

---

## 13. Database Constraints

- **Artwork Revision `approved_key`** — nullable, single-column **UNIQUE**; holds the Artwork identity only while Approved; enforces **at most one Approved revision per Artwork**.
- **Production Artwork Set `approved_key`** — nullable, single-column **UNIQUE**; holds the Sales Order identity only while Approved; enforces **at most one Approved set per Sales Order**.
- **Customer Approval Evidence `approved_revision_key`** — conditionally nullable, single-column **UNIQUE**; holds the exact Artwork Revision identity only while `outcome = Approved`; **retained after revocation**; enforces **at most one historical Approved Customer Approval Evidence record per Artwork Revision, permanently** (Section 7.1b).
- **`revision_label`** — unique within one Artwork.
- **Set membership** — no duplicate Artwork within one set.
- Storage engine must support transactional row locking and the above constraints. Constraint behaviour is **not claimed validated** — see Section 21.

---

## 14. Controlled Operations

All operations are authenticated, **mutation-only**, capability-checked, take **current locking reads**, decide from **locked values**, perform **no early commit**, commit exactly once, and return stable domain errors with **no stack traces or SQL**. Desk, restricted API and background execution use the **same** server operations.

| Operation | Effect |
|---|---|
| Submit Artwork Revision for approval | Draft → Submitted; computes and stores SHA-256; freezes file identity and hash |
| Record Customer Approval Evidence | Creates Approved or Rejected evidence; for Approved, derives `approved_revision_key` server-side and relies on the database UNIQUE constraint for race-safe enforcement (Section 7.1b) |
| Approve Artwork Revision | Submitted → Approved; **verifies exactly one valid, unrevoked Customer Approval Evidence with `approved_revision_key == artwork_revision` for the exact locked Revision (Section 7.1b)**; rejects on zero, revoked, or corrupt-multiple evidence; **recomputes and compares SHA-256**; sets `approved_key`, `approved_by`, `approved_on`; supersedes the prior Approved revision atomically |
| Change Artwork production requirement | Updates `required_for_production`; **atomically moves the currently Approved Set (if any) to Withdrawn**; records durable audit evidence (Section 6a) |
| Revoke Customer Approval Evidence | Approved → Revoked; **atomically withdraws the associated Approved Revision and every currently Approved Set containing it** where the Revision was Approved for Production; permanently disqualifies a Submitted Revision from internal approval; records immutable revocation evidence (Section 7.1a) |
| Reject Artwork Revision | Submitted → Rejected with reason |
| Withdraw Artwork Revision | Approved → Withdrawn with actor, timestamp and mandatory reason; withdraws or invalidates any containing Approved set in the same transaction |
| Submit Production Artwork Set for approval | Draft → Submitted; freezes membership |
| Approve Production Artwork Set | Submitted → Approved; evaluates the Section 11 completeness predicate; sets `approved_key`, `approved_by`, `approved_on`; supersedes the prior Approved set atomically |
| Reject Production Artwork Set | Submitted → Rejected |
| Withdraw Production Artwork Set | Approved → Withdrawn with actor, timestamp and mandatory reason |
| Release Job Card against an approved set | Section 16 contract |

---

## 15. Job Card Reference Contract

The Job Card Tier A specification will eventually add **one** conceptual field. **That change is not made by this document.**

| Aspect | Value |
|---|---|
| Concept | Production Artwork Set reference |
| Proposed field name | `production_artwork_set` |
| Type | Link → `PrintHub Production Artwork Set` (Naming Registry status: Proposed, not Approved) |
| Schema-required | **No** — Registered records may be created before Artwork approval |
| Required | **Mandatory for successful Registered → Released** |
| Client mutability | **None** |
| Assignment | Server-side controlled Release action only |
| After Release | **Immutable** |
| Importable / bulk-editable | No / No |

The Job Card does **not** acquire an Artwork Revision child table, copied file hashes, copied approval actor, copied approval timestamp, a copied revision collection, Proof references, or Approval Record references. **The approved Production Artwork Set remains the authority.**

---

## 16. Release-Gate Contract (Registered → Released)

1. Authenticate. 2. Reject Guest. 3. Require the governed Job Card release capability, the applicable standard Role Permission and the applicable Company User Permission. 4. Acquire the Job Card row with a current locking read equivalent to `SELECT ... FOR UPDATE`. 5. Use the locking-read values as authoritative. 6. Require `status = Registered`, `docstatus = 0`, a still-valid Submitted Sales Order, and Job Card Company equal to Sales Order Company. 7. Resolve the selected Production Artwork Set. 8. Acquire the set row through a locking read. 9. Use the locked set values as authoritative. 10. Require `state = Approved for Production`; set Company equals Job Card Company; set Sales Order equals Job Card Sales Order; `approved_key` equals the exact Sales Order identity; approved evidence structurally intact; authoritative File records exist; required stored hashes exist. 11. Require governed read access to the set and its approved evidence. 12. Assign the Production Artwork Set reference. 13. Set status to Released through the controlled transition. 14. Commit once. 15. Any failure rolls back all Job Card changes.

**Lock ordering: (1) Job Card, then (2) Production Artwork Set.** Decisions must not be taken from stale plain reads under REPEATABLE-READ. **Full cross-aggregate lock ordering and deadlock treatment remain unvalidated** (Section 21).

**A full file-content rehash is not required at every release by default.** Release validates authoritative File identity, File existence, private status, presence of the stored hash, and that set and revision evidence has not been governed invalid. **Missing evidence, changed identity or a missing hash blocks release.**

---

## 17. Post-Release Supersession and Withdrawal

**Supersession** — the existing binding is **not** invalidated. Released may progress to In Progress, and In Progress may complete, when the bound set is Superseded but not Withdrawn. Completed remains historical. The superseded set **cannot be used for a new Job Card release**.

**Withdrawal** (corrected 2026-07-31 — ART-ARCH-F5: the previously undefined In Progress "governed correction procedure" that implied possible rebinding is **removed**) — Released: **Released → In Progress is blocked**, an operational alert is required, and correction **uses controlled `discard()` with mandatory terminal reason** plus a replacement Job Card against a newly Approved Set. In Progress: **In Progress → Completed is blocked**, an operational alert is required, and correction **uses controlled `discard()` with mandatory terminal reason** plus a replacement Job Card against a newly Approved Set. Completed: the historical production record **remains unchanged**; withdrawal does not rewrite history; **controlled `cancel()` / Void with mandatory reason may be used only where governed business follow-up requires it.**

**Rules:** `production_artwork_set` is **never modified after Release**; there is **no silent rebind**; an In Progress Job Card is **never updated** to a replacement Set; **no Artwork Hold Job Card state is added**; replacement follows the existing one-active-Job-Card-per-Sales-Order and terminal lifecycle rules.

---

## 18. Permission Specification

**Artwork capabilities:** create Artwork draft; edit Artwork draft; **manage production-requirement classification** (the controlled `required_for_production` operation, Section 6a); create Artwork Revision; upload or replace Draft revision file; submit revision for approval; **record Customer Approval Evidence outcome** (Section 7.1); **approve revision for production**; reject revision; create Production Artwork Set; submit set for approval; **approve set for production**; reject set; **withdraw or revoke**; view/download approved Artwork; administer exceptional corrections.

**Production capability:** release Job Card against an approved Production Artwork Set.

**Separation of duties is normative:** Artwork approval authority is **distinct** from Job Card release authority. Holding release authority does **not** grant approval authority; holding approval authority does **not** automatically grant release authority.

**Maker-checker treatment (ART-BIZ-F3):** Revision submission and Revision production approval are **separate capabilities**, but **Tier A does not require the submitter and approver to be different user identities** — no mandatory `approved_by != submitted_by` server invariant is introduced. A Tenant may enforce maker-checker separation through governed role assignment as an operational matter, outside this specification's scope. This is independent of, and does not weaken, the mandatory separation between Artwork approval capability and Job Card release capability.

Standard Role Permissions and Company User Permissions remain the baseline. The **P-2 provisioning invariant applies to Artwork operational roles** — a role-bearing user with **zero Company User Permissions is an invalid provisioned state**. **No `has_permission` hook** and **no `permission_query_conditions` hook** is required by current evidence; either requires a future reproduced gap plus a controlled design change. **Administrator is privileged for permission purposes but cannot bypass production-approval domain invariants.** Tenant isolation remains site/database based; **no Tenant field**.

---

## 19. File and Attachment Rules

The Artwork Revision **owns the authoritative File**, which **must be private**. **Attachment existence alone does not mean approval.** Upload uses standard Frappe attachment handling. Upload and replacement are permitted **only while the revision is Draft**. Submission computes and stores SHA-256; approval **recomputes and compares** it, and a mismatch rejects approval. Approved content **cannot be replaced in place** — changed content requires a **new revision**. Approved or ever-approved evidence **must not be silently deleted**. Thumbnails and previews are **convenience-only**. MIME and extension restrictions require a **server-side allow-list**. **Malware scanning remains a separate platform production-readiness control.** Job Card users receive **read/download only** where authorized, and **no** Artwork upload, replacement, approval, withdrawal or deletion capability.

**File is storage only and is not the approval authority.**

---

## 20. Direct-Bypass Guards

Ordinary REST field mutation and ordinary save **must not** be able to fabricate: approval state; file hash; approved key; approval actor or timestamp; set membership; withdrawal state; **`required_for_production` classification**; **Customer Approval Evidence outcome**; **Customer Approval Evidence revocation state or evidence (Section 7.1a)**; **Customer Approval Evidence `approved_revision_key` or a change of `artwork_revision` after creation (Section 7.1b)**; or Job Card release authority. System-managed fields must be rejected or restored to server-derived values before persistence. The guards apply to ordinary save, REST resource updates, standard document method endpoints, background execution, Administrator, and privileged execution. Database UNIQUE constraints remain independently authoritative. Raw SQL by a database administrator remains outside normal application guarantees.

---

## 21. Audit, Migration and Required Runtime Validation

### 21.1 Audit Fields

**Artwork Revision:** `approved_by`; `approved_on`; `file_sha256`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`. **Customer Approval Evidence:** `outcome`; `customer_identity`; `decided_on`; `evidence_reference`; `approved_revision_key` (Section 7.1b); `revoked`; `revoked_by`; `revoked_on`; `revocation_reason` where present (Section 7.1a). **Production Artwork Set:** `approved_by`; `approved_on`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`. **Requirement-change events** (Section 6a): Artwork; prior value; new value; actor; timestamp; reason — **the exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated.**

Record identity carries immutable revision/set identity; explicit fields carry approval and withdrawal events; `modified` and `modified_by` describe **current record metadata only**; Version history is **supplemental when enabled** and **must not be assumed guaranteed authoritative audit storage**.

### 21.2 Migration Requirements

Do not fabricate historical approval evidence. Demo-only releases must remain identifiable as demo-only. Existing Job Cards without a Production Artwork Set reference must **not** be retro-labelled production-valid. Detect approved revisions without hashes; **detect approved revisions lacking valid Customer Approval Evidence**; **detect Approved for Production Revisions whose Customer Approval Evidence has since been revoked without a corresponding Revision withdrawal (inconsistent data requiring governed remediation, not silent correction)**; **detect Approved Production Artwork Sets containing a Revision whose authoritative evidence is revoked**; **detect revoked Approved evidence lacking required revocation metadata**; **detect Approved evidence whose `approved_revision_key` does not equal its exact Artwork Revision identity**; **before creating the `approved_revision_key` UNIQUE constraint (Section 7.1b), detect every Artwork Revision with more than one historical Approved Customer Approval Evidence record, failing the migration visibly rather than resolving by winner selection, auto-revocation or outcome conversion**; missing authoritative Files; duplicate revision labels within one Artwork; multiple Approved revisions per Artwork **before** creating the revision approved-key constraint; multiple Approved sets per Sales Order **before** creating the set approved-key constraint; and **Submitted Sets whose completeness no longer matches current requirements (stale Sets), which must be rejected or replaced, never silently approved.** Prohibit silent winner selection, silent deletion, silent state changes, and **silent Job Card rebinding to a replacement Set**. Require explicit governed remediation. **Fail visibly** when unresolved invalid data remains.

### 21.3 Evidence Still Required — Runtime Validation

**No validation below is claimed to have passed, and this correction task closes no runtime-validation gate.** All remain outstanding:

1. Standalone Artwork Revision lifecycle and DocType/docstatus strategy. 2. Production Artwork Set lifecycle and DocType/docstatus strategy. 3. Nullable revision approved-key UNIQUE behaviour. 4. Nullable set approved-key UNIQUE behaviour. 5. Concurrent approval of competing revisions. 6. Concurrent approval of competing sets. 7. Atomic supersession of the previously Approved revision. 8. Atomic supersession of the previously Approved set. 9. Release-versus-withdrawal race. 10. Release-versus-set-supersession behaviour. 11. Lock ordering across Job Card and Production Artwork Set. 12. Deadlock and lock-wait treatment. 13. Two-hop private File access from Production users. 14. Approved File deletion prohibition. 15. Approved revision deletion prohibition. 16. Approved set deletion prohibition while referenced. 17. Hash computation and approval-time re-verification. 18. Server-side MIME/extension allow-list behaviour. 19. Company User Permission behaviour on Artwork records. 20. Administrator non-exemption from approval and release invariants. 21. Direct REST and privileged bypass prevention. 22. Post-release withdrawal progression guards. 23. Migration duplicate detection and constraint creation. 24. Production approval and Job Card release capability separation. 25. Successful revocation before internal Revision approval. 26. Successful revocation of an internally Approved Revision. 27. Atomic Revision withdrawal on revocation. 28. Atomic Revision approved-key release on revocation. 29. Atomic withdrawal of every affected currently Approved Set on revocation. 30. Atomic Set approved-key release on revocation. 31. Rollback after Customer Approval Evidence revocation mutation. 32. Rollback after revocation-driven Revision withdrawal logic. 33. Rollback after revocation-driven Set withdrawal logic. 34. Revocation-versus-Revision-approval race. 35. Revocation-versus-Set-approval race. 36. Revocation-versus-Job-Card-release race. 37. Revocation-versus-direct-Set-withdrawal race. 38. Multiple inconsistent Approved Sets containing one Revision under revocation. 39. Revocation direct-REST/bypass prevention. 40. Submitted-Revision permanent ineligibility after approval revocation. 41. Job Card progression guards after revocation-driven Set withdrawal. 42. Service timeout/worker-loss/connection-loss rollback for revocation. 43. Global lock-order and deadlock treatment across requirement-change, release, Revision/Set approval, Revision/Set withdrawal and revocation. 44. Stable domain-error translation for revocation. 45. First Approved Customer Approval Evidence succeeds for one Revision. 46. Second Approved evidence for the same Revision is rejected. 47. Concurrent Approved-evidence creation attempts commit at most one record. 48. The single-column `approved_revision_key` UNIQUE constraint is authoritative. 49. Rejected evidence keeps `approved_revision_key` NULL. 50. Revoked Approved evidence retains `approved_revision_key`. 51. Revocation does not permit another Approved record for the same Revision. 52. Internal production approval rejects zero Approved evidence records. 53. Internal production approval accepts exactly one valid unrevoked Approved record. 54. Internal production approval rejects one revoked Approved record. 55. Corrupt multiple Approved evidence fails visibly. 56. Historical duplicate detection runs before `approved_revision_key` constraint creation. 57. Migration performs no silent winner selection for duplicate Approved evidence. 58. Direct REST cannot assign `approved_revision_key`. 59. Administrator and `ignore_permissions=True` cannot bypass the uniqueness invariant. 60. The exact Artwork Revision Link identity is used with no trimming or case conversion. 61. Company/Sales Order mismatch is rejected independently of uniqueness. 62. Database duplicate-key errors on `approved_revision_key` are translated without exposing SQL or index details.

**None of items 25–62 is claimed to have passed; this correction closes no runtime-validation gate.**

---

## 22. Pre-Publication Gate Checklist

- [x] Architecture Review complete — **Accepted with non-blocking corrections** (second narrow targeted re-review, 2026-07-31)
- [x] Business Review complete — **Accepted with non-blocking corrections** (second narrow targeted re-review, 2026-07-31)
- [ ] Project Owner lifecycle approval granted
- [ ] Proposed technical names governed *(all five now Naming Registry Proposed, 2026-08-22; Under Review, Approval, and collision verification remain outstanding — item remains Open)*
- [x] Exact `printos_core` module paths selected *(resolved at design/governance level 2026-09-09 — `Artwork` module for all five DocTypes in this specification, per `../standards/Naming_Registry.md` Section 13h; `ART-RVR-B02` → `ART-RVR-A12`; no directory, Module Def, or file created — this checklist item's underlying design decision is closed, but Publication and coding readiness remain gated by every other Open item below)*
- [ ] Artwork Revision standalone design validated
- [ ] Revision state/docstatus strategy selected and validated
- [ ] Production Artwork Set state/docstatus strategy selected and validated
- [ ] Required-Artwork completeness calculation validated
- [ ] Revision approved-key uniqueness validated
- [ ] Set approved-key uniqueness validated
- [ ] Concurrent revision approval validated
- [ ] Concurrent set approval and supersession validated
- [ ] Release-versus-withdrawal race validated
- [ ] Cross-aggregate lock ordering validated
- [ ] Two-hop private File permissions validated
- [ ] Approved File deletion guard validated
- [ ] Approved revision deletion guard validated
- [ ] Approved set deletion/reference guard validated
- [ ] SHA-256 submission and approval verification validated
- [ ] MIME/extension allow-list selected and validated
- [ ] Company User Permission behaviour validated for Artwork records
- [ ] Administrator non-exemption validated
- [ ] Direct REST and privileged bypass protection validated
- [ ] Post-release withdrawal progression guards validated
- [ ] Migration and duplicate detection validated
- [ ] Separation of approval and release capabilities validated
- [ ] Customer Approval Evidence technical name governed *(candidate `PrintHub Customer Approval Evidence` now Naming Registry Proposed, 2026-08-22; Under Review, Approval, and collision verification remain outstanding — item remains Open)*
- [ ] Customer Approval Evidence persistence contract finalized
- [ ] Customer-approval prerequisite runtime validated
- [ ] Requirement-change audit persistence selected
- [ ] Requirement-change and Set-withdrawal atomicity validated
- [ ] Zero-required-Artwork rejection validated
- [ ] Stale Submitted Set treatment validated
- [ ] No-rebind progression guards validated
- [ ] Customer Approval Evidence revocation persistence validated
- [ ] Revocation-to-Revision-withdrawal atomicity validated
- [ ] Revocation-to-Set-withdrawal atomicity validated
- [ ] Revocation-versus-Revision-approval race validated
- [ ] Revocation-versus-Set-approval race validated
- [ ] Revocation-versus-Job-Card-release race validated
- [ ] Global lock-order policy selected and validated (requirement-change, release, approval, withdrawal, revocation)
- [ ] Revocation direct-bypass guards validated
- [ ] Revocation-driven Job Card progression guards validated
- [ ] Customer Approval Evidence approved-outcome uniqueness validated
- [ ] `approved_revision_key` database constraint validated
- [ ] Concurrent Approved-evidence creation validated
- [ ] Revoked evidence uniqueness retention validated
- [ ] Duplicate historical Approved evidence detection validated
- [ ] Internal approval zero/one/revoked/multiple evidence behavior validated
- [ ] Direct-bypass prevention validated (uniqueness key)
- [x] Post-correction review verification complete — **Passed with non-blocking observations** (2026-07-31)
- [x] Final cleanup verification completed — **Passed** (2026-07-31)
- [x] Governing Artwork System Design lifecycle Approval — **Approval, Version 1.0, granted 2026-07-31** (governing prerequisite; does not itself grant this specification's own lifecycle approval, Publication, runtime-validation authorization, or implementation authorization)
- [ ] Artwork production gate closed
- [ ] Documentation references synchronized
- [ ] Publication granted

**No item above is complete merely because the design is now documented. This correction task marks none of these items complete.**

---

## 23. Permanent Test Inventory

| Test ID | Scenario |
|---|---|
| A-1 | Artwork creation derives Company from the Sales Order; Submitted Sales Orders only |
| A-2 | Company mismatch between Artwork and Sales Order rejected |
| A-3 | Cross-Company Artwork reuse rejected |
| A-4 | `required_for_production` change after an approved set exists requires a new set |
| A-5 | Revision file replaceable while Draft; immutable from Submitted onward |
| A-6 | Submission computes and stores SHA-256 |
| A-7 | Approval recomputes SHA-256; mismatch rejects approval |
| A-8 | Approved revision content cannot be replaced in place |
| A-9 | At most one Approved revision per Artwork (constraint-enforced) |
| A-10 | Approving a replacement revision supersedes the prior one atomically |
| A-11 | Revision withdrawal requires actor, timestamp and mandatory reason |
| A-12 | Withdrawing a revision withdraws or invalidates any containing Approved set in the same transaction |
| A-13 | Duplicate `revision_label` within one Artwork rejected |
| S-1 | Set membership editable while Draft; immutable from Submitted onward |
| S-2 | Completeness predicate: missing required Artwork rejected |
| S-3 | Completeness predicate: duplicate, unrelated, wrong-Sales-Order or wrong-Company membership rejected |
| S-4 | Completeness predicate: count-only equality is insufficient — exact identity-set comparison enforced |
| S-5 | Set approval rejected when any selected revision is not Approved for Production |
| S-6 | Set approval rejected when a selected revision is Superseded or Withdrawn at the decision point |
| S-7 | Set approval rejected when an authoritative File or stored hash is absent |
| S-8 | At most one Approved set per Sales Order (constraint-enforced) |
| S-9 | Approving a replacement set supersedes the prior Approved set atomically |
| S-10 | Set withdrawal requires actor, timestamp and mandatory reason |
| R-1 | Release blocked with no Production Artwork Set reference |
| R-2 | Release blocked when the set is Draft, Submitted, Rejected, Superseded or Withdrawn |
| R-3 | Release permitted only when the set is Approved for Production |
| R-4 | Release blocked on Company or Sales Order mismatch |
| R-5 | Release blocked when `approved_key` does not equal the exact Sales Order identity |
| R-6 | Release blocked when File identity changed, File missing, File not private, or hash missing |
| R-7 | Release blocked when the acting user lacks governed read access to the set or its evidence |
| R-8 | `production_artwork_set` immutable after Release |
| C-1 | Concurrent approval of competing revisions — exactly one Approved |
| C-2 | Concurrent approval of competing sets — exactly one Approved |
| C-3 | Release versus set withdrawal race — deterministic outcome, no invalid binding |
| C-4 | Release versus set supersession — deterministic outcome |
| C-5 | Cross-aggregate lock ordering (Job Card then set); deadlock and lock-wait treatment |
| P-1 | Approval and release capabilities are separate; release authority alone cannot approve |
| P-2 | Company User Permission scoping on Artwork, revision and set records |
| P-3 | Administrator cannot bypass approval or release invariants |
| P-4 | Ordinary save and REST cannot fabricate approval state, hash, approved key, actor/time, membership, withdrawal state or release authority |
| P-5 | Job Card users have read/download only; no upload, replacement, approval, withdrawal or deletion |
| W-1 | Supersession does not invalidate an existing Job Card binding; Released → In Progress and In Progress → Completed still permitted |
| W-2 | Withdrawal blocks Released → In Progress with an operational alert |
| W-3 | Withdrawal blocks In Progress → Completed with an operational alert; no silent rebinding |
| W-4 | Withdrawal does not alter a Completed Job Card's historical record |
| M-1 | Approved File deletion prohibited while referenced |
| M-2 | Approved revision and approved set deletion prohibited while referenced |
| M-3 | Migration detects missing hashes, missing Files, duplicate labels, multiple Approved revisions and multiple Approved sets, failing visibly without silent remediation |
| M-4 | Demo-only releases remain identifiable; Job Cards lacking a set reference are not retro-labelled production-valid |
| CA-1 | Revision approval rejected without current valid Customer Approval Evidence for the exact locked Revision |
| CA-2 | Revision approval succeeds only after a valid Approved Customer Approval Evidence outcome is recorded |
| CA-3 | Proof status alone, or file existence alone, does not satisfy the customer-approval requirement |
| CA-4 | Customer Approval Evidence cannot be fabricated through ordinary REST field mutation |
| RC-1 | `required_for_production` cannot be changed through ordinary save, REST, import, bulk edit, background assignment, Administrator field mutation or `ignore_permissions=True` |
| RC-2 | Controlled requirement-change operation requires authentication, the dedicated capability, and a mandatory bounded reason |
| RC-3 | Requirement change atomically moves the currently Approved Set (if any) to Withdrawn, recording actor, timestamp and reason |
| RC-4 | Requirement-change lock order (Artwork, then currently Approved Set) is honored; failure rolls back both the requirement change and the Set withdrawal together |
| RC-5 | A Draft Set recomputes completeness before submission; a Submitted Set whose completeness no longer matches current requirements is rejected or replaced, never approved |
| Z-1 | A Sales Order with zero required Artworks cannot have its Production Artwork Set submitted or approved |
| Z-2 | An empty-membership Production Artwork Set cannot be submitted or approved |
| RJ-1 | A Rejected Artwork Revision cannot return to Draft, be resubmitted, be approved, or enter a Production Artwork Set |
| RJ-2 | Correction of a Rejected Revision requires creating a new Artwork Revision |
| NR-1 | `production_artwork_set` is never modified after Job Card Release |
| NR-2 | An In Progress Job Card is never silently updated to a replacement Production Artwork Set |
| NR-3 | Withdrawal of the bound Set on a Released or In Progress Job Card blocks progression and requires controlled `discard()` with mandatory terminal reason plus a replacement Job Card |
| MC-1 | Same-identity submit and approve of one Artwork Revision is permitted in Tier A (no mandatory `approved_by != submitted_by` invariant) |
| SO-1 | Artwork, Revisions, Customer Approval Evidence and Production Artwork Sets remain attached to their original Sales Order after amendment; no automatic relinking occurs |
| RV-1 | Revocation of an Approved Customer Approval Evidence record for an Approved-for-Production Revision atomically withdraws the Revision and every currently Approved Set containing it in one transaction |
| RV-2 | Revocation preserves the original approval payload unchanged and records separate immutable revocation evidence (`revoked_by`, `revoked_on`, `revocation_reason`) |
| RV-3 | A revoked Customer Approval Evidence record cannot be edited, un-revoked, corrected in place, or reused as valid approval authority |
| RV-4 | Revocation of evidence for a Submitted-for-Approval Revision leaves the Revision Submitted but permanently ineligible for internal production approval; the controlled approval operation rejects it |
| RV-5 | Revocation of evidence for a Draft Revision fails visibly rather than silently treating the Revision as approved |
| RV-6 | Revocation of evidence for a Superseded or Withdrawn Revision does not reopen or reverse the Revision's terminal state |
| RV-7 | Multiple inconsistent currently Approved Sets containing one revoked Revision are all processed or the operation fails visibly — no silent winner selection |
| RV-8 | Reapproval after revocation requires new Customer Approval Evidence, a new Artwork Revision, renewed internal approval and a new Production Artwork Set — no in-place reversal |
| RV-9 | Revocation cannot be fabricated or partially executed through ordinary save, REST, import, bulk edit, background assignment, Administrator field mutation or `ignore_permissions=True` |
| RV-10 | Rollback on failure reverts the evidence revocation, Revision state/approved-key/withdrawal evidence, and every affected Set's state/approved-key/withdrawal evidence together |
| U-1 | First Approved Customer Approval Evidence for one Revision succeeds and sets `approved_revision_key == artwork_revision` |
| U-2 | A second Approved evidence record for the same Revision is rejected by the database UNIQUE constraint |
| U-3 | Concurrent attempts to record Approved evidence for one Revision commit at most one record |
| U-4 | Rejected evidence always has `approved_revision_key` NULL |
| U-5 | Revoking Approved evidence retains `approved_revision_key`; revocation never clears it |
| U-6 | A Revision whose Approved evidence was revoked can never receive a second Approved evidence record |
| U-7 | Internal production approval rejects a Revision with zero Approved evidence records |
| U-8 | Internal production approval accepts a Revision with exactly one valid, unrevoked Approved evidence record |
| U-9 | Internal production approval rejects a Revision whose sole Approved evidence record is revoked |
| U-10 | A Revision with more than one historical Approved evidence record (corruption) fails visibly at internal approval, with no silent selection |
| U-11 | Migration detects every Artwork Revision with more than one historical Approved evidence record before `approved_revision_key` constraint creation, failing visibly with no winner selection |
| U-12 | `approved_revision_key` cannot be assigned through ordinary save, REST, import, bulk edit, background assignment, Administrator field mutation or `ignore_permissions=True` |

---

## 24. Explicit Exclusions

Proof and Approval Record internal design, and Customer Approval Evidence's final technical name and full internal design beyond the minimum authority contract in Section 7.1 (all Artwork-internal, deferred, subject to Naming Registry §§26, 38 and 39 governance — a separate, currently untracked question, distinct from `AR-003`'s own recorded eight-term scope, neither resolved nor established here); freelancer-sourced artwork (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; per-line-item Job Card decomposition; a multi-file child table on Artwork Revision (Section 6.1b of the System Design); a mandatory maker-checker identity invariant; an Artwork Hold Job Card state; an Expired approval state; any Tenant field; any ERPNext core modification; and all implementation detail.

---

## 25. Review Status

- **Formal Architecture Review and Business Review:** conducted **2026-07-31**.
- **Architecture Review:** **Accepted with non-blocking corrections.**
- **Business Review:** **Accepted with non-blocking corrections.**
- **Original blocking findings (Closed):** **ART-ARCH-F1**, **ART-BIZ-F1**.
- **Project Owner correction decisions:** approved **2026-07-31**.
- **First targeted re-review:** completed read-only **2026-07-31**; original Architecture findings Closed; original Business finding ART-BIZ-F1 Partially Closed; identified remaining blocking finding ART-TRR-BIZ-F1 and remaining non-blocking finding ART-TRR-ARCH-F1.
- **Second narrow targeted re-review:** completed read-only **2026-07-31**; disposition **Architecture Review: Accepted with non-blocking corrections**, **Business Review: Accepted with non-blocking corrections**; **ART-TRR-BIZ-F1: Closed**; **ART-TRR-ARCH-F1: Closed**; residual non-blocking findings ART-TRR2-ARCH-F1, ART-TRR2-ARCH-F2, ART-TRR2-BIZ-F1 identified and corrected in the prior revision.
- **Post-correction verification:** completed read-only **2026-07-31**; disposition **Post-correction verification: Passed with non-blocking observations**. Verified: Customer Approval Evidence uniqueness; the `approved_revision_key` permanent uniqueness claim; uniqueness retention after revocation; the database-constraint and migration contract. Identified **ART-PCV-F1**, **ART-PCV-F2** (this document, Section 3 — corrected in the prior revision), **ART-PCV-F3**. **No blocking review finding remains.** This disposition is a review outcome only — not document lifecycle Approval, not Publication, and not implementation authorization.
- **Final cleanup verification:** completed read-only **2026-07-31**; disposition **Final cleanup verification: Passed**.
- **Bounded Artwork Approval V1 review:** completed read-only **2026-08-13** over the Version 1.1 / Draft 0.7 package. **Architecture Review: Accepted with non-blocking corrections. Business Review: Accepted with non-blocking corrections.** A parallel Documentation Governance pass identified one blocking accuracy defect and one non-blocking wording defect, both located in the governing System Design's own governance/revision-history text. Local review labels **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2** — **local labels only, not Architecture Review Register identifiers; no AR identifier was created and AR-003 remains unresolved.** The corrections affecting this specification (**LR-ARCH-1** — Section 10.1 supersession-trigger completeness; **LR-ARCH-2** — Section 10.2 complete-propagation guard) are **applied in this same Draft 0.7 revision**, together with **LV-OBS-1**, a further non-blocking Section 10.1 wording clarification raised and corrected subsequently.
- **Verification outcome (completed read-only, 2026-08-13).** An independent read-only post-correction verification has been **completed**. Final results: **Architecture Review — Accepted with non-blocking corrections, with all identified corrections verified applied; Business Review — Accepted; Documentation Governance — Passed.** **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2 and LV-OBS-1 are all Closed by independent read-only verification**, and **no material review finding remains**. These are **local review labels, not Architecture Review Register identifiers**. Review completion is **not** lifecycle Approval, **not** Publication, **not** runtime-validation authorization, **not** implementation authorization, and **not** production readiness. This specification is **not promoted** and remains **Draft, Version 0.7**, **not Published** and **not safe for coding**; the governing System Design remains **Draft, Version 1.1** with **Approval, Version 1.0** as its prior approved baseline. **AR-003 remains unresolved; all 62 runtime gates and the Artwork production gate remain Open; no Full Architecture Freeze is claimed.**
- **Governing Artwork System Design:** `../blueprint/18_Artwork_Management.md` — currently **Approval, Version 1.3**, Project Owner Document Lifecycle Approval **Granted 2026-08-19** at Version 1.1 (Architecture Review and Business Review of the Version 1.1 package both Accepted with non-blocking corrections; all six local Artwork findings Closed), carried forward through a bounded reference-only correction and a bounded naming-status synchronization, both on **2026-08-22**; prior historical baselines **Approval, Version 1.2**, **Approval, Version 1.1**, and **Approval, Version 1.0**, Granted 2026-07-31. This governing approval is **not** Publication of this DocType Specification and **not** implementation authorization.
- **Project Owner Document Lifecycle Approval (this specification):** **Not Granted**
- **Publication:** **Not Granted**
- **Implementation Authorization:** **Not Granted**
- **Direct coding reliance while Draft:** **Prohibited**

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.1 | 2026-09-09 | Module-Path Governance Resolution Synchronization (ART-RVR-B02) | Per explicit Project Owner decision dated 2026-09-09 (`../standards/Naming_Registry.md` Section 13h; `ART-RVR-B02` resolved and moved to `ART-RVR-A12`), synchronized this specification's current-state module-path statements. Updated Section 6's `PrintHub Artwork` naming-status line to record the adopted conceptual module `Artwork` and normalized DocType name `printhub_artwork`, explicitly labeled a design/governance resolution only, not implementation. Updated Section 22's "Exact `printos_core` module paths selected" checklist item from Open to **Closed at the design/governance level**, citing the same decision, while explicitly preserving every other Pre-Publication Gate Checklist item's Open status and stating that Publication and coding readiness remain separately gated. **No conceptual DocType, field, state model, constraint, controlled operation, permission, test, migration rule, or runtime-validation gate was modified; no technical name was selected or Approved; no directory, Module Def, or file was created.** `AR-003` was not resolved or modified. This specification remains **Draft, Version 1.1**, targets Published, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**. All 62 runtime-validation items remain open and unchecked. No AR identifier was invented. No source was inspected or modified as part of this documentation task; no environment, test, or migration was run. |
| 1.0 | 2026-08-22 | Naming-Status and Candidate Synchronization | **Bounded naming-status and candidate synchronization only; Draft status and non-Publication are retained.** Per explicit Project Owner decision dated 2026-08-22, the five Artwork technical DocType names (`PrintHub Artwork`, `PrintHub Artwork Revision`, `PrintHub Production Artwork Set`, `PrintHub Production Artwork Set Item`) entered Naming Registry **Proposed** status (`../standards/Naming_Registry.md` Section 13a), and **`PrintHub Customer Approval Evidence` was added to Section 4 (Naming Boundary) and Section 5 (Aggregate Boundaries) as the first documented provisional technical-name candidate** for the existing, already-governed Customer Approval Evidence entity — the entity and its Section 7.1 business rules are unchanged; only a provisional technical identity is added. Updated Sections 2, 3, 4, 5, 6, 7, 7.1, 8, 8.1, 8.2, and 15's Link-target and naming-status parentheticals to state Proposed, not Approved, status consistently. Kept the Section 22 Pre-Publication Gate Checklist items "Proposed technical names governed" and "Customer Approval Evidence technical name governed" **Open** — Registry Proposed status alone does not satisfy "governed." Updated the governing-reference citations to Artwork System Design **Approval, Version 1.3** and Job Card Tier A System Design **Approval, Version 1.7**. **No conceptual DocType, field, state model, constraint, controlled operation, permission, test, migration rule, or runtime-validation gate was modified; no technical name or module path was selected or approved.** `AR-003` was not resolved or modified. This specification remains **Draft, Version 1.0**, targets Published, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**. All 62 runtime-validation items remain open and unchecked. No AR identifier was invented. |
| 0.9 | 2026-08-22 | AR-003 Scope Misattribution Correction | Bounded reference-only correction, per `../Documentation_Workflow.md` Section 8, mirroring the identical correction applied to the governing `../blueprint/18_Artwork_Management.md` (→ Approval, Version 1.2) on the same date. Corrected two current-state occurrences that incorrectly attributed Customer Approval Evidence's pending technical naming to `AR-003` (Section 7.1's naming clause; Section 24 Exclusions), replacing them with references to Naming Registry §§5, 26, 38 and 39 and stating plainly that `AR-003`'s own recorded scope (its eight named terms) is unrelated. Updated the governing-reference citations (Sections 2, 3, 25) to cite Artwork System Design **Approval, Version 1.2** and Job Card Tier A System Design **Approval, Version 1.6**, both carried forward through their own identical bounded corrections on 2026-08-22. `AR-003` itself was **not** modified, resolved, or reinterpreted. **No conceptual DocType, field, state model, constraint, controlled operation, permission, test, migration rule, or runtime-validation gate was modified; no technical name or module path was selected or approved.** This specification remains **Draft, Version 0.9**, targets Published, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**; no implementation was authorized; no source was inspected or modified; no environment, test, or migration was run. All 62 runtime-validation items remain open and unchecked. No Architecture Review Register item was created or modified; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.8 | 2026-08-19 | Governing Reference Update — Artwork System Design Approval Synchronization | Bounded governing-reference synchronization only, per `../Documentation_Workflow.md` Section 8 ("Minor Updates — Clarifications, added examples, or non-contradictory expansions are minor updates, incrementing the MINOR version"), consistent with this document's own Version 0.6 precedent for an identical class of change. Recorded that the Project Owner granted a further, independent Document Lifecycle Approval to `../blueprint/18_Artwork_Management.md` on **2026-08-19**, promoting it from Draft, Version 1.1 to **Approval, Version 1.1** — its current approved baseline — following completed Architecture Review (Accepted with non-blocking corrections), Business Review (Accepted with non-blocking corrections), Documentation Governance verification (Passed), and independent verification confirming all six local Artwork findings (LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2, LV-OBS-1) Closed with no material finding remaining. **Approval, Version 1.0** is preserved as the prior historical baseline. Updated the three current-state governing-reference citations in Sections 2 and 3 accordingly. **No conceptual DocType, field, state model, constraint, controlled operation, permission, test, migration rule, or runtime-validation gate was modified.** This specification remains **Draft, Version 0.8**, targets Published, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**; no implementation was authorized; no source was inspected or modified; no environment, test, or migration was run. All 62 runtime-validation items remain open and unchecked; none is claimed to have passed. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.7 | 2026-08-13 | Governing Reference Update — Newer-Revision/Approved-Set Supersession Rule (lifecycle citation corrected 2026-08-13) | **Lifecycle citation correction.** This entry first cited the governing System Design as "Approval, Version 1.1." That was incorrect: because its Section 7.2 is new normative content lacking Architecture Review and Business Review, that document is **Draft, Version 1.1**, with **Approval, Version 1.0 remaining its last fully reviewed and Owner-approved baseline** (`../Documentation_Workflow.md` Sections 5 and 7). All citations in Sections 2, 3 and 25 are corrected accordingly, and targeted review of Section 7.2 is recorded as Pending. **Content of this revision.** Recorded the Project Owner-approved bounded documentation reset (2026-08-08) to the governing `../blueprint/18_Artwork_Management.md`, which moved from Approval, Version 1.0 to **Draft, Version 1.1** (Minor, additive): retention of the governed six-state Production Artwork Set lifecycle, and a new business rule resolving the previously open question of what happens to an already-Approved Production Artwork Set when a member Revision is superseded by a newer internal approval. Updated Section 2 and Section 3's citations of the governing System Design version from 1.0 to 1.1. Added Section 10.2, mirroring System Design Section 7.2: where a newer Revision is approved and the previous current Revision it replaces is a member of a currently Approved Set, that Set moves Approved for Production → Superseded (not Withdrawn) in the same transaction, its `approved_key` is cleared, it is never rewritten, Released/In Progress Job Cards bound to it are unaffected under the existing Superseded-not-Withdrawn rule (Section 17), and no Job Card is silently rebound. Updated Section 25 to cite the governing System Design's corrected lifecycle position. **No new conceptual field, state, or runtime-validation gate number was added** — this rule is covered by the existing atomic-supersession gates (Section 21.3, items 7–8); none is claimed to have passed. **No new Architecture Review Register identifier was created; AR-003 remains unresolved.** This document remains Draft, targets Published, and remains **not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted; no source was inspected or modified; no environment, test, or migration was run. No Job Card Tier A document's substantive content, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry. **Bounded review corrections applied within this same Version 0.7 revision (2026-08-13).** Following the completed read-only bounded Architecture and Business Review (Section 25 — both Accepted with non-blocking corrections), two corrections affecting this specification were applied here without a version increment, since Version 0.7 remains uncommitted: **LR-ARCH-1** — Section 10.1's transition matrix and Rules now name **member-Revision supersession (Section 10.2)** as a governed cause of Set supersession alongside replacement-Set approval, with a normative summary separating the Superseded causes from the Withdrawn causes (customer-approval revocation, explicit Set withdrawal, member-Revision withdrawal, requirement change), recording that neither terminal state permits a new Job Card release or rewrites historical membership or bindings, and that they differ only for already-bound Job Cards; **LR-ARCH-2** — Section 10.2 now requires the operation to detect **every** currently Approved Set containing the previous Revision and either supersede all of them in the same transaction or fail visibly and roll back entirely, never silently selecting a winner or leaving partial propagation, mirroring the Section 7.1a revocation guard and without authorizing more than one Approved Set per Sales Order. **Verification completed (2026-08-13).** An independent read-only post-correction verification has since been completed: **LR-ARCH-1 and LR-ARCH-2 are Closed by verified correction**, with final results **Architecture Review — Accepted with non-blocking corrections (all identified corrections verified applied); Business Review — Accepted; Documentation Governance — Passed**. **LV-OBS-1 — local non-blocking wording observation (applied within this same Version 0.7 revision).** The Section 10.1 Rules sentence previously read "superseded sets remain historical and may remain bound to previously Released or later Job Cards." The phrase "or later Job Cards" was ambiguous: the governed meaning is Job Cards at later *lifecycle stages*, but it could be misread as Job Cards created *later*, conflicting with the adjacent rule that no Superseded Set may authorize a new release. The sentence now states that Superseded Sets may remain bound to Job Cards already Released before supersession, including those that later progress to In Progress or Completed, that they cannot authorize a new Job Card release, and that no Job Card is silently rebound — wording materially identical to the governing System Design's Section 8.1. **This clarifies existing behaviour and changes no business semantics**; the rule is unchanged and stated identically in Section 10.2, Section 17 and the Section 10.1 governed-causes summary. Withdrawn-state progression blocking is unaffected. **LV-OBS-1 is a local review label, not an Architecture Review Register identifier; it is Closed by the independent read-only verification completed 2026-08-13.** With that, **all six local Artwork findings — LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2 and LV-OBS-1 — are Closed, and no material Artwork review finding remains.** Review completion is **not** lifecycle Approval; this specification remains **Draft, Version 0.7**, **not Published** and **not safe for coding**. No conceptual field, state, constraint, controlled operation, permission, test, migration rule or runtime-validation gate was added or closed; this specification remains **Draft, Version 0.7**, **not Published**, **not safe for coding**; AR-003 was not resolved and no AR identifier was created. |
| 0.1 | 2026-07-31 | PrintHub Architecture Team | Initial Draft Artwork Authority DocType Specification, subordinate to `../blueprint/18_Artwork_Management.md` (Draft 0.1), created following the Project Owner's approval of the Artwork design defaults on 2026-07-31 — an approved design input that authorized documentation-only design work and is not Architecture Review, Business Review, lifecycle Approval, Publication or implementation authorization. Defines four proposed DocTypes, all names recorded as **proposed pending governed naming treatment**: `PrintHub Artwork` (aggregate root, Company- and Submitted-Sales-Order-scoped, with an authoritative `required_for_production` classification), `PrintHub Artwork Revision` (**standalone standard DocType, explicitly not a child table**, carrying state, the single authoritative private File, an immutable `file_sha256`, a nullable UNIQUE `approved_key` enforcing at most one Approved revision per Artwork, and immutable approval, rejection, withdrawal and supersession evidence), `PrintHub Production Artwork Set` (**the final production-release authority consumed by the Job Card**, with a nullable UNIQUE `approved_key` enforcing at most one Approved set per Sales Order), and `PrintHub Production Artwork Set Item` (**the only child table introduced**, carrying immutable membership values binding each required Artwork to one exact approved revision, and explicitly not the approval authority). Records six-state models for both the revision and the set with only "Approved for Production" permitting a new Registered → Released transition, **no Expired state** because Tier A approvals do not expire, full mutability matrices, database constraints, the exact-identity-set completeness predicate (a count comparison is explicitly insufficient), the controlled-operation inventory, the Job Card reference contract (one immutable `production_artwork_set` Link, schema-optional but mandatory for release, with no copied hashes, actors, timestamps, revision collections, Proof or Approval Record references and no Job Card child table), the release-gate contract with locking reads and Job-Card-then-Set lock ordering, post-release supersession and withdrawal behaviour that preserves bindings and never rewrites Completed history while **adding no Artwork Hold Job Card state**, permission capabilities with normative separation of Artwork approval authority from Job Card release authority, file and attachment rules establishing that **File is storage only and not approval authority**, direct-bypass guards, audit fields where native modified metadata is insufficient, migration requirements prohibiting fabricated evidence and silent remediation, a 24-item outstanding runtime-validation list, a 30-item unchecked Pre-Publication Gate Checklist, and a permanent test inventory. **No runtime validation is claimed to have passed and the Artwork production gate remains open.** This document remains Draft, targets Published, and is **not safe for coding**; Architecture Review and Business Review are Pending; Project Owner lifecycle approval, Publication and Implementation Authorization are all Not Granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. |
| 0.6 | 2026-07-31 | Governing Artwork System Design Approval — Reference Synchronization | Recorded the Project Owner's Document Lifecycle Approval, granted **2026-07-31**, of the governing `../blueprint/18_Artwork_Management.md`, which transitioned from Draft, Version 0.5 to **Approval, Version 1.0** following its completed final cleanup verification (Passed), post-correction verification (Passed with non-blocking observations), and second narrow targeted re-review (Architecture Review and Business Review both Accepted with non-blocking corrections, no blocking finding remaining). Updated Section 2 and Section 3 to cite the governing System Design as Approval, Version 1.0, with Project Owner lifecycle approval Granted 2026-07-31, replacing the stale Draft, Version 0.5 citation. Updated Section 25 to record the governing approval alongside this specification's own unchanged review and lifecycle state. Marked the Section 22 Pre-Publication Gate Checklist's "Final cleanup verification completed" item complete (Passed, 2026-07-31) and added a governing-prerequisite item recording the System Design's Approval 1.0 status — explicitly noting neither item grants this specification's own Project Owner lifecycle approval, Publication, runtime-validation authorization, or implementation authorization. This is a **bounded governing-reference synchronization only**: no conceptual DocType, field, state model, constraint, controlled operation, permission capability, Customer Approval Evidence rule, `approved_revision_key` behavior, revocation-propagation rule, migration rule, permanent test, or runtime-validation definition was modified. All 62 runtime-validation items remain open and unchecked; none is claimed to have passed. This specification remains **Draft**, targets **Published**, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.5 | 2026-07-31 | Post-Correction Verification Cross-Reference Cleanup | Recorded the completed read-only post-correction verification (2026-07-31; disposition **Post-correction verification: Passed with non-blocking observations**), which confirmed Customer Approval Evidence uniqueness, the `approved_revision_key` permanent uniqueness claim, uniqueness retention after revocation, and the database-constraint and migration contract, while identifying three non-blocking prose-staleness observations: ART-PCV-F1 (System Design), ART-PCV-F2 (this document's own stale governing-reference citation of the System Design's version and review status), and ART-PCV-F3 (Documentation Status). Applied **ART-PCV-F2**: corrected Section 3's citation of `../blueprint/18_Artwork_Management.md`, which had never been updated across three prior correction commits and still read "Draft, Version 0.2... Corrections Required, corrections applied, targeted re-review pending" — now correctly cites Draft, Version 0.5, targets Approval, Architecture Review and Business Review both Accepted with non-blocking corrections, Project Owner lifecycle approval Not Granted. ART-PCV-F1 and ART-PCV-F3 are companion-document corrections applied in the same commit to `blueprint/18_Artwork_Management.md` and `Documentation_Status.md` respectively. Marked the Section 22 Pre-Publication Gate Checklist's "Post-correction review verification complete" item complete, reflecting the formal Passed-with-non-blocking-observations disposition, and added a new unchecked item, "Final cleanup verification completed" — **not marked complete by this task**. Updated Section 2 and Section 25 to record the post-correction verification's date, disposition, and the three identified observations, and that ART-PCV-F1 through ART-PCV-F3 are **corrected pending final cleanup verification**, not formally Closed. This is a bounded prose-cleanup correction only: no Customer Approval Evidence uniqueness, revocation, lifecycle, database or migration rule was altered; all 62 runtime-validation items remain unchecked and none is claimed to have passed. This document remains Draft, targets Published, and remains **not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.4 | 2026-07-31 | Second Targeted Re-Review Corrections and Disposition Synchronization | Applied the Project Owner-approved Customer Approval Evidence uniqueness rule, completed the remaining non-blocking review corrections, and synchronized the accepted-with-non-blocking-corrections Architecture and Business Review outcomes. No lifecycle promotion or implementation authorization was granted. Recorded the second narrow targeted re-review (completed read-only, 2026-07-31): **ART-TRR-BIZ-F1 Closed**; **ART-TRR-ARCH-F1 Closed**; **Architecture Review: Accepted with non-blocking corrections**; **Business Review: Accepted with non-blocking corrections**; residual findings ART-TRR2-ARCH-F1, ART-TRR2-ARCH-F2, ART-TRR2-BIZ-F1. Added `approved_revision_key` to the Section 7.1 Customer Approval Evidence field table (conditionally nullable, single-column UNIQUE, server-derived, immutable, retained after revocation, no Company/Sales-Order/Tenant in the key, no normalization). Added Section 7.1b, closing ART-TRR2-BIZ-F1 (Project Owner decision approved 2026-07-31): at most one historical Approved Customer Approval Evidence record per Artwork Revision, permanently, surviving revocation; the twelve uniqueness invariants; the `record_customer_approval` operation contract relying on the database UNIQUE constraint for final race-safe enforcement; the updated internal production-approval predicate with explicit zero/one-unrevoked/one-revoked/corrupt-multiple outcomes and a no-silent-selection rule; the `CustomerApprovalAlreadyApprovedForRevision`-equivalent database-conflict translation; migration duplicate-detection requirements; and direct-bypass guards for the new field — explicitly distinguished from the current-authority "active key" pattern (Revision/Set `approved_key`, cleared on withdrawal/supersession). Updated Section 13 (database constraints), Section 14 (added the "Record Customer Approval Evidence" operation row and updated the "Approve Artwork Revision" row to reference the uniqueness predicate), Section 20 (direct-bypass matrix), Section 21.1 (audit fields), Section 21.2 (migration requirements, now also mirroring the revoked-evidence and duplicate-evidence detection rules), and Section 21.3 (18 new runtime-validation items, 45–62, none claimed to have passed). Added 12 new permanent tests (U-1 through U-12) to Section 23. Marked the Section 22 Pre-Publication Gate Checklist's Architecture Review and Business Review items complete, reflecting the formal Accepted-with-non-blocking-corrections disposition; added 8 new unchecked items for the uniqueness-specific runtime gates and post-correction review verification — **none marked complete**; Project Owner lifecycle approval, Publication, Artwork production gate closure, runtime validation and implementation authorization remain explicitly unchecked. Updated Section 2 and Section 25 to record both formal review dates, the Accepted-with-non-blocking-corrections dispositions, that no blocking review finding remains, and that follow-up post-correction verification is Pending. This document remains Draft, targets Published, and remains **not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. The six-state Revision and Set models, mandatory customer approval, revocation propagation, Job Card reference contract, and release-gate contract are unchanged. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.3 | 2026-07-31 | Customer Approval Evidence Revocation Correction | Applied the Project Owner-approved customer-approval revocation-propagation design decision (approved 2026-07-31), addressing **ART-TRR-BIZ-F1** identified by the first targeted re-review (completed read-only, 2026-07-31; original Architecture findings ART-ARCH-F1–F11 Closed; original Business finding ART-BIZ-F1 Partially Closed). Finalized the `revoked`, `revoked_by`, `revoked_on` and `revocation_reason` fields in Section 7.1 as immutable, system-managed, non-importable, non-bulk-editable and REST-non-writable. Added Section 7.1a: a controlled `revoke_customer_approval(evidence, reason)`-equivalent operation, requiring authentication, Guest rejection, a dedicated revocation capability, applicable Role and Company User Permissions, a mandatory bounded reason, and current locking reads in the order Sales Order → every currently Approved Set containing the Revision (by record identity) → Artwork Revision → Customer Approval Evidence; specifying atomic propagation for an Approved-for-Production Revision (Revision withdrawn, `approved_key` cleared, every affected Approved Set withdrawn and its `approved_key` cleared, single commit, full rollback, no silent winner selection across multiple inconsistent Approved Sets); explicit state-specific behavior for Submitted (permanent ineligibility, no replacement evidence), Draft (visible failure, never silently approved), Rejected (terminal, not revoked) and Superseded/Withdrawn (no reopening); direct-bypass guards; and ten stable conceptual domain errors. Added the corresponding invariant to Section 9.1 rejecting internal approval of a Submitted Revision whose evidence has been revoked. Added the "Revoke Customer Approval Evidence" row to Section 14's controlled-operations table. Extended Section 20's direct-bypass matrix, Section 21.1's audit fields, and Section 21.2's migration requirements to cover revocation. Extended Section 21.3's runtime-validation list with 20 new items (25–44) and Section 22's Pre-Publication Gate Checklist with 9 new unchecked items — **none marked complete**. Added 10 new permanent tests (RV-1 through RV-10) to Section 23. Corrected the stale Job Card System Design cross-reference in Section 3 from Version 1.3 to **Version 1.4** (ART-TRR-ARCH-F1). Updated Section 2 and Section 25 to record the first targeted re-review's outcome and that this correction is **applied pending a second, narrowly-scoped targeted re-review** — this task does not and cannot change either disposition to Accepted, mark ART-TRR-BIZ-F1 formally Closed, grant Publication, or grant Implementation Authorization. No approved Project Owner design decision was reopened; the six-state Revision and Set models are unchanged; no new state was added for revocation. AR-003 was neither resolved nor modified. This document remains Draft, targets Published, and remains **not safe for coding**. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry. |
| 0.2 | 2026-07-31 | Architecture and Business Review Correction | Applied the Project Owner-approved corrections (2026-07-31) to the formal combined Architecture Review (Disposition: **Corrections Required**, blocking finding **ART-ARCH-F1**) and Business Review (Disposition: **Corrections Required**, blocking finding **ART-BIZ-F1**), both dated 2026-07-31. Added Section 6a, a controlled `required_for_production` change operation closing ART-ARCH-F1: `required_for_production` marked internal, system-managed after creation, non-importable and non-bulk-editable; the operation requires authentication, Guest rejection, a dedicated production-requirement-management capability, applicable Role and Company User Permissions, a mandatory bounded reason, locking reads on the Artwork then the currently Approved Set (in that lock order), atomic movement of that Set to Withdrawn with durable audit evidence, single commit and full rollback; removed the undefined "governed out of scope" phrase. Added Section 7.1, the Customer Approval Evidence contract, closing ART-BIZ-F1: minimum conceptual fields (`artwork_revision`, `company`, `sales_order`, `outcome`, `customer_identity`, `decided_on`, `evidence_reference`, revocation metadata); recorded as Artwork-internal with final naming pending governed treatment and AR-003 (neither resolved nor modified); `Proof` explicitly not approval authority; ordinary REST writes cannot fabricate approval. Updated the Section 9 Revision transition matrix (new Section 9.1) to require exact, current, valid Customer Approval Evidence before Submitted for Approval → Approved for Production, and to record Rejected as explicitly terminal with no return to Draft, no resubmission and no Set membership (closing ART-ARCH-F4). Added Section 10.1, the complete Production Artwork Set transition matrix (closing ART-ARCH-F6). Updated Section 11's completeness predicate to require at least one required Artwork before a Set may be submitted or approved, prohibiting empty-membership Sets and zero-required-Artwork Sales Orders with no Tier A exception (closing ART-ARCH-F3), and to validate Customer Approval Evidence for every selected Revision. Added Section 11.1 recording that Set approval is not a second content review but the act of freezing a verified combination of already-approved Revisions (closing ART-BIZ-F4). Added Section 7.2, the bounded reason-field rule (500 Unicode code points, reject-not-truncate, no surrounding whitespace), applied to `rejected_reason`, `withdrawal_reason` and the requirement-change reason (closing ART-ARCH-F7). Corrected Section 17 to remove the undefined In Progress "governed correction procedure," requiring instead that Released and In Progress Job Cards whose bound Set is Withdrawn use controlled `discard()` with mandatory terminal reason and a replacement Job Card, that `production_artwork_set` is never modified after Release, and that no silent rebinding occurs (closing ART-ARCH-F5). Updated Section 18 to add the production-requirement-management and customer-approval-recording capabilities and a Maker-Checker Treatment subsection recording that Tier A does not require distinct submitter/approver identities while Artwork-approval and Job-Card-release authority remain normatively separate (closing ART-BIZ-F3). Extended Section 20's direct-bypass guards to cover `required_for_production` and Customer Approval Evidence. Extended Section 21's audit fields and migration requirements with Customer Approval Evidence, requirement-change audit evidence, and detection of stale Submitted Sets. Added 18 new permanent tests (CA-1 through CA-4, RC-1 through RC-5, Z-1 through Z-2, RJ-1 through RJ-2, NR-1 through NR-3, MC-1, SO-1) to Section 23. Added 8 new unchecked items to the Section 22 Pre-Publication Gate Checklist for Customer Approval Evidence naming and persistence, the customer-approval runtime prerequisite, requirement-change audit persistence, requirement-change/Set-withdrawal atomicity, zero-required-Artwork rejection, stale-Set treatment, and no-rebind progression guards — **none marked complete**. Updated Section 24 to record the deferred multi-file child table and mandatory maker-checker identity exclusions. Updated Section 2 and Section 25 to record the formal review date, both Corrections Required dispositions, the two blocking findings, and that corrections are applied in this revision but **remain subject to targeted re-review** — this task does not and cannot change either disposition to Accepted, mark any runtime-validation gate closed, or close the Artwork production gate. All technical DocType names, including the working name `Customer Approval Evidence`, remain marked proposed pending governed naming treatment; AR-003 is neither resolved nor modified. No approved Project Owner design decision was changed. This document remains Draft, targets Published, and remains **not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry. |

---

## Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
