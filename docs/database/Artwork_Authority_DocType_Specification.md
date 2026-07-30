# PrintHub Artwork Authority DocType Specification

## 1. Document Control

Version:
0.1

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

**Governance status:** Architecture Review **Pending**; Business Review **Pending**; Project Owner document lifecycle approval **Not Granted**; Publication **Not Granted**; Implementation Authorization **Not Granted**.

**The Artwork production gate remains open** until this specification is reviewed and its required runtime validations (Section 21) close.

---

## 3. Governing Decisions and Source Documents

- [../blueprint/18_Artwork_Management.md](../blueprint/18_Artwork_Management.md) — **Draft, Version 0.1**; the Artwork architecture/design authority for this specification (targets Approval; not yet approved).
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
| `required_for_production` | Check | Yes (default 1) | No | No | Governed operation only | No | **Authoritative indicator used when calculating set completeness** |

**Rules.** Company must equal the Sales Order Company. Cross-Company reuse is prohibited. Tenant identity remains implicit at the site/database level; **no Tenant field**. A required Artwork must be represented in an approved Production Artwork Set before Job Card release. Optional Artwork does not block release unless later marked required through a governed Artwork operation. Changes to production requirements after an approved set exists require a **new** Production Artwork Set.

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
| `rejected_reason` | Small Text (bounded plain text) | On rejection | No | No | Yes | No | |
| `withdrawn_by` | Link → User | On withdrawal | No | No | Yes | No | |
| `withdrawn_on` | Datetime | On withdrawal | No | No | Yes | No | |
| `withdrawal_reason` | Small Text (bounded plain text) | **Mandatory for withdrawal** | No | No | Yes | No | |
| `superseded_by` | Link → proposed `PrintHub Artwork Revision` | On supersession | No | No | Yes | No | Records the replacement revision |

`modified`, `modified_by` and Version history **must not** be relied upon as the authoritative approval event, because later supersession or withdrawal changes the record. Version history is **supplemental only**.

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
| `withdrawal_reason` | Small Text (bounded plain text) | **Mandatory for withdrawal** | No | No | Yes | No | |
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

| From | To | Mechanism | Authority |
|---|---|---|---|
| Draft | Submitted for Approval | Controlled submit operation | Submit-for-approval capability |
| Submitted for Approval | Approved for Production | Controlled approve operation | **Approve-revision capability** |
| Submitted for Approval | Rejected | Controlled reject operation | Reject capability |
| Approved for Production | Superseded | System consequence of a later approval, same transaction | — |
| Approved for Production | Withdrawn | Controlled withdraw operation with mandatory reason | Withdraw/revoke capability |

**No Expired state exists. Tier A approvals do not expire.** Withdrawing a revision requires any currently Approved set containing it to be withdrawn or invalidated **in the same governed transaction**.

---

## 10. Production Artwork Set State Model

States: **Draft**, **Submitted for Approval**, **Approved for Production**, **Rejected**, **Superseded**, **Withdrawn**.

**Only Approved for Production permits a new Registered → Released Job Card transition.**

Rules: at most one set may be Approved for Production per Sales Order; approving a replacement set **supersedes the currently Approved set in the same transaction**; superseded sets remain historical and may remain bound to previously Released or later Job Cards; withdrawn sets may not be used for new release; withdrawal requires actor, timestamp and reason; approved membership is immutable; **no silent in-place replacement is permitted**.

---

## 11. Completeness Predicate (Set Approval)

Evaluated at Production Artwork Set approval time:

1. Load the Sales Order and Company.
2. Determine the complete current set of Artwork where `sales_order` equals the set Sales Order, `company` equals the set Company, `required_for_production` is true, and the Artwork has not been governed out of scope through an explicit Artwork-domain operation.
3. Compare that authoritative required-Artwork set with the set-item Artwork membership.
4. Require **exact equality** — no missing required Artwork; no duplicate Artwork; no unrelated Artwork; no wrong Sales Order; no wrong Company.
5. Validate every selected revision: belongs to the corresponding Artwork; state is Approved for Production; authoritative private File exists; stored SHA-256 exists; file identity is immutable; not Withdrawn; not Superseded at the authoritative decision point.
6. Approve only when every condition passes.

