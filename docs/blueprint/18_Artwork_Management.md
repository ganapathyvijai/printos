# PrintHub Artwork Management and Production Authority

Version:
0.3

Status:
Draft

Date:
2026-07-31

Owner:
PrintHub Architecture Team

Target Lifecycle:
Approval

Document Class:
Artwork architecture and design authority document. This is **not** a direct coding specification — see [../database/Artwork_Authority_DocType_Specification.md](../database/Artwork_Authority_DocType_Specification.md) for the document intended to become the direct coding specification once Published.

---

# 1. Purpose

This document records the Project Owner-approved design baseline for the PrintHub **Artwork** bounded context and its **production authority** — the server-verifiable evidence that permits a PrintHub Job Card to transition from Registered to Released under the production-capable track.

It defines the Artwork aggregate model, the standalone Artwork Revision, the Production Artwork Set that constitutes the final release authority, their state models, file-integrity requirements, approval authority and separation of duties, the production release contract consumed by the Production context, concurrency treatment, supersession and withdrawal behaviour, audit requirements, migration safety, and the runtime validations that remain outstanding.

---

# 2. Governance Status

- **Production-capable Artwork track:** selected by the Project Owner on **2026-07-30**.
- **Artwork design defaults:** approved by the Project Owner on **2026-07-31**.
- **Authorization granted by that approval:** documentation-only Artwork authority design work, as a prerequisite to Job Card Tier A Publication.
- **Formal Architecture Review and Business Review:** conducted **2026-07-31**.
- **Architecture Review disposition:** **Corrections Required.**
- **Business Review disposition:** **Corrections Required.**
- **Blocking findings:** **ART-ARCH-F1** (incomplete `required_for_production` change control) and **ART-BIZ-F1** (undefined relationship between customer approval and internal production approval).
- **Project Owner correction decisions:** approved **2026-07-31**.
- **Correction status:** the approved corrections are **applied in this revision** (Version 0.2). **Targeted re-review remains required** before either disposition may change from Corrections Required.
- **First targeted re-review:** completed read-only **2026-07-31**. Original Architecture findings ART-ARCH-F1 through ART-ARCH-F11: **Closed**. Original Business finding **ART-BIZ-F1: Partially Closed** — the mandatory-before-approval obligation was fully specified, but revocation propagation was undefined. Remaining blocking finding: **ART-TRR-BIZ-F1** (Customer Approval Evidence revocation propagation). Remaining non-blocking finding: **ART-TRR-ARCH-F1** (stale Job Card System Design version citation).
- **ART-TRR-BIZ-F1 correction status:** the Project Owner approved the customer-approval revocation-propagation design decision on **2026-07-31** (Section 6.2c, Section 16.2). **Correction applied — closure pending second targeted re-review.** This correction does not itself close ART-TRR-BIZ-F1, does not change either Review disposition to Accepted, and does not grant any lifecycle promotion. **A second, narrowly-scoped targeted re-review remains pending.**
- **Project Owner document lifecycle approval:** **Not Granted**.
- **Publication:** **Not applicable** as a direct coding authority — this document targets Approval, not Published.
- **Artwork product implementation:** **Not Authorized**.
- **Job Card Tier A:** **Not Authorized**.
- **Implementation Authorization:** **Not Granted**.

The Project Owner's design-default approval and the Project Owner's correction-decision approval are each an **approved design input**. Neither is targeted re-review acceptance, Architecture Review acceptance, Business Review acceptance, document lifecycle Approval, Publication, or implementation authorization.

---

# 3. Naming Boundary

The following technical names are **proposed working names only — pending governed naming treatment**:

| Business term (approved) | Proposed technical name — pending governed naming treatment |
|---|---|
| Artwork | `PrintHub Artwork` |
| Artwork Revision | `PrintHub Artwork Revision` |
| Production Artwork Set | `PrintHub Production Artwork Set` |
| — (set membership row) | `PrintHub Production Artwork Set Item` |

No claim is made that these names are approved by the Naming Registry. The Naming Registry is not modified by this document, and AR-003 is neither resolved nor modified.

---

# 4. Scope

**In scope:** the Artwork aggregate root; standalone Artwork Revisions; the Production Artwork Set and its immutable membership; approval, rejection, supersession and withdrawal; authoritative file evidence and integrity; Company and Sales Order scoping; the production release contract consumed by Job Card; permission capabilities and separation of duties; audit evidence; migration safety; and the outstanding validation gates.

