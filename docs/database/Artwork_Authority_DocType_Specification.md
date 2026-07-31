# PrintHub Artwork Authority DocType Specification

## 1. Document Control

Version:
0.2

Status:
Draft

Date:
2026-07-31

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

**Governance status:** formal Architecture Review and Business Review conducted **2026-07-31**. **Architecture Review: Corrections Required. Business Review: Corrections Required.** Blocking findings: **ART-ARCH-F1** (incomplete `required_for_production` change control) and **ART-BIZ-F1** (undefined relationship between customer approval and internal production approval). Project Owner correction decisions approved **2026-07-31**; corrections are **applied in this revision** but **remain subject to targeted re-review** — neither disposition is changed to Accepted by this task. Project Owner document lifecycle approval **Not Granted**; Publication **Not Granted**; Implementation Authorization **Not Granted**.

**The Artwork production gate remains open** until this specification passes targeted re-review and its required runtime validations (Section 21) close.

---

## 3. Governing Decisions and Source Documents

- [../blueprint/18_Artwork_Management.md](../blueprint/18_Artwork_Management.md) — **Draft, Version 0.2**; the Artwork architecture/design authority for this specification (targets Approval; not yet approved; Architecture and Business Review both Corrections Required, corrections applied, targeted re-review pending).
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md) — domain entities and cardinality.
- [../blueprint/06_Bounded_Contexts.md](../blueprint/06_Bounded_Contexts.md) — Artwork context ownership.
- [Business_Entity_Inventory.md](Business_Entity_Inventory.md) — entity inventory.
- [ERPNext_DocType_Mapping.md](ERPNext_DocType_Mapping.md) — implementation ownership mapping.
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — module dependency direction.
- [../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) — Accepted; Tenant/Company distinction.
- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — Approval, Version 1.3; the consuming Production design (**not modified by this task**).

**Project Owner design defaults approved 2026-07-31.** That approval is an approved design input only — it is not Architecture Review, Business Review, document lifecycle Approval, Publication, or implementation authorization.

---

## 4. Naming Boundary

All technical DocType names below are **proposed working names only — pending governed naming treatment**:

| Business term (approved) | Proposed technical name — pending governed naming treatment |
|---|---|
| Artwork | `PrintHub Artwork` |
| Artwork Revision | `PrintHub Artwork Revision` |
| Production Artwork Set | `PrintHub Production Artwork Set` |
| — (set membership row) | `PrintHub Production Artwork Set Item` |

No claim is made that these names are approved by the Naming Registry. The Naming Registry is not modified by this specification, and AR-003 is neither resolved nor modified.

---

## 5. Aggregate Boundaries

| Concept | Structure | Role |
|---|---|---|
| Artwork | Aggregate root, standard DocType | Company- and Sales-Order-scoped artwork identity with production-requirement classification |
| Artwork Revision | **Standalone** aggregate, standard DocType — **not a child table** | The independently approvable unit carrying authoritative file evidence |
| Customer Approval Evidence *(normative working name pending naming treatment)* | **Standalone**, Artwork-internal aggregate, standard DocType | The durable, mandatory record of customer approval for one exact Artwork Revision (Section 7.1) |
| Production Artwork Set | Aggregate root, standard DocType | **Final production-release authority consumed by the Job Card** |
| Production Artwork Set Item | **Child table** of Production Artwork Set | Immutable membership value binding one Artwork to one exact Artwork Revision |

**Artwork Revision is standalone** because it is independently approvable, requires its own state, requires independent permissions, must be lockable, requires file-integrity evidence, may be referenced by set membership, may require unique database constraints, and must preserve immutable approval evidence.

**Production Artwork Set Item is the only child table introduced.** It is acceptable because its rows are immutable aggregate membership values — **it is not the approval authority itself.**

**The Job Card eventually stores one Production Artwork Set Link** (Section 15). It does not acquire an Artwork child table.

**File is storage only and is not approval authority.**

---

## 6. Proposed DocType — Artwork

Proposed technical name `PrintHub Artwork` — pending governed naming treatment. App-owned standard DocType in `printos_core`; exact module path unresolved (Section 22).

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

Proposed technical name `PrintHub Artwork Revision` — pending governed naming treatment. **Standalone standard DocType; explicitly not a child table.**

| Field | Type | Required | Unique | Hidden/internal | Read-only to clients | Importable | Notes |
|---|---|---|---|---|---|---|---|
| `artwork` | Link → proposed `PrintHub Artwork` | Yes | No | No | Yes after creation | No | Immutable |
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
| `superseded_by` | Link → proposed `PrintHub Artwork Revision` | On supersession | No | No | Yes | No | Records the replacement revision |