**A mere count comparison is insufficient — exact identity-set comparison is required.**

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
| Approve Artwork Revision | Submitted → Approved; **recomputes and compares SHA-256**; sets `approved_key`, `approved_by`, `approved_on`; supersedes the prior Approved revision atomically |
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

**Withdrawal** — Released: **Released → In Progress blocked**, operational alert required, correction via the existing controlled Discard path plus a replacement Job Card. In Progress: **In Progress → Completed blocked**, operational alert required, resolution requires an approved replacement set and a governed correction procedure or controlled termination; **the Job Card must not be silently rebound**. Completed: the historical production record **remains unchanged**; withdrawal does not rewrite history; follow-up may result in controlled Cancel/Void with reason.

**No Artwork Hold Job Card state is added.**

---

## 18. Permission Specification

**Artwork capabilities:** create Artwork draft; edit Artwork draft; create Artwork Revision; upload or replace Draft revision file; submit revision for approval; **approve revision for production**; reject revision; create Production Artwork Set; submit set for approval; **approve set for production**; reject set; **withdraw or revoke**; view/download approved Artwork; administer exceptional corrections.

**Production capability:** release Job Card against an approved Production Artwork Set.

**Separation of duties is normative:** Artwork approval authority is **distinct** from Job Card release authority. Holding release authority does **not** grant approval authority; holding approval authority does **not** automatically grant release authority.

Standard Role Permissions and Company User Permissions remain the baseline. The **P-2 provisioning invariant applies to Artwork operational roles** — a role-bearing user with **zero Company User Permissions is an invalid provisioned state**. **No `has_permission` hook** and **no `permission_query_conditions` hook** is required by current evidence; either requires a future reproduced gap plus a controlled design change. **Administrator is privileged for permission purposes but cannot bypass production-approval domain invariants.** Tenant isolation remains site/database based; **no Tenant field**.

---

## 19. File and Attachment Rules

The Artwork Revision **owns the authoritative File**, which **must be private**. **Attachment existence alone does not mean approval.** Upload uses standard Frappe attachment handling. Upload and replacement are permitted **only while the revision is Draft**. Submission computes and stores SHA-256; approval **recomputes and compares** it, and a mismatch rejects approval. Approved content **cannot be replaced in place** — changed content requires a **new revision**. Approved or ever-approved evidence **must not be silently deleted**. Thumbnails and previews are **convenience-only**. MIME and extension restrictions require a **server-side allow-list**. **Malware scanning remains a separate platform production-readiness control.** Job Card users receive **read/download only** where authorized, and **no** Artwork upload, replacement, approval, withdrawal or deletion capability.

**File is storage only and is not the approval authority.**

---

## 20. Direct-Bypass Guards

Ordinary REST field mutation and ordinary save **must not** be able to fabricate: approval state; file hash; approved key; approval actor or timestamp; set membership; withdrawal state; or Job Card release authority. System-managed fields must be rejected or restored to server-derived values before persistence. The guards apply to ordinary save, REST resource updates, standard document method endpoints, background execution, Administrator, and privileged execution. Database UNIQUE constraints remain independently authoritative. Raw SQL by a database administrator remains outside normal application guarantees.

---

## 21. Audit, Migration and Required Runtime Validation

### 21.1 Audit Fields

