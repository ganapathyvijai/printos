# PrintHub Artwork Management and Production Authority

Version:
1.1

Status:
Approval

Date:
2026-08-19

Current Approved Baseline:
**Approval, Version 1.1 (2026-08-19).** Project Owner Document Lifecycle Approval was granted on **2026-08-19**, promoting this document from **Draft, Version 1.1** to **Approval, Version 1.1**, following: completed Architecture Review (Accepted with non-blocking corrections) and Business Review (Accepted with non-blocking corrections) of the Version 1.1 package (2026-08-13); completed Documentation Governance verification (Passed); and independent read-only post-correction verification confirming **all six local Artwork findings — LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2, LV-OBS-1 — Closed by verified correction**, with no material Artwork review finding remaining. These local labels are not Architecture Review Register identifiers.

**Prior historical baseline:** Approval, Version 1.0 (2026-07-31), superseded by this Version 1.1 approval; every rule recorded in the 1.0 baseline is preserved unchanged in 1.1, which is additive only (Section 7.2).

**Bounded meaning of this approval.** This approval accepts the current architecture and business-design baseline only. It does **not** grant or imply: Publication; direct coding authority; source-inspection authorization; implementation authorization; environment creation; runtime validation; production authorization or readiness; closure of any of the 62 Artwork runtime gates; closure of the Artwork production gate; resolution of AR-003; approval of final technical names; approval of the `printos_core` module path; global lock-order resolution; or Full Architecture Freeze. `../database/Artwork_Authority_DocType_Specification.md` remains **Draft**, **not Published**, and **not safe for coding**.

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
- **Architecture Review disposition:** **Accepted with non-blocking corrections.**
- **Business Review disposition:** **Accepted with non-blocking corrections.**
- **Original blocking findings (Closed):** **ART-ARCH-F1** (incomplete `required_for_production` change control) and **ART-BIZ-F1** (undefined relationship between customer approval and internal production approval).
- **Project Owner correction decisions:** approved **2026-07-31**.
- **First targeted re-review:** completed read-only **2026-07-31**. Original Architecture findings ART-ARCH-F1 through ART-ARCH-F11: **Closed**. Original Business finding **ART-BIZ-F1: Partially Closed** — the mandatory-before-approval obligation was fully specified, but revocation propagation was undefined. Remaining blocking finding at that point: ART-TRR-BIZ-F1. Remaining non-blocking finding at that point: ART-TRR-ARCH-F1.
- **Second narrow targeted re-review:** completed read-only **2026-07-31**. Disposition: **Architecture Review: Accepted with non-blocking corrections**; **Business Review: Accepted with non-blocking corrections**. **ART-TRR-BIZ-F1: Closed by second targeted re-review.** **ART-TRR-ARCH-F1: Closed by second targeted re-review.** Residual non-blocking findings identified: **ART-TRR2-ARCH-F1** (stale internal audit cross-reference), **ART-TRR2-ARCH-F2** (migration-rule asymmetry), **ART-TRR2-BIZ-F1** (Customer Approval Evidence uniqueness not yet normatively specified).
- **ART-TRR2 correction status:** the Project Owner approved the Customer Approval Evidence uniqueness rule on **2026-07-31** (Section 6.2d). ART-TRR2-ARCH-F1 and ART-TRR2-ARCH-F2 were corrected in the prior revision (Section 18, Section 19). ART-TRR2-BIZ-F1 was corrected in the prior revision via the uniqueness rule. **No blocking review finding remains.**
- **Post-correction verification:** completed read-only **2026-07-31**. Disposition: **Post-correction verification: Passed with non-blocking observations.** Verified and confirmed: Customer Approval Evidence uniqueness; the `approved_revision_key` permanent uniqueness claim; uniqueness retention after revocation; the database-constraint and migration contract. Identified three non-blocking observations: **ART-PCV-F1** (this document's stale runtime-validation preamble — **correction applied in this revision**), **ART-PCV-F2** (companion DocType Specification's stale governing-reference version/status — corrected in that document in this same commit), **ART-PCV-F3** (Documentation Status's stale Artwork-document version references — corrected in that document in this same commit). **Correction applied — closure pending final cleanup verification.** None of ART-PCV-F1 through ART-PCV-F3 is formally Closed by that revision. This correction and the Accepted-with-non-blocking-corrections disposition are **not** document lifecycle Approval, not Publication, not runtime-validation authorization, and not implementation authorization.
- **Final cleanup verification:** completed read-only **2026-07-31**. Disposition: **Final cleanup verification: Passed.** ART-PCV-F1, ART-PCV-F2 and ART-PCV-F3 confirmed corrected; no blocking Architecture finding remains; no blocking Business finding remains; no remaining review observation.
- **Project Owner document lifecycle approval:** **Granted 2026-07-31.** Approved lifecycle version: **Approval, Version 1.0.** Lifecycle target: **achieved.** This grant accepts the Artwork architecture and business-design baseline recorded in this document. **Approval 1.0 is not** a direct coding specification, not Publication of the companion DocType Specification, not runtime-validation authorization, not product implementation authorization, and not production-readiness confirmation.
- **Publication:** **Not applicable** as a direct coding authority — this document is now Approval, not Published; the companion DocType Specification remains Draft and is **not** made safe for coding by this grant.
- **Artwork runtime validation:** **Not Authorized and not executed.** None of the 62 runtime-validation items (Section 21) is marked complete by this grant.
- **Artwork production gate:** **Open.**
- **Artwork product implementation:** **Not Authorized**.
- **Job Card Tier A:** **Not Authorized**.
- **Implementation Authorization:** **Not Granted**.
- **Full Architecture Freeze:** **not achieved.**
- **Newer-Revision/Approved-Set business decision:** Project Owner-approved **2026-08-08** (Section 7.2). This resolves the previously open question of what happens to an already-Approved Production Artwork Set when a member Revision is superseded by a newer approval. **No new Architecture Review Register identifier was created; AR-003 remains unresolved and is not affected.**
- **Lifecycle status of Version 1.1 (corrected 2026-08-13).** Version 1.1 adds new normative content (Section 7.2) on Project Owner design authority. Project Owner approval of a design decision is an **approved design input**, and under `../Documentation_Workflow.md` Section 6 and Section 11 it is a **distinct step** from Architecture Review and Business Review — it does not substitute for them. Because Workflow Section 5 defines **Approval** as "All required reviews passed; awaiting final Owner sign-off," and Section 7 prohibits reaching Approval "until every applicable review lens has been explicitly passed," this document **returns to Draft at Version 1.1**. Workflow Section 8 independently confirms MINOR as the correct version increment for an additive, non-contradictory expansion, while noting such updates "still require review appropriate to the category." **The Approval, Version 1.0 baseline (2026-07-31) remains the last fully reviewed and Owner-approved version**; every rule it contained is preserved unchanged in Version 1.1. An earlier revision of this task incorrectly retained Approval status at Version 1.1; that over-claim is corrected here. **Targeted Architecture Review and Business Review of Section 7.2 are Pending.** This correction does not reverse the Project Owner's business decision — the decision stands as an approved design input awaiting its review lenses.
- **Bounded Artwork Approval V1 review (completed read-only, 2026-08-13).** A bounded Architecture Review and Business Review of the Version 1.1 / Draft 0.7 package was completed. **Architecture Review disposition: Accepted with non-blocking corrections. Business Review disposition: Accepted with non-blocking corrections.** A parallel Documentation Governance pass identified **one blocking accuracy defect** and **one non-blocking wording defect** in this document's own governance and revision-history text. Local review labels **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2** were used, together with **LV-OBS-1**, a further non-blocking wording observation raised and corrected subsequently; these are **local labels only and are not Architecture Review Register identifiers** — **no AR identifier was created and AR-003 remains unresolved.**
- **Verification outcome (completed read-only, 2026-08-13).** An independent read-only post-correction verification of the corrections above has been **completed**. Final results: **Architecture Review — Accepted with non-blocking corrections, with all identified corrections verified applied; Business Review — Accepted; Documentation Governance — Passed.** **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2 and LV-OBS-1 are all Closed by independent read-only verification**, and **no material review finding remains**. Review completion is **not** Project Owner lifecycle Approval, **not** Publication, **not** runtime-validation authorization, **not** implementation authorization, and **not** production readiness. This document is **not promoted** by this recording and remains **Draft, Version 1.1**, with **Approval, Version 1.0** remaining the prior approved baseline; the companion DocType Specification remains **Draft, Version 0.7** and **not safe for coding**. **AR-003 remains unresolved; all 62 runtime gates and the Artwork production gate remain Open; no Full Architecture Freeze is claimed.**
- **Project Owner Document Lifecycle Approval (2026-08-19).** The Project Owner explicitly granted lifecycle Approval on **2026-08-19**, superseding the "remains Draft, Version 1.1" status recorded in the bullets above (which correctly describe this document's state as of the dated actions they each describe). This document is now **Approval, Version 1.1** (see the Document Control header above); **Approval, Version 1.0** is the prior historical baseline. This approval accepts the current architecture and business-design baseline only and grants none of the authorities enumerated in the header's "Bounded meaning of this approval" note; the Artwork production gate, all 62 runtime gates, AR-003, and every other item listed there remain exactly as recorded elsewhere in this section.
- **V1 acceptance-scope reset:** the active Artwork V1 business-acceptance scope is the ten behaviors in Section 21.1. The 62 detailed runtime-validation gate list (Section 21.2) and the concurrency treatment (Section 15) are retained as **later-phase reference material**, not active V1 business-acceptance scope. **No gate is claimed passed by this reclassification.**
- **Stale-status correction:** Section 21's preamble previously stated "The Artwork design remains Draft," which contradicted the **Approval, Version 1.0 status this document held at that time** (granted 2026-07-31). That sentence is corrected in this revision (Section 21). This is a correction of stale text carried forward from before the Version 1.0 promotion; it is unrelated to, and must not be confused with, the separate lifecycle position of Version 1.1 recorded below.

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

### 6.2d Customer Approval Evidence Uniqueness (corrected 2026-07-31 — ART-TRR2-BIZ-F1; Project Owner decision approved 2026-07-31)

**Each Artwork Revision may have at most one Customer Approval Evidence record whose immutable outcome is Approved.** This is a **permanent historical uniqueness claim**, distinct from the current-authority "active key" pattern used elsewhere in this design (Section 6.2, Section 6.3): it is **not cleared by withdrawal or supersession**, and it **survives revocation**. The Approved evidence retains its uniqueness claim after revocation — revocation changes whether the approval remains *valid*, not whether it *occurred*. A second Approved Customer Approval Evidence record must **never** be created for the same Artwork Revision, including after the first was revoked. Reapproval against the same Artwork Revision is **prohibited**; reapproval requires a new Artwork Revision, new Customer Approval Evidence, renewed internal production approval, and a new Production Artwork Set (Section 6.2c). Duplicate historical Approved evidence for one Revision is **invalid data**, never a valid concurrent state.

**Uniqueness field (conceptual):** `approved_revision_key` — a proposed internal field name, not a final technical DocType name; a data-compatible field suitable for a database UNIQUE constraint. Holds the exact validated Artwork Revision identity **only** while the evidence's outcome is Approved; **NULL** for Rejected evidence; **remains populated after revocation** — revocation never clears it. System-managed; not client-writable, form-editable, REST-writable, importable, bulk-editable, or background-directly-assignable; Administrator field mutation and `ignore_permissions=True` are prohibited from setting it by the domain invariant. Immutable after persistence. No normalization, trimming or case conversion is applied — the exact successfully validated Revision identity is used as-is. **Company, Sales Order and Tenant are deliberately excluded from this key** — the Artwork Revision record identity is already site-unique, so including them would not add integrity and would only complicate the constraint.

**Invariants:** (1) Approved evidence: `approved_revision_key == artwork_revision`; (2) Rejected evidence: `approved_revision_key IS NULL`; (3) revoked Approved evidence retains outcome Approved, retains `approved_revision_key`, and sets `revoked = true` — the evidence no longer satisfies the internal production-approval predicate but the uniqueness claim stands; (4) `approved_revision_key` is immutable after persistence; (5) revocation must not clear it; (6) no application path converts Approved to Rejected; (7) no application path converts Rejected to Approved in place; (8) no application path moves evidence to a different Revision; (9) no second Approved evidence may be created for a Revision, even where the first was revoked; (10) the database UNIQUE constraint is independently authoritative; (11) an ordinary existence query is not sufficient concurrency control; (12) concurrent attempts to record Approved evidence for one Revision must result in **at most one** committed Approved record.

**Recording operation (conceptually `record_customer_approval(artwork_revision, outcome, evidence_payload)`).** For an Approved outcome: authenticate; reject Guest; require the governed customer-approval-recording capability, applicable Role Permissions and Company User Permissions; acquire a current locking read on the Artwork Revision; validate exact Company and Sales Order context and Revision eligibility; derive `approved_revision_key` **on the server only**, never from client input; insert transactionally; rely on the **database UNIQUE constraint** for final race-safe enforcement; commit once; roll back fully on failure. For a Rejected outcome: the evidence remains historical, `approved_revision_key` is NULL, the applicable Revision lifecycle rules remain authoritative, and the record can never later be converted to Approved. No ordinary resource save or REST field update may fabricate an Approved outcome or its uniqueness key.

**Internal production-approval predicate (updated).** Before Submitted for Approval → Approved for Production, the controlled internal-approval operation must establish: exactly one Customer Approval Evidence record exists for the exact locked Revision with outcome Approved and `approved_revision_key == artwork_revision`; that record is not revoked; its Company and Sales Order match the Revision/Artwork; its Artwork Revision Link is the exact locked Revision; the evidence remains structurally valid; and no corrupt duplicate Approved evidence exists. Outcomes: **zero** Approved evidence → reject; **exactly one, unrevoked** → continue with all other checks (Section 6.2a, Section 6.2c); **exactly one, revoked** → reject; **more than one** (historical corruption) → **fail visibly and require governed remediation**. The operation must **never** silently select the newest, oldest, most-recently-modified, an unrevoked record from a duplicate group, or a client-chosen record.

**Database-conflict treatment.** A database duplicate-key conflict on `approved_revision_key` means customer approval has already been recorded as Approved for that Revision. It is translated into a stable domain conflict (conceptually `CustomerApprovalAlreadyApprovedForRevision`; exact class name not prescribed) without exposing SQL, table or index names. Uniqueness conflicts are never automatically retried, and no different record is silently loaded and returned in its place absent a separately approved future idempotency contract; no request-ID or idempotency field is introduced by this correction.

**Migration and historical safety.** Before creating the UNIQUE constraint: find every Approved Customer Approval Evidence record; group by exact Artwork Revision identity; detect every group containing more than one Approved record; **fail the migration visibly** where any duplicate group exists, with enough diagnostic identity information for governed remediation without exposing unnecessary customer-sensitive evidence. Migration must **never**: select a winner; auto-revoke duplicates; convert Approved to Rejected; clear outcomes or evidence references; reassign a Revision; fabricate timestamps or actors; populate `approved_revision_key` as unflagged historical proof (it must be recorded as migration-derived system data); or auto-delete duplicates. Only after duplicate remediation is explicitly governed may `approved_revision_key` be populated for every Approved record (NULL retained for Rejected), the UNIQUE constraint created, and absence of remaining duplicates verified.

**Direct-bypass guards.** Ordinary save, REST resource mutation, document method endpoints that bypass the governed operation, import, bulk edit, background direct assignment, Administrator field mutation and `ignore_permissions=True` must not assign or change `outcome`, `artwork_revision` after creation, `company`, `sales_order`, `approved_revision_key`, `revoked`, `revoked_by`, `revoked_on`, or `revocation_reason`. Raw SQL by a database administrator remains outside normal application guarantees.

**Distinction from active-key patterns (normative).** Revision `approved_key` and Set `approved_key` are **current production-approved/production-release authority**, cleared on withdrawal or supersession. Customer Approval Evidence `approved_revision_key` is a **permanent historical uniqueness claim** for the one Approved customer outcome, **retained after revocation**. These are deliberately different mechanisms and must not be conflated.

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

## 7.2 Set Consequence of Revision Supersession (Project Owner decision approved 2026-08-08)

This section resolves the previously open question of what happens to an already-Approved Production Artwork Set when one of its member Revisions is superseded by a newer internal production approval for the same Artwork.

Where a new Artwork Revision for the same Artwork is internally approved, the previously current Approved Revision moves Approved for Production → **Superseded**, **in the same governed transaction** as the new approval (Section 7.1). Where that previously current Revision is a member of a currently Approved Production Artwork Set for the same Sales Order, that same transaction additionally moves the Set Approved for Production → **Superseded** (Section 8.1) — **not Withdrawn** — and clears the Set's nullable `approved_key`. This is Set-level supersession triggered by member-Revision supersession; it is distinct from Set-level supersession triggered by approving a full replacement Set (Section 8.1), but produces the same Set state and the same downstream consequences.

**Consequences:**

- The superseded Set can never be used for a new Job Card release. A **new** Production Artwork Set — containing the newly Approved Revision and a valid Approved Revision for every other currently required Artwork — must be created and approved before any further release.
- The superseded Set is **never rewritten or modified in place**; its historical membership remains intact and immutable (Section 6.4).
- A Job Card already **Released** or **In Progress** against the now-Superseded Set is unaffected by this transition and follows the existing supersession rule (Section 16.1): Released may progress to In Progress, and In Progress may complete, while the Set remains merely Superseded and is not Withdrawn.
- **No Job Card is silently rebound** to any replacement Set (Section 16.2).
- This supersession path is **distinct** from revocation-driven or explicit withdrawal-driven Set withdrawal (Section 6.2c, Section 16.2), which move the Set to **Withdrawn**, not Superseded, and carry the stricter Withdrawn-state Job Card progression guards (Released → In Progress blocked; In Progress → Completed blocked; Section 16.2). **Superseded and Withdrawn remain distinct Set states with distinct Job Card consequences and must not be conflated.**

**Complete propagation — no silent winner selection.** The database constraints normally permit only **one** Approved Production Artwork Set per Sales Order (Section 6.3), and this rule does **not** authorize more than one. Where inconsistent data nonetheless presents more than one currently Approved Set containing the previous Revision, the newer-Revision approval operation must **detect every such Set** and either **supersede every affected Set within the same transaction**, or **fail visibly and roll the entire operation back** — leaving the newer Revision unapproved. It must **never** silently select one winner, process only the first or newest match, or leave partial propagation in place. This mirrors the equivalent guard on revocation propagation (Section 6.2c).

**Lock ordering (design-level, not runtime validated).** Consistent with the requirement-change and revocation-propagation baselines (Section 6.1a, Section 6.2c), this transaction's current locking reads follow the provisional order: (1) the Artwork Revision being approved; (2) the previously current Approved Revision for the same Artwork; (3) every currently Approved Production Artwork Set containing that previous Revision, sorted by record identity. This does not by itself close the global lock-order reconciliation required before Publication (Section 21.2, item 43) and is not claimed runtime validated.

This rule is covered by the existing runtime-validation gates for atomic revision and set supersession (Section 21.2, items 7–8); no new gate number is added by this decision.

---

# 8. Production Artwork Set State Model

### 8.1 Production Artwork Set Transition Matrix (corrected 2026-07-31 — ART-ARCH-F6)

| Current | Allowed transition | Result |
|---|---|---|
| Draft | Submit for Approval | Submitted for Approval |
| Submitted for Approval | Approve | Approved for Production |
| Submitted for Approval | Reject | Rejected |
| Approved for Production | Supersede through **replacement Set approval** **or member-Revision supersession (Section 7.2)** | Superseded |
| Approved for Production | Withdraw / revoke | Withdrawn |
| Rejected | None | Terminal |
| Superseded | None | Terminal |
| Withdrawn | None | Terminal |

**Only Approved for Production permits a new Registered → Released Job Card transition.**

Rules: at most one set may be Approved for Production for a Sales Order; approving a replacement set **supersedes the currently Approved set in the same transaction**; **Superseded Sets remain historical and may remain bound to Job Cards that were already Released before supersession, including those that later progress to In Progress or Completed; they cannot authorize a new Job Card release**, and no Job Card is silently rebound; withdrawn sets may not be used for new release; withdrawal requires actor, timestamp and reason; approved membership is immutable; **no silent in-place replacement is permitted**. **A requirement change (Section 6.1a) may also move an Approved Set to Withdrawn through its dedicated controlled operation, independent of a normal withdraw/revoke action.**

**Governed causes of each terminal authority state (normative summary).** An Approved Set becomes **Superseded** through either of two causes: (a) approval of a replacement Set for the same Sales Order; or (b) **member-Revision supersession — a newer Artwork Revision for a member Artwork is internally approved, superseding a Revision held by this Set (Section 7.2)**. An Approved Set becomes **Withdrawn** through any of: customer-approval revocation affecting a member Revision (Section 6.2c); explicit governed Set withdrawal; withdrawal of an approved member Revision (Section 7.1); or a governed `required_for_production` requirement change (Section 6.1a). **Neither Superseded nor Withdrawn permits a new Job Card release**, and in neither case is historical Set membership or an existing Job Card binding rewritten. The two states nonetheless remain distinct in their effect on **already-bound** Job Cards: a merely **Superseded** Set permits continued progression of Released and In Progress Job Cards (Section 16.1), whereas a **Withdrawn** Set blocks that progression under the governed withdrawal guards (Section 16.2).

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
- **Customer Approval Evidence:** outcome; customer identity or governed representative identity; decided-on timestamp; evidence source/reference; `approved_revision_key` (Section 6.2d); revocation metadata where present (corrected 2026-07-31 — ART-TRR2-ARCH-F1: **Section 6.2c**, not Section 6.2a).
- **Production Artwork Set:** `approved_by`; `approved_on`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`.
- **Requirement-change events** (Section 6.1a): Artwork; prior value; new value; actor; timestamp; reason. **The exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated** (Section 21).

Record identity carries immutable revision/set identity; explicit fields carry approval and withdrawal events; `modified` and `modified_by` describe **current record metadata only**; Version history is **supplemental when enabled**. **Version rows must not be assumed to be guaranteed authoritative audit storage.**

---

# 19. Migration and Historical Safety

Future-safe rules: do not fabricate historical approval evidence; demo-only releases must remain identifiable as demo-only; existing Job Cards without a Production Artwork Set reference must not be retro-labelled production-valid; detect approved revisions without hashes; detect approved revisions lacking valid Customer Approval Evidence; detect missing authoritative Files; detect duplicate revision labels within one Artwork; detect multiple Approved revisions per Artwork **before** creating the revision approved-key constraint; detect multiple Approved Production Artwork Sets per Sales Order **before** creating the set approved-key constraint; detect Submitted Sets whose completeness no longer matches current requirements (stale Sets) and require them to be rejected or replaced, never silently approved; prohibit silent winner selection; prohibit silent deletion or state changes; prohibit silent Job Card rebinding to a replacement Set; require explicit governed remediation; and **fail visibly** when unresolved invalid data remains.

**Revoked-evidence and duplicate-evidence inconsistency detection (corrected 2026-07-31 — ART-TRR2-ARCH-F2, mirroring the DocType Specification).** Migration must additionally detect, and never silently repair: **Artwork Revisions still marked Approved for Production where their authoritative Customer Approval Evidence has been revoked without a corresponding Revision withdrawal**; **Approved Production Artwork Sets containing a Revision whose authoritative evidence is revoked**; revoked Approved evidence lacking required revocation metadata; Approved evidence whose `approved_revision_key` does not equal its exact Artwork Revision identity; and, **before creating the `approved_revision_key` UNIQUE constraint** (Section 6.2d), every Artwork Revision with more than one historical Approved Customer Approval Evidence record, which must fail the migration visibly rather than be resolved by winner selection, auto-revocation, or outcome conversion.

---

# 20. Exclusions

This design excludes: Proof and Approval Record internal design, and Customer Approval Evidence's final technical name and full internal design beyond the minimum authority contract in Section 6.2a (all Artwork-internal, deferred, and subject to existing naming governance including AR-003, which is neither resolved nor modified); freelancer-sourced artwork (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; per-line-item Job Card decomposition; a multi-file child table on Artwork Revision (Section 6.1b); a mandatory maker-checker identity invariant (Section 11.1); an Artwork Hold Job Card state; an Expired approval state; any Tenant field; any ERPNext core modification; and all implementation detail.

---

# 21. Validation Scope

This document is **Approval, Version 1.1** (Project Owner Document Lifecycle Approval granted 2026-08-19; see Section 2), with **Approval, Version 1.0** (2026-07-31) as the prior historical baseline. It is **not** implementation-ready: none of the runtime-validation evidence below has been executed, and the Artwork production gate remains open. This Approval does **not** grant Publication, source-inspection authorization, implementation authorization, runtime-validation authorization, or production readiness. Earlier revisions of this section carried a stale unqualified "The Artwork design remains Draft" sentence that predated and contradicted the Approval 1.0 promotion; the current wording states the document's actual lifecycle position rather than restating that stale claim.

## 21.1 Active V1 Business Acceptance Criteria

The following ten business behaviors are the **active** Artwork Approval V1 acceptance scope. They are the compact, business-level restatement of the rules already recorded in full in Sections 6–9 and 16 above; they do not add a new rule.

1. A Revision cannot be internally approved without customer approval of that exact Revision (Section 6.2a).
2. Rejected customer approval cannot authorize production (Section 6.2a, Section 7).
3. Only one current Approved Revision exists per Artwork (Section 6.2, Section 7.1).
4. Approved Revision file identity and integrity cannot silently change (Section 10).
5. A Production Artwork Set cannot be approved when a required Artwork is missing (Section 9).
6. Approved Set membership cannot be edited (Section 6.4, Section 8.1).
7. A Job Card cannot be Released without an Approved Set (Section 14).
8. A Released Job Card's Set link cannot be silently changed (Section 13, Section 16).
9. Revoked approval prevents that Revision and relying Set from being used for new releases (Section 6.2c).
10. Approving a newer Revision makes the old current Revision **Superseded**, makes any currently Approved Set containing that old Revision **Superseded**, and requires a **new** Set for future releases (Section 7.2).

Concurrency, deadlock, retry, worker-loss, connection-loss and fault-injection protocols are **not** part of this active V1 acceptance scope. They remain later-phase reference material (Section 15, Section 21.2) to be developed only once source inspection and runtime-validation authorization exist.

## 21.2 Required Runtime Validation Gates (Later-Phase Reference)

Following the second narrow targeted re-review and the post-correction verification (both completed read-only, 2026-07-31; Section 2), **Architecture Review: Accepted with non-blocking corrections** and **Business Review: Accepted with non-blocking corrections**; **post-correction verification: Passed with non-blocking observations**; **no blocking review finding remains**. This accepted disposition does **not** grant Publication, does **not** authorize runtime validation, and does **not** authorize implementation. The following 62 runtime validations are required before the companion DocType Specification may be Published, and **none of the 62 items has been executed or passed** — this document does not and cannot self-certify runtime-validation outcomes. **The Artwork production gate remains open.** This list is **later-phase reference material**: it is not the active V1 business-acceptance scope (Section 21.1), and no further detailed experiment, checkpoint, phase, route, worker, lock, or fault-injection material should be built against it before source inspection and runtime-validation authorization exist.

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
45. First Approved Customer Approval Evidence succeeds for one Revision.
46. Second Approved evidence for the same Revision is rejected.
47. Concurrent Approved-evidence creation attempts commit at most one record.
48. The single-column `approved_revision_key` UNIQUE constraint is authoritative.
49. Rejected evidence keeps `approved_revision_key` NULL.
50. Revoked Approved evidence retains `approved_revision_key`.
51. Revocation does not permit another Approved record for the same Revision.
52. Internal production approval rejects zero Approved evidence records.
53. Internal production approval accepts exactly one valid unrevoked Approved record.
54. Internal production approval rejects one revoked Approved record.
55. Corrupt multiple Approved evidence fails visibly.
56. Historical duplicate detection runs before `approved_revision_key` constraint creation.
57. Migration performs no silent winner selection for duplicate Approved evidence.
58. Direct REST cannot assign `approved_revision_key`.
59. Administrator and `ignore_permissions=True` cannot bypass the uniqueness invariant.
60. The exact Artwork Revision Link identity is used with no trimming or case conversion.
61. Company/Sales Order mismatch is rejected independently of uniqueness.
62. Database duplicate-key errors on `approved_revision_key` are translated without exposing SQL or index details.

**None of items 25–62 is claimed to have passed. This correction closes no runtime-validation gate.**

---

# 22. Related Documents

- [../database/Artwork_Authority_DocType_Specification.md](../database/Artwork_Authority_DocType_Specification.md) — companion specification, targets Published.
- [05_Domain_Model.md](05_Domain_Model.md) — domain entities and cardinality.
- [06_Bounded_Contexts.md](06_Bounded_Contexts.md) — Artwork context ownership.
- [../database/Business_Entity_Inventory.md](../database/Business_Entity_Inventory.md) — entity inventory.
- [../database/ERPNext_DocType_Mapping.md](../database/ERPNext_DocType_Mapping.md) — implementation ownership mapping.
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — module dependency direction.
- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — the consuming Production design; currently **Approval, Version 1.5** (Project Owner Document Lifecycle Approval granted 2026-08-19, an independent decision from this document's own approval), prior historical baseline **Approval, Version 1.4**. Its Version 1.5 records the `production_artwork_set` reference contract reconciled from this document's Approval 1.0 baseline (Sections 13, 16.1, 16.2); this document's own Version 1.1 content (Section 7.2) is not relied upon by that contract.

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.1 | 2026-08-13 | Project Owner Decision — Bounded Documentation Reset (lifecycle treatment corrected 2026-08-13) | **Lifecycle treatment correction.** This entry was first written on 2026-08-08 retaining **Approval** status at Version 1.1. That was **incorrect** under `../Documentation_Workflow.md`: Section 5 defines Approval as "All required reviews passed; awaiting final Owner sign-off," Section 7 prohibits reaching Approval until every applicable review lens has explicitly passed, and Section 6/Section 11 keep Project Owner approval distinct from Architecture Review and Business Review. Because Section 7.2 below is **new normative content that has received no Architecture Review and no Business Review**, this document **returns to Draft at Version 1.1**; the **Approval, Version 1.0 baseline (2026-07-31) remains the last fully reviewed and Owner-approved version**, and every rule it contained is preserved unchanged. Workflow Section 8 independently confirms MINOR as the correct increment for an additive, non-contradictory expansion. Targeted Architecture Review and Business Review of Section 7.2 are **Pending**; promotion back to Approval requires those lenses plus renewed Project Owner lifecycle approval. No review completion is claimed or fabricated. **Content of this revision.** The Project Owner approved two business decisions, recorded as a Minor, additive version increment that reverses no existing rule: (1) **retention** of the governed six-state Production Artwork Set lifecycle (Section 8, Section 8.1) rather than replacing it with a simplified three-state lifecycle; (2) the **newer-Revision/Approved-Set supersession rule** (new Section 7.2): when a newer Artwork Revision is internally approved, the previous current Approved Revision becomes Superseded, and any currently Approved Production Artwork Set containing that previous Revision becomes Superseded (not Withdrawn) and unavailable for new Job Card releases; a new Set is required for future releases; the old Set is never rewritten; Released or In Progress Job Cards retain their historical Set link and may continue while the Set is merely Superseded; no Job Card is silently rebound; and customer-approval revocation or explicit withdrawal remains distinct, moving the affected Revision and Set to Withdrawn under the existing, stricter withdrawal progression guards (Section 6.2c, Section 16.2). **No new Architecture Review Register identifier was created; AR-003 remains unresolved.** Also corrected the stale Section 21 preamble, which incorrectly stated "The Artwork design remains Draft" despite this document's Approval status since Version 1.0 (2026-07-31); replaced Section 21's undifferentiated 62-item list as the sole validation reference with a new Section 21.1 (ten-behavior active V1 business-acceptance scope, restating existing rules with no new rule content) and Section 21.2 (the existing 62-item runtime-validation gate list, reclassified as later-phase reference material, unchanged in content and with no item claimed passed). Corrected the stale Master Index entry for this document from "Placeholder" to reflect its **actual lifecycle position — Draft, Version 1.1, prior approved baseline Approval, Version 1.0** (`docs/blueprint/00_Master_Index.md`, not otherwise modified in substance). Synchronized the companion DocType Specification's **three current-state cross-references** to this document (its Sections 2, 3 and 25) so that each records the **current document as Draft, Version 1.1** and the **prior approved baseline as Approval, Version 1.0**, with the Section 7.2 addition marked Pending targeted Architecture Review and Business Review (`docs/database/Artwork_Authority_DocType_Specification.md`). **No cross-reference identifies Draft, Version 1.1 as Approved.** This is a **documentation-only** change: no source was inspected or modified, no environment was created, no test or migration was run, and no runtime-validation gate is claimed passed by this revision. Publication is **not** granted; the Artwork production gate, all 62 runtime gates, Job Card Tier A implementation, and Implementation Authorization all remain exactly as recorded in Section 2 before this change. AR-003 was not resolved or modified. No Job Card Tier A document's substantive design content, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. **Bounded review corrections applied within this same Version 1.1 revision (2026-08-13).** Following the completed read-only bounded Architecture and Business Review of this package (Section 2 — Architecture Review: Accepted with non-blocking corrections; Business Review: Accepted with non-blocking corrections; plus one blocking and one non-blocking Documentation Governance defect), five corrections under local labels **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2** were applied here without a version increment, since Version 1.1 remains uncommitted: **LR-ARCH-1** — Section 8.1's transition matrix and Rules now name **member-Revision supersession** as a governed cause of Set supersession alongside replacement-Set approval, with a normative summary distinguishing the Superseded causes from the Withdrawn causes (revocation, explicit withdrawal, member-Revision withdrawal, requirement change), recording that neither terminal state permits new release or rewrites history, and that they differ only in their effect on already-bound Job Cards; **LR-ARCH-2** — Section 7.2 now requires the newer-Revision approval operation to detect **every** currently Approved Set containing the previous Revision and either supersede all of them in the same transaction or fail visibly and roll back entirely, never silently selecting a winner or leaving partial propagation, mirroring Section 6.2c and without authorizing more than one Approved Set per Sales Order; **LR-BIZ-1** — Section 21.1 behavior 10 now states all three consequences (old Revision Superseded, containing Approved Set Superseded, new Set required); **LR-GOV-1** — this entry's inaccurate claims that the Master Index was set to an "Approval status" and that "two cross-references" were moved to "Approval 1.1" are corrected above to the accurate lifecycle position and three synchronized cross-references; **LR-GOV-2** — Section 2's stale-status sentence now attributes the contradiction to the Approval 1.0 status held at that time. **Verification completed (2026-08-13).** An independent read-only post-correction verification has since been completed: **LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1 and LR-GOV-2 are Closed by verified correction**, with final results **Architecture Review — Accepted with non-blocking corrections (all identified corrections verified applied); Business Review — Accepted; Documentation Governance — Passed**, and **no material review finding remaining**. **LV-OBS-1 — local non-blocking wording observation (applied within this same Version 1.1 revision).** The Section 8.1 Rules sentence previously read "superseded sets remain historical and may remain bound to previously Released or later Job Cards." The phrase "or later Job Cards" was ambiguous: the governed meaning is Job Cards at later *lifecycle stages*, but it could be misread as Job Cards created *later*, which would have conflicted with the adjacent rule that no Superseded Set may authorize a new release. The sentence now states that Superseded Sets may remain bound to Job Cards already Released before supersession, including those that later progress to In Progress or Completed, that they cannot authorize a new Job Card release, and that no Job Card is silently rebound. **This clarifies existing behaviour and changes no business semantics** — the underlying rule is unchanged and is stated identically in Section 7.2, Section 16.1 and the Section 8.1 governed-causes summary. Withdrawn-state progression blocking is unaffected. **LV-OBS-1 is a local review label, not an Architecture Review Register identifier; it is Closed by the independent read-only verification completed 2026-08-13.** With that, **all six local Artwork findings — LR-ARCH-1, LR-ARCH-2, LR-BIZ-1, LR-GOV-1, LR-GOV-2 and LV-OBS-1 — are Closed, and no material Artwork review finding remains.** Review completion is **not** Project Owner lifecycle Approval; at that point this document remained **Draft, Version 1.1** pending a separate Owner decision. No approved V1 business decision was changed; no field, state, DocType, API, route, workflow or implementation mechanism was added; no runtime gate was closed; no document was promoted; AR-003 was not resolved and no AR identifier was created. **Project Owner Document Lifecycle Approval (2026-08-19).** With no material finding remaining, the Project Owner explicitly granted lifecycle Approval to this Version 1.1 on **2026-08-19**, promoting Status from Draft to **Approval** at the same Version 1.1 — no content, business rule, or normative design was altered by this promotion. **Approval, Version 1.0** (2026-07-31) is preserved as the prior historical baseline. This Approval accepts the current architecture and business-design baseline only; it grants **no** Publication, direct coding authority, source-inspection authorization, implementation authorization, environment creation, runtime-validation authorization, production authorization or readiness, closure of any of the 62 Artwork runtime gates, closure of the Artwork production gate, resolution of AR-003, approval of final technical names, approval of the `printos_core` module path, global lock-order resolution, or Full Architecture Freeze. The companion `database/Artwork_Authority_DocType_Specification.md` remains **Draft**, **not Published**, and **not safe for coding**. |
| 0.1 | 2026-07-31 | PrintHub Architecture Team | Initial Draft. Populates the previously empty Artwork Management blueprint placeholder with the Artwork production authority design, following the Project Owner's production-capable track selection (2026-07-30) and approval of the Artwork design defaults (2026-07-31), which authorized documentation-only Artwork authority design work as a prerequisite to Job Card Tier A Publication. Records: Artwork bounded-context ownership of the complete Artwork lifecycle, with Production consuming but never owning Artwork preparation, revision management, proofing, customer approval, production approval, supersession or withdrawal; the dependency direction Sales → Artwork → Production and the prohibited reverse dependency Artwork → Job Card; the Artwork aggregate root scoped to one Company and one Submitted Sales Order with an explicit `required_for_production` classification; **Artwork Revision as a standalone DocType, not a child table**, with its rationale; the Production Artwork Set as the final production-release authority consumed by the Job Card, with an immutable child-table membership (Production Artwork Set Item) binding each required Artwork to one exact approved revision; six-state models for both the revision and the set with only "Approved for Production" permitting release and **no Expired state** (Tier A approvals do not expire); the exact-identity-set completeness predicate covering every required Artwork; file-integrity treatment (private File owned by the revision, SHA-256 computed at submission and re-verified at approval, no in-place replacement of approved content, and release-time validation of File identity, existence, private status and recorded hash without a default full rehash); the single immutable `production_artwork_set` Job Card reference with no copied hashes, actors, timestamps, revision collections, Proof or Approval Record references and no Job Card child table; the Registered → Released contract with locking reads, Job-Card-then-Set lock ordering and single-commit rollback; supersession preserving existing bindings while blocking new releases; withdrawal blocking Released → In Progress and In Progress → Completed with operational alerting while never rewriting Completed history, **adding no Artwork Hold Job Card state**; separation of Artwork approval authority from Job Card release authority; the P-2 provisioning invariant applied to Artwork roles with no custom permission hook required by current evidence; explicit immutable audit evidence where native modified metadata is insufficient, with Version history supplemental only; migration and historical-safety rules prohibiting fabricated approval evidence and silent remediation; and 24 required runtime-validation gates, **none of which is claimed to have passed**. All technical DocType names are recorded as proposed pending governed naming treatment; the Naming Registry is not modified and AR-003 is neither resolved nor modified. Architecture Review and Business Review are **Pending**; Project Owner document lifecycle approval is **Not Granted**; Artwork product implementation, Job Card Tier A and Implementation Authorization all remain **Not Authorized / Not Granted**. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. |
| 1.0 | 2026-07-31 | Project Owner Document Lifecycle Approval | The Project Owner granted **Document Lifecycle Approval** to PrintHub Artwork Management and Production Authority, Version 0.5, on **2026-07-31**, following the completed read-only final cleanup verification (disposition **Final cleanup verification: Passed**), itself following the completed read-only post-correction verification (disposition **Passed with non-blocking observations**) and the second narrow targeted re-review (disposition **Architecture Review: Accepted with non-blocking corrections; Business Review: Accepted with non-blocking corrections**, no blocking finding remaining). This document transitioned from **Draft, Version 0.5 to Approval, Version 1.0** — the approved Artwork architecture and business-design baseline. This Approval is **design-authority acceptance only**: it does **not** publish the companion `database/Artwork_Authority_DocType_Specification.md`, does **not** make that Draft specification safe for coding, does **not** authorize or mark complete any of the 62 required runtime-validation items, does **not** close the Artwork production gate, does **not** authorize Artwork implementation or Job Card Tier A, does **not** grant Implementation Authorization, and does **not** achieve a Full Architecture Freeze. Every substantive Artwork design rule is preserved unchanged by this promotion: the dedicated Artwork bounded context; Artwork Revision as standalone authority; Production Artwork Set as final release authority; mandatory exact-Revision customer approval with no Tier A waiver; Customer Approval Evidence uniqueness and the permanent `approved_revision_key`; revocation propagation; immutable original approval evidence; the controlled `required_for_production` change operation; the non-empty Production Artwork Set requirement; the six-state Revision and Set lifecycles; the one-file-per-Revision boundary; the exact required-Artwork completeness predicate; the no-rebind and withdrawal progression guards; one active Job Card per Sales Order; Company/Sales-Order scoping with no Tenant field; the standard permission baseline with no unsupported hook; File as storage only with SHA-256 submission/approval-time validation and no default release-time full rehash; and the separation of Artwork approval from Job Card release capability. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.5 | 2026-07-31 | Post-Correction Verification Cross-Reference Cleanup | Recorded the completed read-only post-correction verification (2026-07-31; disposition **Post-correction verification: Passed with non-blocking observations**), which confirmed Customer Approval Evidence uniqueness, the `approved_revision_key` permanent uniqueness claim, uniqueness retention after revocation, and the database-constraint and migration contract, while identifying three non-blocking prose-staleness observations: ART-PCV-F1 (this document's own stale runtime-validation preamble), ART-PCV-F2 (the companion DocType Specification's stale citation of this document's version and review status), and ART-PCV-F3 (Documentation Status's stale Artwork-document version references). Applied **ART-PCV-F1**: corrected Section 21's preamble, which had never been updated since the original first-correction commit and still stated both Review dispositions "remain Corrections Required until a targeted re-review confirms the corrections" — this directly contradicted Section 2's correct, current "Accepted with non-blocking corrections" disposition. The preamble now states the current disposition, confirms no blocking finding remains, and preserves the correct substantive point that none of the 62 runtime-validation items has been executed or passed and the Artwork production gate remains open. ART-PCV-F2 and ART-PCV-F3 are companion-document corrections applied in the same commit to `database/Artwork_Authority_DocType_Specification.md` and `Documentation_Status.md` respectively. **Correction applied — closure pending final cleanup verification.** None of ART-PCV-F1 through ART-PCV-F3 is described as formally Closed by this revision. This is a bounded prose-cleanup correction only: no Artwork aggregate, lifecycle, approval, uniqueness, revocation, migration or Job Card rule was altered; the six-state Revision and Set models, the Customer Approval Evidence uniqueness rule (Section 6.2d), the revocation-propagation design (Section 6.2c), and all 62 runtime-validation gates are unchanged in substance. This correction and the Accepted-with-non-blocking-corrections disposition are **not** document lifecycle Approval, not Publication, not runtime-validation authorization, and not implementation authorization — this document remains Draft and is not promoted. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.4 | 2026-07-31 | Second Targeted Re-Review Corrections and Disposition Synchronization | Applied the Project Owner-approved Customer Approval Evidence uniqueness rule, completed the remaining non-blocking review corrections, and synchronized the accepted-with-non-blocking-corrections Architecture and Business Review outcomes. No lifecycle promotion or implementation authorization was granted. Recorded the second narrow targeted re-review (completed read-only, 2026-07-31), which found: **ART-TRR-BIZ-F1 Closed**; **ART-TRR-ARCH-F1 Closed**; **Architecture Review: Accepted with non-blocking corrections**; **Business Review: Accepted with non-blocking corrections**; residual non-blocking findings ART-TRR2-ARCH-F1, ART-TRR2-ARCH-F2, ART-TRR2-BIZ-F1. Added Section 6.2d, the Customer Approval Evidence uniqueness rule (Project Owner decision approved 2026-07-31, closing ART-TRR2-BIZ-F1): at most one historical Approved Customer Approval Evidence record per Artwork Revision, enforced by a proposed internal `approved_revision_key` field (server-derived, immutable, database UNIQUE, no Company/Sales-Order/Tenant in the key, no normalization/trimming/case-conversion) that **retains its value after revocation** — a deliberate, explicitly-distinguished contrast with the current-authority "active key" pattern used for Revision and Set `approved_key`, which are cleared on withdrawal/supersession. Specified the twelve uniqueness invariants; the `record_customer_approval` recording-operation contract relying on the database constraint for final race-safe enforcement; the updated internal production-approval predicate with explicit zero/one-unrevoked/one-revoked/corrupt-multiple outcomes and an explicit no-silent-selection rule; the `CustomerApprovalAlreadyApprovedForRevision`-equivalent database-conflict translation; and migration duplicate-detection requirements that fail visibly on any duplicate Approved-evidence group and never auto-select a winner, auto-revoke, or convert outcomes. Applied ART-TRR2-ARCH-F1 (Section 18 audit-model cross-reference corrected from Section 6.2a to **Section 6.2c**). Applied ART-TRR2-ARCH-F2 (Section 19 migration rules extended to mirror the DocType Specification's revoked-evidence and duplicate-evidence inconsistency detection). Extended Section 21's runtime-validation gate list with 18 new items (45–62) covering uniqueness creation, concurrency, revocation-retention, internal-approval-predicate outcomes, duplicate detection, bypass prevention, and database-conflict translation — **none claimed to have passed**. **No blocking review finding remains. Follow-up post-correction verification: Pending.** This correction and the Accepted-with-non-blocking-corrections disposition are **not** document lifecycle Approval, not Publication, and not implementation authorization — this document remains Draft and is not promoted. The six-state Artwork Revision and Production Artwork Set models, mandatory customer approval, revocation propagation, Job Card withdrawal behavior, the one-file Tier A boundary, and the no-rebind rule are all unchanged. No Job Card Tier A document, Business Entity Inventory, Module Dependency Matrix, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry; no AR identifier was invented; AR-003 was not resolved or modified. |
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