`modified`, `modified_by` and Version history **must not** be relied upon as the authoritative approval event, because later supersession or withdrawal changes the record. Version history is **supplemental only**.

### 7.1 Customer Approval Evidence Contract — Artwork-Internal (corrected 2026-07-31 — ART-BIZ-F1)

**Customer approval for the exact Artwork Revision is mandatory before internal production approval. There is no Tier A waiver.** Referred to normatively as **`Customer Approval Evidence`** until governed naming is complete; final technical naming remains pending governed naming treatment and the existing AR-003 naming question, which is **not** resolved or modified by this document.

**Minimum conceptual fields:**

| Field | Type | Notes |
|---|---|---|
| `artwork_revision` | Link → proposed `PrintHub Artwork Revision` | Required; immutable |
| `company` | Link → Company | Required; immutable; must match the Revision and Artwork |
| `sales_order` | Link → Sales Order | Required; immutable; must match the Revision's Artwork |
| `outcome` | Select (system-managed) | Approved or Rejected |
| `customer_identity` | Governed customer or representative identity reference | Required |
| `decided_on` | Datetime | Required |
| `evidence_reference` | Data (immutable evidence-source reference) | Required |
| `revoked` | Check (system-managed) | Where revocation is later supported |
| Revocation metadata | Governed, immutable where present | Actor, timestamp, reason |

This record is **Artwork-internal**. `Proof` is the customer-facing review artifact; **`Proof` is not approval authority**, and an Approved outcome **cannot be inferred from Proof status alone, nor from a file merely existing**. **Ordinary REST writes cannot fabricate approval.** The record is immutable after recording except through a governed revocation or correction process.

The controlled Artwork Revision approval operation (Submitted for Approval → Approved for Production) **must verify current valid Customer Approval Evidence for the exact locked Revision** before permitting the transition — see the updated transition requirement in Section 9. **Customer Approval Evidence is not added to the Job Card aggregate** (Section 15).

### 7.2 Bounded Reason-Field Rule

Applies to `rejected_reason`, `withdrawal_reason` (this section and Section 8), and the requirement-change reason (Section 6a): reject `None`, empty, or whitespace-only input; **reject leading or trailing whitespace rather than silently trimming it**; allow internal newline, carriage-return and tab characters; reject other C0 control characters; enforce a maximum of **500 Unicode code points**; **reject over-length input — never silently truncate**; store plain text only; escape on every rendered surface.

---

## 8. Proposed DocType — Production Artwork Set and Set Item

### 8.1 Production Artwork Set

Proposed technical name `PrintHub Production Artwork Set` — pending governed naming treatment. **The final production-release authority consumed by the Job Card.**

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
| `superseded_by` | Link → proposed `PrintHub Production Artwork Set` | On supersession | No | No | Yes | No | |

### 8.2 Production Artwork Set Item (Child Table)

Proposed technical name `PrintHub Production Artwork Set Item` — pending governed naming treatment.

| Field | Type | Required | Notes |
|---|---|---|---|
| `artwork` | Link → proposed `PrintHub Artwork` | Yes | |
| `artwork_revision` | Link → proposed `PrintHub Artwork Revision` | Yes | The exact approved revision |
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

**The Submitted for Approval → Approved for Production transition additionally requires:** frozen `artwork_file` and `file_sha256`; exact valid Customer Approval Evidence for the exact locked Revision; the internal production-approval capability; current locking reads; execution within one transaction; and immutable `approved_by`/`approved_on` written exactly once.

**No Expired state exists. Tier A approvals do not expire.** Withdrawing a revision requires any currently Approved set containing it to be withdrawn or invalidated **in the same governed transaction**.

---

## 10. Production Artwork Set State Model

### 10.1 Production Artwork Set Transition Matrix (corrected 2026-07-31 — ART-ARCH-F6)

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

Rules: at most one set may be Approved for Production per Sales Order; approving a replacement set **supersedes the currently Approved set in the same transaction**; superseded sets remain historical and may remain bound to previously Released or later Job Cards; withdrawn sets may not be used for new release; withdrawal requires actor, timestamp and reason; approved membership is immutable; **no silent in-place replacement is permitted**. **A requirement change (Section 6a) may also move an Approved Set to Withdrawn through its dedicated controlled operation**, independent of a normal withdraw/revoke action.

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
- **`revision_label`** — unique within one Artwork.
- **Set membership** — no duplicate Artwork within one set.
- Storage engine must support transactional row locking and the above constraints. Constraint behaviour is **not claimed validated** — see Section 21.