**Artwork Revision:** `approved_by`; `approved_on`; `file_sha256`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`. **Production Artwork Set:** `approved_by`; `approved_on`; `withdrawn_by`; `withdrawn_on`; `withdrawal_reason`; `superseded_by`.

Record identity carries immutable revision/set identity; explicit fields carry approval and withdrawal events; `modified` and `modified_by` describe **current record metadata only**; Version history is **supplemental when enabled** and **must not be assumed guaranteed authoritative audit storage**.

### 21.2 Migration Requirements

Do not fabricate historical approval evidence. Demo-only releases must remain identifiable as demo-only. Existing Job Cards without a Production Artwork Set reference must **not** be retro-labelled production-valid. Detect approved revisions without hashes; missing authoritative Files; duplicate revision labels within one Artwork; multiple Approved revisions per Artwork **before** creating the revision approved-key constraint; and multiple Approved sets per Sales Order **before** creating the set approved-key constraint. Prohibit silent winner selection, silent deletion and silent state changes. Require explicit governed remediation. **Fail visibly** when unresolved invalid data remains.

### 21.3 Evidence Still Required — Runtime Validation

**No validation below is claimed to have passed.** All remain outstanding:

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
- [ ] Artwork production gate closed
- [ ] Documentation references synchronized
- [ ] Publication granted

**No item above is complete merely because the design is now documented.**

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

---

## 24. Explicit Exclusions

Proof and Approval Record internal design (Artwork-internal, deferred, subject to existing naming governance); freelancer-sourced artwork (Phase 2); Job Card Tier B scope; machine, material, costing, quantity, scheduling and time facts; per-line-item Job Card decomposition; an Artwork Hold Job Card state; an Expired approval state; any Tenant field; any ERPNext core modification; and all implementation detail.

---

## 25. Review Status

- **Architecture Review:** **Pending**
- **Business Review:** **Pending**
- **Project Owner Document Lifecycle Approval:** **Not Granted**
- **Publication:** **Not Granted**
- **Implementation Authorization:** **Not Granted**
- **Direct coding reliance while Draft:** **Prohibited**

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-31 | PrintHub Architecture Team | Initial Draft Artwork Authority DocType Specification, subordinate to `../blueprint/18_Artwork_Management.md` (Draft 0.1), created following the Project Owner's approval of the Artwork design defaults on 2026-07-31 — an approved design input that authorized documentation-only design work and is not Architecture Review, Business Review, lifecycle Approval, Publication or implementation authorization. Defines four proposed DocTypes, all names recorded as **proposed pending governed naming treatment**: `PrintHub Artwork` (aggregate root, Company- and Submitted-Sales-Order-scoped, with an authoritative `required_for_production` classification), `PrintHub Artwork Revision` (**standalone standard DocType, explicitly not a child table**, carrying state, the single authoritative private File, an immutable `file_sha256`, a nullable UNIQUE `approved_key` enforcing at most one Approved revision per Artwork, and immutable approval, rejection, withdrawal and supersession evidence), `PrintHub Production Artwork Set` (**the final production-release authority consumed by the Job Card**, with a nullable UNIQUE `approved_key` enforcing at most one Approved set per Sales Order), and `PrintHub Production Artwork Set Item` (**the only child table introduced**, carrying immutable membership values binding each required Artwork to one exact approved revision, and explicitly not the approval authority). Records six-state models for both the revision and the set with only "Approved for Production" permitting a new Registered → Released transition, **no Expired state** because Tier A approvals do not expire, full mutability matrices, database constraints, the exact-identity-set completeness predicate (a count comparison is explicitly insufficient), the controlled-operation inventory, the Job Card reference contract (one immutable `production_artwork_set` Link, schema-optional but mandatory for release, with no copied hashes, actors, timestamps, revision collections, Proof or Approval Record references and no Job Card child table), the release-gate contract with locking reads and Job-Card-then-Set lock ordering, post-release supersession and withdrawal behaviour that preserves bindings and never rewrites Completed history while **adding no Artwork Hold Job Card state**, permission capabilities with normative separation of Artwork approval authority from Job Card release authority, file and attachment rules establishing that **File is storage only and not approval authority**, direct-bypass guards, audit fields where native modified metadata is insufficient, migration requirements prohibiting fabricated evidence and silent remediation, a 24-item outstanding runtime-validation list, a 30-item unchecked Pre-Publication Gate Checklist, and a permanent test inventory. **No runtime validation is claimed to have passed and the Artwork production gate remains open.** This document remains Draft, targets Published, and is **not safe for coding**; Architecture Review and Business Review are Pending; Project Owner lifecycle approval, Publication and Implementation Authorization are all Not Granted. No Job Card Tier A document, Architecture Review Register item, ADR, Architecture Freeze, Development Roadmap, Naming Registry, Fit Analysis, Gap Analysis, standards document, product code or configuration was modified. |

---

## Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] No implementation code included
- [ ] Reviewed by Project Owner