**Out of scope for this document:** Proof and Approval Record internal design (Artwork-internal, deferred); freelancer-sourced design work (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; the Configuration Studio Approval Designer's own design; and any implementation detail.

---

# 5. Bounded-Context Ownership

The **Artwork bounded context owns the complete Artwork lifecycle** within `printos_core`.

The **Production context (Job Card) consumes Artwork authority but does not own**:

- Artwork preparation;
- Artwork revision management;
- proofing;
- customer approval;
- production approval;
- supersession;
- withdrawal or revocation.

**Dependency direction:** `Sales → Artwork → Production`.

**Prohibited reverse dependency:** Artwork must **not** depend on Job Card. Artwork approval must be meaningful with no Job Card in existence, and no Artwork state may be driven by Job Card lifecycle.

**Configuration Studio Approval Designer** remains an **optional and deferrable** prerequisite. Artwork must therefore provide a **default governed approval mechanism** that functions without it; Job Card Tier A must not acquire a hard dependency on the Approval Designer.

## 5.1 Distinct Approval Stages (corrected 2026-07-31 — ART-BIZ-F1, ART-BIZ-F7)

Five distinct concepts are frequently conflated and must be kept separate:

| Stage | What it is | Who performs it | Authoritative? |
|---|---|---|---|
| **Proof** | The customer-facing review artifact presenting one Artwork Revision for review | Sent by Artwork staff; viewed by the Customer | **No** — a Proof's existence or status is never sufficient evidence of approval on its own |
| **Customer approval** | The durable, recorded outcome of the customer's decision on the exact Revision shown in a Proof | Customer or governed customer representative | **Yes** — recorded as Customer Approval Evidence (Section 6.2a); **mandatory** before internal production approval, with **no Tier A waiver** |
| **Internal production approval** | An authorized internal Artwork approver's decision that a Revision — having already obtained valid customer approval — may be used in production | Internal Artwork approver (Approve-revision capability) | **Yes** — moves the Revision to Approved for Production; requires valid Customer Approval Evidence to exist first |
| **Production Artwork Set approval** | The act of freezing the complete, exact combination of already-Approved Revisions that together satisfy every required Artwork for the Sales Order | Internal Artwork approver (Approve-set capability) | **Yes** — the final release authority; see Section 9.1 for its rationale |
| **Job Card release** | The Production context consuming an Approved Production Artwork Set to permit Registered → Released | Production user (Job Card release capability) | Consumes the authority above; performs no content or approval judgment of its own |

**Customer approval is mandatory before internal production approval, for every Artwork Revision, with no Tier A waiver.** `Proof` alone is never treated as approval evidence.

---

# 6. Aggregate Model

Four aggregates, plus one child-table membership concept:

| Concept | Role |
|---|---|
| **Artwork** | Aggregate root. One Company, one Submitted Sales Order, one or more Artwork Revisions, and an explicit production-requirement classification. |
| **Artwork Revision** | **Standalone** aggregate. The independently approvable unit carrying file evidence and integrity. |
| **Customer Approval Evidence** *(normative working name — Section 6.2a)* | **Standalone, Artwork-internal aggregate.** The durable record of the customer's decision on one exact Artwork Revision. Mandatory before internal production approval. |
| **Production Artwork Set** | Aggregate root. The **final production-release authority** consumed by the Job Card. |
| **Production Artwork Set Item** | **Child table** of Production Artwork Set. An immutable membership value binding one Artwork to one exact Artwork Revision. |

## 6.1 Artwork

Minimum authoritative relationships: one Company; one Submitted Sales Order; one or more Artwork Revisions; explicit production-requirement classification.

Minimum conceptual fields: `company` (Link → Company; required; derived from the Sales Order; immutable after creation); `sales_order` (Link → Sales Order; required; Submitted Sales Orders only; immutable after creation); `title` (Data; operator-readable); `required_for_production` (Check; default true; the authoritative indicator used when calculating set completeness).

**Rules.** Company must equal the Sales Order Company. Cross-Company reuse is prohibited. Tenant identity remains implicit at the site/database level and **no Tenant field** exists. A required Artwork must be represented in an approved Production Artwork Set before Job Card release. Optional Artwork does not block release unless later marked required through a governed Artwork operation. Changes to production requirements after an approved set exists require a **new** Production Artwork Set.

### 6.1a Controlled Requirement-Change Operation (corrected 2026-07-31 — ART-ARCH-F1)

`required_for_production` is **authoritative** and is **not** directly editable through an ordinary form save, REST field mutation, import, bulk edit, background field assignment, Administrator field mutation, or `ignore_permissions=True`. It may change **only** through one controlled Artwork-domain operation, conceptually equivalent to `change_artwork_production_requirement(artwork, required, reason)` (exact technical name not prescribed).

The operation must: require authentication; reject Guest; require a dedicated Artwork production-requirement-management capability; require the applicable Role Permissions and Company User Permissions; require a **mandatory reason** validated under the bounded reason-field rule (Section 6.2b); acquire a **current locking read on the Artwork row**; acquire a **locking read on the currently Approved Production Artwork Set for the same Sales Order where one exists**; update the requirement classification; **atomically move that currently Approved Set to Withdrawn** — not Superseded, because no replacement Set has yet been approved — recording withdrawal actor, timestamp and reason on the Set; commit once; and roll all changes back together on failure.

**Lock order for this operation: (1) Artwork; (2) currently Approved Production Artwork Set** — consistent with the Job-Card-then-Set order used elsewhere; both begin from the Artwork/Sales-Order side of the dependency graph rather than from the Job Card, so no ordering conflict arises with the release-gate lock order (Section 14).

**Consequences:** the withdrawn Set cannot be used for a new Job Card release; Released and In Progress Job Cards already bound to it follow the governed withdrawal progression guards (Section 16.2); Completed Job Cards remain historical; a replacement Production Artwork Set must be created and approved before new release; no Job Card may be silently rebound.

A **Draft** Set is not production authority and must recompute completeness before submission. A **Submitted** Set whose completeness no longer matches the current required-Artwork set **must not be approved** — it must be rejected or replaced through the governed Set lifecycle, never silently approved against stale membership.

The change must produce **durable audit evidence** containing: the Artwork; the prior value; the new value; the actor; the timestamp; and the reason. **The exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated** (Section 21).

The previously undefined phrase "governed out of scope" is **removed**. For Tier A, `required_for_production` is the **sole** authoritative requirement-classification mechanism.

### 6.1b One File Per Revision — Tier A Limitation (ART-BIZ-F2)

Exactly one authoritative production file per Artwork Revision is a **deliberate Tier A limitation**. Multi-file production work is represented through **multiple required Artwork records**, not a multi-file child table. Examples: front and back → two Artwork records; print and cutting files → two Artwork records; multiple panels → one Artwork record per authoritative panel file; language variants → separate Artwork records where each variant is required. Each such Artwork has its own Revisions, its own Customer Approval Evidence, its own internal production approval, and exactly one Revision's membership in the Production Artwork Set. **No multi-file child table is added to Artwork Revision in Tier A.**

## 6.2 Artwork Revision — Standalone

Artwork Revision is a **standalone DocType and must not be mapped as a child table**, because it:

- is independently approvable;
- requires its own state;
- requires independent permissions;
- must be lockable;
- requires file-integrity evidence;
- may be referenced by Production Artwork Set membership;
- may require unique database constraints;
- must preserve immutable approval evidence.

This corrects the previous mapping of Artwork Revision as a child table of Artwork.

Minimum conceptual fields: `artwork` (Link; required; immutable); `company` (Link → Company; required; derived from Artwork; immutable; retained for native Company User Permission scoping); `revision_label` (Data; human-readable sequence such as R1/R2; unique within one Artwork; **not** the authoritative record identity); `state` (Select; system-managed); `artwork_file` (Attach or equivalent governed private-file reference; exactly one authoritative production file for Tier A); `file_sha256` (Data; exact SHA-256 digest; system-managed; immutable from submission onward); `approved_key` (nullable; holds the Artwork identity **only** while the revision is Approved for Production; protected by a UNIQUE constraint; prevents more than one Approved revision per Artwork); `approved_by` (Link → User; immutable approval evidence); `approved_on` (Datetime; immutable approval evidence); `rejected_reason` (bounded plain text where required); `withdrawn_by` (Link → User); `withdrawn_on` (Datetime); `withdrawal_reason` (bounded plain text; mandatory for withdrawal); `superseded_by` (Link → Artwork Revision; records the replacement revision).

`modified`, `modified_by` and Version history **must not** be relied upon as the authoritative approval event, because later supersession or withdrawal changes the record. Version history may be **supplemental only**.

### 6.2a Customer Approval Evidence — Artwork-Internal (corrected 2026-07-31 — ART-BIZ-F1)

**Customer approval for the exact Artwork Revision is mandatory before internal production approval. There is no Tier A waiver.**

Referred to normatively as **`Customer Approval Evidence`** until governed naming is complete; its final technical name remains pending governed naming treatment and the existing AR-003 naming question, which is **not** resolved or modified by this document.

Sequence: (1) an Artwork Revision is submitted and frozen; (2) durable customer approval is recorded against that **exact** Revision as Customer Approval Evidence; (3) an authorized internal Artwork approver may transition the Revision to Approved for Production; (4) a Production Artwork Set freezes the complete combination of approved Revisions; (5) Job Card release consumes the Approved Production Artwork Set.

**Minimum authority contract:** links to exactly one Artwork Revision; Company matches the Revision and Artwork; Sales Order context matches; records a durable Approved or Rejected outcome; an Approved outcome is required for internal production approval; records customer identity or governed customer representative identity; records approval timestamp; records evidence source or reference; is immutable after recording except through a governed revocation or correction process; **cannot be fabricated through ordinary REST field mutation; cannot be inferred from a file merely existing; cannot be inferred from Proof status alone.**

`Proof` is the customer-facing review artifact. **`Proof` alone is not authoritative customer-approval evidence** (Section 5.1).

The controlled Artwork Revision approval operation **must verify current valid Customer Approval Evidence for the exact locked Revision** before permitting Submitted for Approval → Approved for Production. The Revision's production-approved state therefore implies: exact customer approval exists; it applies to this exact Revision; it remains valid at the approval decision point; and internal production approval was separately performed.

**Customer Approval Evidence is not added to the Job Card aggregate.** It remains strictly Artwork-internal (Section 13).

### 6.2b Bounded Reason-Field Rule

Applies to `rejected_reason`, `withdrawal_reason`, and the requirement-change reason (Section 6.1a): reject `None`, empty, or whitespace-only input; **reject leading or trailing whitespace rather than silently trimming it**; allow internal newline, carriage-return and tab characters; reject other C0 control characters; enforce a maximum of **500 Unicode code points**; **reject over-length input — never silently truncate**; store plain text only; escape on every rendered surface.

### 6.2c Customer Approval Evidence Revocation and Atomic Propagation (corrected 2026-07-31 — ART-TRR-BIZ-F1; Project Owner decision approved 2026-07-31)

**Customer Approval Evidence revocation is supported in Tier A.** An Approved Customer Approval Evidence record may be revoked **only** through one controlled Artwork-domain operation, conceptually equivalent to `revoke_customer_approval(evidence, reason)` (exact technical name not prescribed). Revocation **preserves the original approval evidence unchanged** — customer identity, approval timestamp, outcome, evidence reference, Revision Link, Company and Sales Order are never altered — and records separate, immutable revocation evidence. **Revocation cannot be reversed in place.** A later customer reapproval requires new Customer Approval Evidence, a new Artwork Revision, renewed internal production approval, and a new Production Artwork Set (Section 7.1).

**Operation requirements:** authenticated; reject Guest; require a dedicated Customer Approval Evidence revocation capability, applicable Role Permissions and Company User Permissions; require a **mandatory reason** validated under the bounded reason-field rule (Section 6.2b); use current locking reads; decide only from locked, transaction-authoritative values; no early commit; commit once; roll all effects back together on failure; expose stable domain errors; reject direct REST, field, import, bulk-edit, background-assignment, Administrator-field-mutation and `ignore_permissions=True` bypass.

**Revocation evidence fields (conceptual, immutable once written):** `revoked` (system-managed indicator); `revoked_by` (Link → User); `revoked_on` (Datetime); `revocation_reason` (bounded plain text, Section 6.2b). Only an **Approved** Customer Approval Evidence record may enter the revoked condition. **Rejected evidence is terminal historical evidence and is never "revoked."** A revoked record cannot be edited, deleted through ordinary application paths, un-revoked, corrected in place, or reused as valid approval authority. The Customer Approval Evidence record **owns** the authoritative revocation actor, timestamp and reason; `modified`/`modified_by` and Version history are supplemental only.

**Atomic propagation — Approved Revision.** When valid Customer Approval Evidence for an internally Approved-for-Production Artwork Revision is revoked, **one database transaction** must: (1) mark the evidence revoked and write `revoked_by`/`revoked_on`/`revocation_reason`; (2) move the Artwork Revision Approved for Production → Withdrawn; (3) clear the Revision's nullable `approved_key`; (4) write the Revision's existing withdrawal evidence (`withdrawn_by`, `withdrawn_on`, `withdrawal_reason`); (5) identify **every** currently Approved Production Artwork Set containing that exact Revision; (6) move **every** such Set Approved for Production → Withdrawn; (7) clear each affected Set's nullable `approved_key`; (8) write each affected Set's withdrawal evidence; (9) commit once. Any failure rolls back all of the above together. **The database constraints normally permit only one Approved Set per Sales Order, but propagation must detect and process — or fail visibly on — every inconsistent currently Approved Set containing the Revision; no silent winner selection or partial propagation is permitted.**

**Submitted for Approval.** Where customer approval is revoked before internal production approval, the evidence becomes revoked; the Revision **remains Submitted for Approval for historical traceability**; that Revision becomes **permanently ineligible for internal production approval** and cannot obtain replacement Customer Approval Evidence; correction requires a **new** Artwork Revision with a new Customer Approval Evidence record referencing it. The controlled internal-approval operation **must reject** a Submitted Revision whose former approval evidence has been revoked.

**Draft.** A Draft Revision should not yet have authoritative Approved Customer Approval Evidence. Where inconsistent data exists, revocation must fail visibly or follow explicit governed remediation — it must never silently treat a Draft Revision as production-approved or customer-approved.

**Rejected.** Rejected Customer Approval Evidence is terminal historical evidence and is not revoked. A Rejected Artwork Revision remains terminal.

**Superseded or Withdrawn Revision.** Revocation remains an auditable change to the Customer Approval Evidence but does **not** reopen or reverse the Revision's existing terminal state. Any currently Approved Set containing such a terminal Revision represents inconsistent data and must be withdrawn, or must cause visible failure, through the governed propagation operation.

**Production Artwork Set propagation.** Customer-approval revocation is a **distinct cause** of Set withdrawal, with the **same** authority consequences as withdrawal caused by direct governed Set withdrawal, `required_for_production` classification change (Section 6.1a), or withdrawal of an approved member Revision: state becomes Withdrawn; `approved_key` becomes NULL; withdrawal actor, timestamp and reason are recorded; the Set cannot support a new Job Card release, cannot be silently returned to Approved, and cannot be modified in place to replace the member Revision — a new Set must be constructed and approved. Set membership remains historical and immutable. The withdrawal reason must identify that the cause was customer-approval revocation for a member Revision **without exposing unnecessary customer-sensitive evidence**.

**Locking baseline (design-level, not runtime validated).** Current locking reads in the deterministic provisional order: (1) Sales Order; (2) every currently Approved Production Artwork Set containing the Revision, sorted by record identity; (3) Artwork Revision; (4) Customer Approval Evidence. This is the **current design baseline** for revocation only. Set approval, Set withdrawal, Revision withdrawal and Customer Approval Evidence revocation must be reconciled to **one non-contradictory global lock-order policy before Publication** (Section 21); exact MariaDB/Frappe behavior remains subject to runtime validation; **no lock-order or deadlock runtime gate is closed by this documentation correction.** This baseline does not alter the previously recorded Job-Card-then-Set release ordering (Section 14) or the Artwork-then-Set requirement-change ordering (Section 6.1a).

**Direct-bypass guards.** Ordinary save, REST resource mutation, standard document method endpoints, import, bulk edit, background field assignment, Administrator, and `ignore_permissions=True` must not be able to fabricate or partially execute revocation. Client input cannot directly assign `revoked`, `revoked_by`, `revoked_on`, `revocation_reason`, Revision Withdrawn state, Revision `approved_key`, Set Withdrawn state, Set `approved_key`, or withdrawal evidence. Database UNIQUE constraints remain independently authoritative for approved-key integrity. Raw SQL by a database administrator remains outside normal application guarantees.

**Stable domain errors (conceptual):** `CustomerApprovalAlreadyRevoked`; `CustomerApprovalNotApproved`; `CustomerApprovalRevisionMismatch`; `CustomerApprovalCompanyMismatch`; `CustomerApprovalSalesOrderMismatch`; `CustomerApprovalRevocationReasonInvalid`; `ArtworkRevisionConcurrentModification`; `ProductionArtworkSetConcurrentModification`; `CustomerApprovalRevocationPropagationFailed`; `CustomerApprovalEvidenceNotAccessible`. Authentication and authorization errors remain distinct from domain conflicts. No stack traces, raw SQL, database credentials or sensitive customer evidence are exposed. Domain rejection and uniqueness conflicts are never automatically retried; lock-wait timeout and deadlock may receive bounded retry only after the global lock-order policy (above) is runtime validated.

## 6.3 Production Artwork Set

The Production Artwork Set is the **final production-release authority consumed by the Job Card**.

Minimum conceptual fields: `company` (Link → Company; required; immutable); `sales_order` (Link → Sales Order; required; Submitted only; immutable); `state` (Select; system-managed); `approved_key` (nullable; contains the exact Sales Order identity **only** while the set is Approved for Production; protected by a UNIQUE constraint; permits **at most one** currently Approved Production Artwork Set per Sales Order); `approved_by` (Link → User); `approved_on` (Datetime); `withdrawn_by` (Link → User); `withdrawn_on` (Datetime); `withdrawal_reason` (bounded plain text); `superseded_by` (Link → Production Artwork Set).

## 6.4 Production Artwork Set Item — Child Table

Minimum child fields: `artwork` (Link; required); `artwork_revision` (Link; required); `revision_label` (optional, read-only display value only — the authoritative value remains on the revision).

**Set-item rules:** one row per required Artwork; no duplicate Artwork membership; every linked Artwork belongs to the same Company and Sales Order; every linked revision belongs to the linked Artwork; every linked revision must be Approved for Production at set approval; membership becomes immutable when the set is submitted for approval; set membership is **not modified in place after approval**; changed content requires a **new set**.

The child table is acceptable **because its rows are immutable aggregate membership values — it is not the approval authority itself.**

---

# 7. Artwork Revision State Model

| State | Business meaning | Permits use in an approved set? | File/hash |
|---|---|---|---|
| **Draft** | File and metadata may be prepared | No | File may be replaced |
| **Submitted for Approval** | Frozen for review | No — does not permit Job Card release | File identity and hash immutable |
| **Approved for Production** | The **only** revision state allowed in an approved Production Artwork Set | Yes | Immutable; approval actor and timestamp recorded |
| **Rejected** | Review declined; terminal for that revision | Never permits production release | Immutable |
| **Superseded** | Replaced by a later approved revision | Cannot be selected for a newly approved set; historical references remain intact | Immutable |
| **Withdrawn** | Approval explicitly revoked; requires actor, timestamp and mandatory reason | Cannot be used for new release | Immutable |

### 7.1 Artwork Revision Transition Matrix (corrected 2026-07-31 — ART-ARCH-F4)

| Current | Allowed transition | Result |
|---|---|---|
| Draft | Submit for Approval | Submitted for Approval |
| Submitted for Approval | Approve (requires valid Customer Approval Evidence, Section 6.2a) | Approved for Production |
| Submitted for Approval | Reject | Rejected |
| Approved for Production | Supersede through a later approval | Superseded |
| Approved for Production | Withdraw / revoke | Withdrawn |
| **Rejected** | **None** | **Terminal** |
| Superseded | None | Terminal |
| Withdrawn | None | Terminal |

**Rejected is terminal for that Artwork Revision.** A Rejected Revision cannot return to Draft, cannot be resubmitted, cannot be approved, and cannot enter a Production Artwork Set. It remains historical. **Corrections require creating a new Artwork Revision.**

Any currently Approved set containing a revision that is being **Withdrawn** must itself be withdrawn or invalidated **in the same governed transaction**.

**No Expired state is defined.** Tier A approvals have **no validity period**.

---

# 8. Production Artwork Set State Model

### 8.1 Production Artwork Set Transition Matrix (corrected 2026-07-31 — ART-ARCH-F6)

| Current | Allowed transition | Result |
|---|---|---|
| Draft | Submit for Approval | Submitted for Approval |
| Submitted for Approval | Approve | Approved for Production |
| Submitted for Approval | Reject | Rejected |
| Approved for Production | Supersede through replacement approval | Superseded |
| Approved for Production | Withdraw / revoke | Withdrawn |
| Rejected | None | Terminal |
| Superseded | None | Terminal |
| Withdrawn | None | Terminal |

**Only Approved for Production permits a new Registered → Released Job Card transition.**

Rules: at most one set may be Approved for Production for a Sales Order; approving a replacement set **supersedes the currently Approved set in the same transaction**; superseded sets remain historical and may remain bound to previously Released or later Job Cards; withdrawn sets may not be used for new release; withdrawal requires actor, timestamp and reason; approved membership is immutable; **no silent in-place replacement is permitted**. **A requirement change (Section 6.1a) may also move an Approved Set to Withdrawn through its dedicated controlled operation, independent of a normal withdraw/revoke action.**

---

# 9. Production Artwork Set Completeness

The authoritative completeness predicate, evaluated **at Production Artwork Set approval time**:

1. Load the Sales Order and Company.
2. Determine the complete current set of Artwork records where `sales_order` equals the set Sales Order, `company` equals the set Company, and `required_for_production` is true. **`required_for_production` is the sole authoritative requirement-classification mechanism** (Section 6.1a) — the previously undefined "governed out of scope" phrase is removed.
3. **Require at least one required Artwork.** A production-capable Tier A Sales Order must have `required Artwork count >= 1` before a Production Artwork Set may be submitted or approved. An empty Production Artwork Set is invalid; a Set with zero membership rows cannot be submitted; a Set cannot be approved where the authoritative required-Artwork identity set is empty; Registered → Released cannot succeed with zero required Artworks. **There is no Tier A no-Artwork exception; any future no-Artwork production path requires a separate governed design change.**
4. Compare that authoritative required-Artwork set with the set-item Artwork membership.
5. Require **exact equality**: no missing required Artwork; no duplicate Artwork; no unrelated Artwork; no wrong Sales Order; no wrong Company.
6. Validate every selected Artwork Revision: belongs to the corresponding Artwork; state is Approved for Production; **valid Customer Approval Evidence exists for this exact Revision** (Section 6.2a); authoritative private File exists; stored SHA-256 exists; file identity is immutable; revision is not Withdrawn; revision is not Superseded at the authoritative approval decision point.
7. Approve the Production Artwork Set **only when every condition passes**.

**A mere count comparison is insufficient — exact identity-set equality with a minimum required-Artwork cardinality of one is required.**

## 9.1 Production Artwork Set Approval Rationale (ART-BIZ-F4)

Production Artwork Set approval is **not** a second file-content review. Its business purpose is to: recompute the current required-Artwork identity set; verify exact completeness (Section 9); verify that every selected Revision has valid customer approval; verify that every selected Revision is internally Approved for Production; freeze the selected combination; and create **one atomic, server-verifiable production-release authority**. The content review already occurred at Revision approval (Section 6.2a); Set approval freezes *which combination* of already-approved Revisions constitutes one production package.

---

# 10. File Integrity and Authority

- The **Artwork Revision owns the authoritative File**; the File **must be private**.
- **Attachment existence alone does not mean approval.**
- File upload uses standard Frappe attachment handling.
- Upload and replacement are allowed **only while the revision is Draft**.
- **Submission computes and stores SHA-256.**
- **Approval recomputes and compares SHA-256**; a hash mismatch rejects approval.
- Approved content **cannot be replaced in place**; changed content requires a **new revision**.
- Approved or ever-approved evidence **must not be silently deleted**.
- Thumbnails and previews are **convenience-only**.
- MIME and extension restrictions require a **server-side allow-list**.
- **Malware scanning remains a separate platform production-readiness control** and is not part of this gate.
- Job Card users receive **read/download only** where authorized; they receive **no** Artwork upload, replacement, approval, withdrawal or deletion capability.

**A full file-content rehash is not required during every Job Card release by default.** At release the server validates: authoritative File identity still matches the approved revision; the File record exists; the File is private; the stored hash is present; and the set and revision evidence has not been governed invalid. **Missing evidence, changed identity or a missing hash blocks release.**

---

# 11. Approval Authority and Separation of Duties

**Artwork capabilities:** create Artwork draft; edit Artwork draft; **manage production-requirement classification (the controlled `required_for_production` operation, Section 6.1a)**; create Artwork Revision; upload or replace Draft revision file; submit revision for approval; **record Customer Approval Evidence outcome** (Section 6.2a); approve revision for production; reject revision; create Production Artwork Set; submit set for approval; approve set for production; reject set; withdraw or revoke; view/download approved Artwork; administer exceptional corrections.

**Production capability:** release Job Card against an approved Production Artwork Set.

**Rules.** Artwork approval authority is **distinct** from Job Card release authority. Holding release authority does **not** grant approval authority; holding approval authority does **not** automatically grant release authority. Standard Role Permissions and Company User Permissions remain the baseline. The **P-2 provisioning invariant applies to Artwork operational roles** — a role-bearing user with zero Company User Permissions is an **invalid provisioned state**, because such a user is unrestricted rather than denied. **No `has_permission` hook** and **no `permission_query_conditions` hook** is required by current evidence; either would require a future reproduced gap plus a controlled design change. Administrator is privileged for permission purposes but **cannot bypass production-approval domain invariants**. Tenant isolation remains site/database based and **no Tenant field** exists.

## 11.1 Maker-Checker Treatment (ART-BIZ-F3)

Revision submission and Revision production approval are **separate capabilities**. **Tier A does not require the submitter and approver to be different user identities** — no mandatory `approved_by != submitted_by` server invariant is introduced. A Tenant may enforce maker-checker separation through governed role assignment as an operational matter, outside document scope. This is independent of, and does not weaken, the mandatory separation between Artwork approval capability and Job Card release capability, which **does** remain a normative document-level rule.

---

# 12. Company, Sales Order and Tenant Rules

Company consistency is mandatory and transitive: `Company(Job Card) == Company(Sales Order) == Company(Artwork) == Company(Artwork Revision) == Company(Production Artwork Set) == Company(Customer Approval Evidence)`. Only Submitted Sales Orders are eligible. Cross-Company reuse is prohibited. Multi-Company users are handled by the standard permission stack as the explicit union of assigned Company User Permissions. Tenant identity remains implicit at the site/database boundary per Accepted ADR-015; **no Tenant field** is introduced on any record.

## 12.1 Sales Order Amendment (ART-BIZ-F8)

Artwork, Artwork Revisions, Customer Approval Evidence and Production Artwork Sets **remain attached to their original Sales Order**. They are **never retargeted** to an amended or replacement Sales Order — this follows from the `sales_order` field being immutable on each of these records. A replacement or materially changed Sales Order requires **new** Artwork authority records, **new** customer approval evidence where applicable, and a **new** Production Artwork Set. Existing approval records remain historical against the original Sales Order. **No automatic relinking is permitted.**

---

# 13. Job Card Reference Contract

The Job Card Tier A design will eventually add **one** conceptual field:

| Aspect | Value |
|---|---|
| Concept | Production Artwork Set reference |
| Proposed field name | `production_artwork_set` |
| Field type | Link → proposed `PrintHub Production Artwork Set` |
| Schema-required | **No** — Registered records may be created before Artwork approval |
| Required | **Mandatory for successful Registered → Released** |
| Client mutability | **None** |
| Assignment | Server-side controlled Release action only |
| After Release | **Immutable** |
| Importable | No |
| Bulk-editable | No |

The Job Card **does not** acquire: an Artwork Revision child table; copied file hashes; copied approval actor; copied approval timestamp; a copied revision collection; Proof references; Approval Record references; or Customer Approval Evidence references. **The approved Production Artwork Set remains the authority.**

---

# 14. Production Release Contract (Registered → Released)

1. Authenticate.
2. Reject Guest.
3. Require the governed Job Card release capability, the applicable standard Role Permission, and the applicable Company User Permission.
4. Acquire the Job Card row with a current locking read equivalent to `SELECT ... FOR UPDATE`.
5. Use the locking-read values as the authoritative Job Card state.
6. Require `status = Registered`; `docstatus = 0`; the Submitted Sales Order remains valid; Job Card Company equals Sales Order Company.
7. Resolve the selected Production Artwork Set.
8. Acquire the Production Artwork Set row through a locking read.
9. Use the locked set values as authoritative.
10. Require `state = Approved for Production`; set Company equals Job Card Company; set Sales Order equals Job Card Sales Order; `approved_key` equals the exact Sales Order identity; approved evidence remains structurally intact; authoritative File records exist; required stored hashes exist.
11. Require the acting user to have governed read access to the set and its approved evidence.
12. Assign the Production Artwork Set reference to the Job Card.
13. Set Job Card status to Released through the controlled transition.
14. Commit once.
15. Any failure rolls back all Job Card changes.

**Lock ordering for this release operation: (1) Job Card, then (2) Production Artwork Set.**

**Decisions must not be taken from stale plain reads under REPEATABLE-READ.** The runtime validation must later confirm the full cross-aggregate lock order and deadlock treatment (Section 21).

---

# 15. Concurrency Treatment

Every controlled operation takes current locking reads and decides from locked values; none commits early; each commits exactly once. Material races and their required outcomes: concurrent approval of competing revisions for one Artwork (at most one Approved, enforced by the revision `approved_key` UNIQUE constraint); concurrent approval of competing sets for one Sales Order (at most one Approved, enforced by the set `approved_key` UNIQUE constraint); atomic supersession of the previously Approved revision or set within the approving transaction; release versus withdrawal (serialized by the set row lock — either Released against a still-approved set, or release rejected); release versus set supersession; and cross-aggregate lock ordering with deadlock and lock-wait treatment. None of these behaviours is claimed validated — all appear in Section 21.

---

# 16. Supersession, Withdrawal and Revocation

## 16.1 Supersession

For a Job Card already bound to a Production Artwork Set: supersession alone **does not invalidate the existing binding**; Released may progress to In Progress when the bound set is Superseded but not Withdrawn; In Progress may complete when the bound set is Superseded but not Withdrawn; Completed remains historical; and the superseded set **cannot be used for a new Job Card release**. Historical production authority is preserved.

## 16.2 Withdrawal or Revocation

**A Set withdrawn because of customer-approval revocation (Section 6.2c) follows exactly these same rules — customer-approval revocation is a Set-withdrawal cause, not a distinct Job Card procedure.**

For a Job Card whose bound set is **Withdrawn** (corrected 2026-07-31 — ART-ARCH-F5: the previously undefined In Progress "governed correction procedure" that implied possible rebinding is **removed**):

- **Released** — Registered → Released is already complete; **Released → In Progress is blocked**; an operational alert is required; **use controlled `discard()` with mandatory terminal reason** and create a replacement Job Card against a newly Approved Set.
- **In Progress** — **In Progress → Completed is blocked**; an operational alert is required; **use controlled `discard()` with mandatory terminal reason** and create a replacement Job Card against a newly Approved Set.
- **Completed** — the historical production record **remains unchanged**; withdrawal does not rewrite history; **controlled `cancel()` / Void with mandatory reason may be used only where governed business follow-up requires it.**

**Rules:** `production_artwork_set` is **never modified after Release**; there is **no silent rebind**; an In Progress Job Card is **never updated** to a replacement Set; **no Artwork Hold state** is added; replacement follows the existing one-active-Job-Card-per-Sales-Order and terminal lifecycle rules. The approved Job Card lifecycle is unchanged: enforcement uses transition guards plus operational alerting, and the two existing audited terminal exits (controlled `discard()` and controlled `cancel()`) remain the **only** governed correction paths — no separate "correction procedure" exists.

---

# 17. API and Desk Surfaces

Desk, restricted API and background execution share the **same server authority**. Required controlled operations, conceptually: submit Artwork Revision for approval; approve Artwork Revision; reject Artwork Revision; withdraw Artwork Revision; submit Production Artwork Set for approval; approve Production Artwork Set; reject Production Artwork Set; withdraw Production Artwork Set; and release Job Card against an approved set.

Requirements: authenticated, mutation-only operations; Guest rejected; permission and capability checks; current locking reads; no early commit; stable domain errors; no stack traces or SQL in responses. **Ordinary REST field mutation cannot fabricate** approval state, file hash, approved key, approval actor/time, set membership, withdrawal state, or Job Card release authority.

Rendered browser behaviour is **not validated** by this authoring task.

---

# 18. Audit Model

Explicit immutable evidence is required wherever native modified metadata is insufficient — approved records undergo **further** state changes (supersession, withdrawal), which would overwrite `modified`/`modified_by`.

- **Artwork Revision:** `approved_by`; `approved_on`; `file_sha256`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`.
- **Customer Approval Evidence:** outcome; customer identity or governed representative identity; decided-on timestamp; evidence source/reference; revocation metadata where present (Section 6.2a).
- **Production Artwork Set:** `approved_by`; `approved_on`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`.
- **Requirement-change events** (Section 6.1a): Artwork; prior value; new value; actor; timestamp; reason. **The exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated** (Section 21).

Record identity carries immutable revision/set identity; explicit fields carry approval and withdrawal events; `modified` and `modified_by` describe **current record metadata only**; Version history is **supplemental when enabled**. **Version rows must not be assumed to be guaranteed authoritative audit storage.**

---

# 19. Migration and Historical Safety

Future-safe rules: do not fabricate historical approval evidence; demo-only releases must remain identifiable as demo-only; existing Job Cards without a Production Artwork Set reference must not be retro-labelled production-valid; detect approved revisions without hashes; detect approved revisions lacking valid Customer Approval Evidence; detect missing authoritative Files; detect duplicate revision labels within one Artwork; detect multiple Approved revisions per Artwork **before** creating the revision approved-key constraint; detect multiple Approved Production Artwork Sets per Sales Order **before** creating the set approved-key constraint; detect Submitted Sets whose completeness no longer matches current requirements (stale Sets) and require them to be rejected or replaced, never silently approved; prohibit silent winner selection; prohibit silent deletion or state changes; prohibit silent Job Card rebinding to a replacement Set; require explicit governed remediation; and **fail visibly** when unresolved invalid data remains.

---

# 20. Exclusions

This design excludes: Proof and Approval Record internal design, and Customer Approval Evidence's final technical name and full internal design beyond the minimum authority contract in Section 6.2a (all Artwork-internal, deferred, and subject to existing naming governance including AR-003, which is neither resolved nor modified); freelancer-sourced artwork (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; per-line-item Job Card decomposition; a multi-file child table on Artwork Revision (Section 6.1b); a mandatory maker-checker identity invariant (Section 11.1); an Artwork Hold Job Card state; an Expired approval state; any Tenant field; any ERPNext core modification; and all implementation detail.

---

# 21. Required Runtime Validation Gates

The Artwork design remains **Draft** and **cannot be treated as implementation-ready**. The approved corrections from the 2026-07-31 Architecture and Business Review (Section 2) have been applied in this revision, but **both dispositions remain Corrections Required until a targeted re-review confirms the corrections** — this document does not and cannot self-certify that outcome. The following runtime validations are additionally required before the companion DocType Specification may be Published. **None is claimed to have passed; this correction task closes no runtime-validation gate.**

1. Standalone Artwork Revision lifecycle and DocType/docstatus strategy.
2. Production Artwork Set lifecycle and DocType/docstatus strategy.
3. Nullable revision approved-key UNIQUE behaviour.
4. Nullable set approved-key UNIQUE behaviour.
5. Concurrent approval of competing revisions.
6. Concurrent approval of competing sets.
7. Atomic supersession of the previously Approved revision.
8. Atomic supersession of the previously Approved set.
9. Release-versus-withdrawal race.
10. Release-versus-set-supersession behaviour.
11. Lock ordering across Job Card and Production Artwork Set.
12. Deadlock and lock-wait treatment.
13. Two-hop private File access from Production users.
14. Approved File deletion prohibition.
15. Approved revision deletion prohibition.
16. Approved set deletion prohibition while referenced.
17. Hash computation and approval-time re-verification.
18. Server-side MIME/extension allow-list behaviour.
19. Company User Permission behaviour on Artwork records.
20. Administrator non-exemption from approval and release invariants.
21. Direct REST and privileged bypass prevention.
22. Post-release withdrawal progression guards.
23. Migration and duplicate detection, and constraint creation.
24. Production approval and Job Card release capability separation.
25. Successful revocation before internal Revision approval.
26. Successful revocation of an internally Approved Revision.
27. Atomic Revision withdrawal on revocation.
28. Atomic Revision approved-key release on revocation.
29. Atomic withdrawal of every affected currently Approved Set on revocation.
30. Atomic Set approved-key release on revocation.
31. Rollback after Customer Approval Evidence revocation mutation.
32. Rollback after revocation-driven Revision withdrawal logic.
33. Rollback after revocation-driven Set withdrawal logic.
34. Revocation versus Revision approval race.
35. Revocation versus Set approval race.
36. Revocation versus Job Card release race.
37. Revocation versus direct Set withdrawal race.
38. Multiple inconsistent Approved Sets containing one Revision under revocation.
39. Revocation direct REST/bypass prevention.
40. Submitted-Revision permanent ineligibility after approval revocation.
41. Job Card progression guards after revocation-driven Set withdrawal.
42. Service timeout, worker loss and database connection-loss rollback for revocation.
43. Global lock-order and deadlock treatment across requirement-change, release, Revision/Set approval, Revision/Set withdrawal and revocation.
44. Stable domain-error translation for revocation.

**None of items 25–44 is claimed to have passed. This correction closes no runtime-validation gate.**

---

# 22. Related Documents

- [../database/Artwork_Authority_DocType_Specification.md](../database/Artwork_Authority_DocType_Specification.md) — companion specification, targets Published.
- [05_Domain_Model.md](05_Domain_Model.md) — domain entities and cardinality.
- [06_Bounded_Contexts.md](06_Bounded_Contexts.md) — Artwork context ownership.
- [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) — entity inventory.
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md) — implementation ownership mapping.
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — module dependency direction.
- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — Approval, Version 1.4; the consuming Production design (**not modified by this task**).

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-31 | PrintHub Architecture Team | Initial Draft. Populates the previously empty Artwork Management blueprint placeholder with the Artwork production authority design, following the Project Owner's production-capable track selection (2026-07-30) and approval of the Artwork design defaults (2026-07-31), which authorized documentation-only Artwork authority design work as a prerequisite to Job Card Tier A Publication. Records: Artwork bounded-context ownership of the complete Artwork lifecycle, with Production consuming but never owning Artwork preparation, revision management, proofing, customer approval, production approval, supersession or withdrawal; the dependency direction Sales → Artwork → Production and the prohibited reverse dependency Artwork → Job Card; the Artwork aggregate root scoped to one Company and one Submitted Sales Order with an explicit `required_for_production` classification; **Artwork Revision as a standalone DocType, not a child table**, with its rationale; the Production Artwork Set as the final production-release authority consumed by the Job Card, with an immutable child-table membership (Production Artwork Set Item) binding each required Artwork to one exact approved revision; six-state models for both the revision and the set with only "Approved for Production" permitting release and **no Expired state** (Tier A approvals do not expire); the exact-identity-set completeness predicate covering every required Artwork; file-integrity treatment (private File owned by the revision, SHA-256 computed at submission and re-verified at approval, no in-place replacement of approved content, and release-time validation of File identity, existence, private status and recorded hash without a default full rehash); the single immutable `production_artwork_set` Job Card reference with no copied hashes, actors, timestamps, revision collections, Proof or Approval Record references and no Job Card child table; the Registered → Released contract with locking reads, Job-Card-then-Set lock ordering and single-commit rollback; supersession preserving existing bindings while blocking new releases; withdrawal blocking Released → In Progress and In Progress → Completed with operational alerting while never rewriting Completed history, **adding no Artwork Hold Job Card state**; separation of Artwork approval authority from Job Card release authority; the P-2 provisioning invariant applied to Artwork roles with no custom permission hook required by current evidence; explicit immutable audit evidence where native modified metadata is insufficient, with Version history supplemental only; migration and historical-safety rules prohibiting fabricated approval evidence and silent remediation; and 24 required runtime-validation gates, **none of which is claimed to have passed**. All technical DocType names are recorded as proposed pending governed naming treatment; the Naming Registry is not modified and AR-003 is neither resolved nor modified. Architecture Review and Business Review are **Pending**; Project Owner document lifecycle approval is **Not Granted**; Artwork product implementation, Job Card Tier A and Implementation Authorization all remain **Not Authorized / Not Granted**. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. |
| 0.3 | 2026-07-31 | Customer Approval Evidence Revocation Correction | Applied the Project Owner-approved customer-approval revocation-propagation design decision (approved 2026-07-31), addressing **ART-TRR-BIZ-F1** (Customer Approval Evidence revocation propagation) identified by the first targeted re-review (completed read-only, 2026-07-31; original Architecture findings ART-ARCH-F1–F11 Closed, original Business finding ART-BIZ-F1 Partially Closed). Added Section 6.2c: Customer Approval Evidence revocation is supported in Tier A through one controlled Artwork-domain operation, preserving the original approval evidence unchanged and recording separate immutable revocation evidence (`revoked`, `revoked_by`, `revoked_on`, `revocation_reason`); revocation cannot be reversed in place; reapproval requires new Customer Approval Evidence, a new Artwork Revision, renewed internal production approval and a new Production Artwork Set. Specified atomic propagation for an internally Approved Revision: one transaction revokes the evidence, withdraws the Revision, clears its `approved_key`, identifies and withdraws every currently Approved Production Artwork Set containing that Revision, clears each affected Set's `approved_key`, and commits once, with full rollback on failure and no silent winner selection across multiple inconsistent Approved Sets. Defined explicit state-specific behavior for Submitted for Approval (permanently ineligible, no replacement evidence, correction requires a new Revision), Draft (fail visibly or governed remediation, never silently treated as approved), Rejected (terminal, not revoked) and Superseded/Withdrawn (revocation does not reopen the terminal state). Recorded that Set withdrawal caused by revocation carries the same consequences as withdrawal from direct Set withdrawal, requirement-change or member-Revision withdrawal (Section 8.1, Section 16.2), and that a Job Card bound to such a Set follows the existing withdrawal progression guards without introducing a distinct procedure. Recorded a design-level locking baseline for revocation (Sales Order; every affected Approved Set by record identity; Artwork Revision; Customer Approval Evidence) and an explicit requirement to reconcile it with the requirement-change and release-gate lock orders into one non-contradictory global policy before Publication — **not claimed runtime validated**. Added direct-bypass guards and ten stable conceptual domain errors for revocation. Extended Section 21's runtime-validation gate list with 20 new items (25–44) covering revocation atomicity, races, rollback, bypass prevention and lock ordering, **none claimed to have passed**. Corrected the stale Job Card System Design cross-reference in Section 22 from Version 1.3 to **Version 1.4** (ART-TRR-ARCH-F1). Updated Section 2 to record the first targeted re-review's outcome and that this correction is **applied pending a second, narrowly-scoped targeted re-review** — this task does not and cannot change either Review disposition to Accepted, close ART-TRR-BIZ-F1, promote this document's lifecycle Status, or grant Publication or implementation authorization. No approved Project Owner design decision was reopened; the six-state Revision and Set models are unchanged and no new state was added for revocation. This document remains Draft and is not promoted to Approval by this correction. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; AR-003 was not resolved or modified. |
| 0.2 | 2026-07-31 | Architecture and Business Review Correction | Applied the Project Owner-approved corrections (2026-07-31) to the findings of the formal combined Architecture Review (Disposition: **Corrections Required**, blocking finding **ART-ARCH-F1**: incomplete `required_for_production` change control) and Business Review (Disposition: **Corrections Required**, blocking finding **ART-BIZ-F1**: undefined relationship between customer approval and internal production approval), both dated 2026-07-31. Added Section 6.1a, a controlled `required_for_production` change operation closing ART-ARCH-F1: authentication, Guest rejection, a dedicated production-requirement-management capability, mandatory bounded reason, locking reads on the Artwork then the currently Approved Set (in that lock order), atomic movement of that Set to Withdrawn (not Superseded, since no replacement yet exists) with durable audit evidence, single commit, full rollback, and removal of the undefined "governed out of scope" phrase. Added Section 6.2a, the Customer Approval Evidence contract, closing ART-BIZ-F1: customer approval is now recorded as mandatory, with no Tier A waiver, before internal production approval, for the exact Artwork Revision; `Proof` is explicitly not approval authority; the controlled Revision-approval operation must verify current valid Customer Approval Evidence before Submitted for Approval → Approved for Production. Added Section 5.1 explicitly distinguishing Proof, customer approval, internal production approval, Production Artwork Set approval and Job Card release. Added Section 6.1b recording exactly one authoritative file per Revision as a deliberate Tier A limitation, with multi-file production work represented through multiple required Artwork records (closing ART-BIZ-F2). Added Section 6.2b, the bounded reason-field rule (500 Unicode code points, reject-not-truncate, no surrounding whitespace), applied to `rejected_reason`, `withdrawal_reason` and the requirement-change reason (closing ART-ARCH-F7). Added Section 7.1 and Section 8.1, complete Artwork Revision and Production Artwork Set transition matrices, with Rejected recorded as explicitly terminal (closing ART-ARCH-F4 and ART-ARCH-F6). Updated Section 9's completeness predicate to require at least one required Artwork before a Set may be submitted or approved, prohibiting empty-membership Sets and zero-required-Artwork Sales Orders with no Tier A exception (closing ART-ARCH-F3), and to validate Customer Approval Evidence for every selected Revision. Added Section 9.1 recording that Production Artwork Set approval is not a second content review but the act of freezing a verified combination of already-approved Revisions (closing ART-BIZ-F4). Added Section 11.1 recording that Tier A does not require distinct submitter/approver identities, while Artwork-approval and Job-Card-release authority remain normatively separate (closing ART-BIZ-F3). Added Section 12.1 recording that Artwork, Revisions, Customer Approval Evidence and Production Artwork Sets remain permanently attached to their original Sales Order with no automatic relinking on amendment (closing ART-BIZ-F8). Corrected Section 16.2 to remove the undefined In Progress "governed correction procedure" that implied possible rebinding, replacing it with an explicit requirement that Released and In Progress Job Cards whose bound Set is Withdrawn use controlled `discard()` with mandatory terminal reason and a replacement Job Card against a newly Approved Set, that `production_artwork_set` is never modified after Release, and that no silent rebinding of any kind occurs (closing ART-ARCH-F5). Extended the audit model (Section 18) and migration rules (Section 19) with Customer Approval Evidence and requirement-change audit evidence, and with detection of stale Submitted Sets whose completeness no longer matches current requirements. Recorded that the exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item, not runtime validated. Updated Section 2 to record the formal review date, both Corrections Required dispositions, the two blocking findings, and that corrections are applied in this revision but **remain subject to targeted re-review** — this task does not and cannot change either disposition to Accepted. All technical DocType names, including the working name `Customer Approval Evidence`, remain marked proposed pending governed naming treatment; AR-003 is neither resolved nor modified. No approved Project Owner design decision was changed: the production-capable track, dedicated Artwork bounded context, Production Artwork Set authority, standalone Artwork Revision, Production Artwork Set Item as the sole child table, no approval expiry, supersession-preserves-bindings behaviour, one active Job Card per Sales Order, no Artwork Hold state, and the separation of approval and release capability are all unchanged. This document remains Draft and is not promoted to Approval by this correction. No implementation authorization was granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