---

## 14. Controlled Operations

All operations are authenticated, **mutation-only**, capability-checked, take **current locking reads**, decide from **locked values**, perform **no early commit**, commit exactly once, and return stable domain errors with **no stack traces or SQL**. Desk, restricted API and background execution use the **same** server operations.

| Operation | Effect |
|---|---|
| Submit Artwork Revision for approval | Draft → Submitted; computes and stores SHA-256; freezes file identity and hash |
| Approve Artwork Revision | Submitted → Approved; **verifies current valid Customer Approval Evidence for the exact locked Revision (Section 7.1)**; **recomputes and compares SHA-256**; sets `approved_key`, `approved_by`, `approved_on`; supersedes the prior Approved revision atomically |
| Change Artwork production requirement | Updates `required_for_production`; **atomically moves the currently Approved Set (if any) to Withdrawn**; records durable audit evidence (Section 6a) |
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
| Type | Link → proposed `PrintHub Production Artwork Set` |
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

Ordinary REST field mutation and ordinary save **must not** be able to fabricate: approval state; file hash; approved key; approval actor or timestamp; set membership; withdrawal state; **`required_for_production` classification**; **Customer Approval Evidence outcome**; or Job Card release authority. System-managed fields must be rejected or restored to server-derived values before persistence. The guards apply to ordinary save, REST resource updates, standard document method endpoints, background execution, Administrator, and privileged execution. Database UNIQUE constraints remain independently authoritative. Raw SQL by a database administrator remains outside normal application guarantees.

---

## 21. Audit, Migration and Required Runtime Validation

### 21.1 Audit Fields

**Artwork Revision:** `approved_by`; `approved_on`; `file_sha256`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`. **Customer Approval Evidence:** `outcome`; `customer_identity`; `decided_on`; `evidence_reference`; revocation metadata where present (Section 7.1). **Production Artwork Set:** `approved_by`; `approved_on`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`. **Requirement-change events** (Section 6a): Artwork; prior value; new value; actor; timestamp; reason — **the exact technical persistence mechanism for repeated requirement-change audit events remains a pre-Publication design item and is not claimed runtime validated.**

Record identity carries immutable revision/set identity; explicit fields carry approval and withdrawal events; `modified` and `modified_by` describe **current record metadata only**; Version history is **supplemental when enabled** and **must not be assumed guaranteed authoritative audit storage**.

### 21.2 Migration Requirements

Do not fabricate historical approval evidence. Demo-only releases must remain identifiable as demo-only. Existing Job Cards without a Production Artwork Set reference must **not** be retro-labelled production-valid. Detect approved revisions without hashes; **detect approved revisions lacking valid Customer Approval Evidence**; missing authoritative Files; duplicate revision labels within one Artwork; multiple Approved revisions per Artwork **before** creating the revision approved-key constraint; multiple Approved sets per Sales Order **before** creating the set approved-key constraint; and **Submitted Sets whose completeness no longer matches current requirements (stale Sets), which must be rejected or replaced, never silently approved.** Prohibit silent winner selection, silent deletion, silent state changes, and **silent Job Card rebinding to a replacement Set**. Require explicit governed remediation. **Fail visibly** when unresolved invalid data remains.

### 21.3 Evidence Still Required — Runtime Validation

**No validation below is claimed to have passed, and this correction task closes no runtime-validation gate.** All remain outstanding:

1. Standalone Artwork Revision lifecycle and DocType/docstatus strategy. 2. Production Artwork Set lifecycle and DocType/docstatus strategy. 3. Nullable revision approved-key UNIQUE behaviour. 4. Nullable set approved-key UNIQUE behaviour. 5. Concurrent approval of competing revisions. 6. Concurrent approval of competing sets. 7. Atomic supersession of the previously Approved revision. 8. Atomic supersession of the previously Approved set. 9. Release-versus-withdrawal race. 10. Release-versus-set-supersession behaviour. 11. Lock ordering across Job Card and Production Artwork Set. 12. Deadlock and lock-wait treatment. 13. Two-hop private File access from Production users. 14. Approved File deletion prohibition. 15. Approved revision deletion prohibition. 16. Approved set deletion prohibition while referenced. 17. Hash computation and approval-time re-verification. 18. Server-side MIME/extension allow-list behaviour. 19. Company User Permission behaviour on Artwork records. 20. Administrator non-exemption from approval and release invariants. 21. Direct REST and privileged bypass prevention. 22. Post-release withdrawal progression guards. 23. Migration duplicate detection and constraint creation. 24. Production approval and Job Card release capability separation.

---

## 22. Pre-Publication Gate Checklist

- [ ] Architecture Review complete
- [ ] Business Review complete
- [ ] Project Owner lifecycle approval granted
- [ ] Proposed technical names governed
- [ ] Exact `printos_core` module paths selected
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
- [ ] Customer Approval Evidence technical name governed
- [ ] Customer Approval Evidence persistence contract finalized
- [ ] Customer-approval prerequisite runtime validated
- [ ] Requirement-change audit persistence selected
- [ ] Requirement-change and Set-withdrawal atomicity validated
- [ ] Zero-required-Artwork rejection validated
- [ ] Stale Submitted Set treatment validated
- [ ] No-rebind progression guards validated
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

---

## 24. Explicit Exclusions

Proof and Approval Record internal design, and Customer Approval Evidence's final technical name and full internal design beyond the minimum authority contract in Section 7.1 (all Artwork-internal, deferred, subject to existing naming governance including AR-003, which is neither resolved nor modified); freelancer-sourced artwork (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; per-line-item Job Card decomposition; a multi-file child table on Artwork Revision (Section 6.1b of the System Design); a mandatory maker-checker identity invariant; an Artwork Hold Job Card state; an Expired approval state; any Tenant field; any ERPNext core modification; and all implementation detail.

---

## 25. Review Status

- **Formal Architecture Review and Business Review:** conducted **2026-07-31**.
- **Architecture Review:** **Corrections Required.**
- **Business Review:** **Corrections Required.**
- **Blocking findings:** **ART-ARCH-F1**, **ART-BIZ-F1**.
- **Project Owner correction decisions:** approved **2026-07-31**; corrections **applied in this revision**; **targeted re-review pending** — neither disposition is changed to Accepted by this task.
- **Project Owner Document Lifecycle Approval:** **Not Granted**
- **Publication:** **Not Granted**
- **Implementation Authorization:** **Not Granted**
- **Direct coding reliance while Draft:** **Prohibited**

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-31 | PrintHub Architecture Team | Initial Draft Artwork Authority DocType Specification, subordinate to `../blueprint/18_Artwork_Management.md` (Draft 0.1), created following the Project Owner's approval of the Artwork design defaults on 2026-07-31 — an approved design input that authorized documentation-only design work and is not Architecture Review, Business Review, lifecycle Approval, Publication or implementation authorization. Defines four proposed DocTypes, all names recorded as **proposed pending governed naming treatment**: `PrintHub Artwork` (aggregate root, Company- and Submitted-Sales-Order-scoped, with an authoritative `required_for_production` classification), `PrintHub Artwork Revision` (**standalone standard DocType, explicitly not a child table**, carrying state, the single authoritative private File, an immutable `file_sha256`, a nullable UNIQUE `approved_key` enforcing at most one Approved revision per Artwork, and immutable approval, rejection, withdrawal and supersession evidence), `PrintHub Production Artwork Set` (**the final production-release authority consumed by the Job Card**, with a nullable UNIQUE `approved_key` enforcing at most one Approved set per Sales Order), and `PrintHub Production Artwork Set Item` (**the only child table introduced**, carrying immutable membership values binding each required Artwork to one exact approved revision, and explicitly not the approval authority). Records six-state models for both the revision and the set with only "Approved for Production" permitting a new Registered → Released transition, **no Expired state** because Tier A approvals do not expire, full mutability matrices, database constraints, the exact-identity-set completeness predicate (a count comparison is explicitly insufficient), the controlled-operation inventory, the Job Card reference contract (one immutable `production_artwork_set` Link, schema-optional but mandatory for release, with no copied hashes, actors, timestamps, revision collections, Proof or Approval Record references and no Job Card child table), the release-gate contract with locking reads and Job-Card-then-Set lock ordering, post-release supersession and withdrawal behaviour that preserves bindings and never rewrites Completed history while **adding no Artwork Hold Job Card state**, permission capabilities with normative separation of Artwork approval authority from Job Card release authority, file and attachment rules establishing that **File is storage only and not approval authority**, direct-bypass guards, audit fields where native modified metadata is insufficient, migration requirements prohibiting fabricated evidence and silent remediation, a 24-item outstanding runtime-validation list, a 30-item unchecked Pre-Publication Gate Checklist, and a permanent test inventory. **No runtime validation is claimed to have passed and the Artwork production gate remains open.** This document remains Draft, targets Published, and is **not safe for coding**; Architecture Review and Business Review are Pending; Project Owner lifecycle approval, Publication and Implementation Authorization are all Not Granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. |
| 0.2 | 2026-07-31 | Architecture and Business Review Correction | Applied the Project Owner-approved corrections (2026-07-31) to the formal combined Architecture Review (Disposition: **Corrections Required**, blocking finding **ART-ARCH-F1**) and Business Review (Disposition: **Corrections Required**, blocking finding **ART-BIZ-F1**), both dated 2026-07-31. Added Section 6a, a controlled `required_for_production` change operation closing ART-ARCH-F1: `required_for_production` marked internal, system-managed after creation, non-importable and non-bulk-editable; the operation requires authentication, Guest rejection, a dedicated production-requirement-management capability, applicable Role and Company User Permissions, a mandatory bounded reason, locking reads on the Artwork then the currently Approved Set (in that lock order), atomic movement of that Set to Withdrawn with durable audit evidence, single commit and full rollback; removed the undefined "governed out of scope" phrase. Added Section 7.1, the Customer Approval Evidence contract, closing ART-BIZ-F1: minimum conceptual fields (`artwork_revision`, `company`, `sales_order`, `outcome`, `customer_identity`, `decided_on`, `evidence_reference`, revocation metadata); recorded as Artwork-internal with final naming pending governed treatment and AR-003 (neither resolved nor modified); `Proof` explicitly not approval authority; ordinary REST writes cannot fabricate approval. Updated the Section 9 Revision transition matrix (new Section 9.1) to require exact, current, valid Customer Approval Evidence before Submitted for Approval → Approved for Production, and to record Rejected as explicitly terminal with no return to Draft, no resubmission and no Set membership (closing ART-ARCH-F4). Added Section 10.1, the complete Production Artwork Set transition matrix (closing ART-ARCH-F6). Updated Section 11's completeness predicate to require at least one required Artwork before a Set may be submitted or approved, prohibiting empty-membership Sets and zero-required-Artwork Sales Orders with no Tier A exception (closing ART-ARCH-F3), and to validate Customer Approval Evidence for every selected Revision. Added Section 11.1 recording that Set approval is not a second content review but the act of freezing a verified combination of already-approved Revisions (closing ART-BIZ-F4). Added Section 7.2, the bounded reason-field rule (500 Unicode code points, reject-not-truncate, no surrounding whitespace), applied to `rejected_reason`, `withdrawal_reason` and the requirement-change reason (closing ART-ARCH-F7). Corrected Section 17 to remove the undefined In Progress "governed correction procedure," requiring instead that Released and In Progress Job Cards whose bound Set is Withdrawn use controlled `discard()` with mandatory terminal reason and a replacement Job Card, that `production_artwork_set` is never modified after Release, and that no silent rebinding occurs (closing ART-ARCH-F5). Updated Section 18 to add the production-requirement-management and customer-approval-recording capabilities and a Maker-Checker Treatment subsection recording that Tier A does not require distinct submitter/approver identities while Artwork-approval and Job-Card-release authority remain normatively separate (closing ART-BIZ-F3). Extended Section 20's direct-bypass guards to cover `required_for_production` and Customer Approval Evidence. Extended Section 21's audit fields and migration requirements with Customer Approval Evidence, requirement-change audit evidence, and detection of stale Submitted Sets. Added 18 new permanent tests (CA-1 through CA-4, RC-1 through RC-5, Z-1 through Z-2, RJ-1 through RJ-2, NR-1 through NR-3, MC-1, SO-1) to Section 23. Added 8 new unchecked items to the Section 22 Pre-Publication Gate Checklist for Customer Approval Evidence naming and persistence, the customer-approval runtime prerequisite, requirement-change audit persistence, requirement-change/Set-withdrawal atomicity, zero-required-Artwork rejection, stale-Set treatment, and no-rebind progression guards — **none marked complete**. Updated Section 24 to record the deferred multi-file child table and mandatory maker-checker identity exclusions. Updated Section 2 and Section 25 to record the formal review date, both Corrections Required dispositions, the two blocking findings, and that corrections are applied in this revision but **remain subject to targeted re-review** — this task does not and cannot change either disposition to Accepted, mark any runtime-validation gate closed, or close the Artwork production gate. All technical DocType names, including the working name `Customer Approval Evidence`, remain marked proposed pending governed naming treatment; AR-003 is neither resolved nor modified. No approved Project Owner design decision was changed. This document remains Draft, targets Published, and remains **not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis or standards document was modified by this entry. |

---

## Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
