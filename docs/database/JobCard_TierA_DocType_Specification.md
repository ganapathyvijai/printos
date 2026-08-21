# PrintHub Job Card Tier A — DocType Specification

## 1. Document Control

Version:
0.8

Status:
Draft

Date:
2026-08-19

Owner:
PrintHub Architecture Team

Target Lifecycle:
Published

---

## 2. Specification Authority and Coding Boundary

This document is intended to become a **direct coding specification only after Publication.** While Draft:

- it is **not safe for coding**;
- **no implementation authorization exists**;
- the unresolved pre-Publication gates listed in Section 24 must remain visible and must not be silently treated as closed.

---

## 3. Governing Decisions and Source Documents

- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — currently **Approval, Version 1.5** (Project Owner Document Lifecycle Approval granted **2026-08-19**, following a dedicated Version 1.5 Architecture Review — Accepted with non-blocking corrections — and Business Review — Accepted — with all six local Job Card findings Closed); prior historical baseline **Approval, Version 1.4** (granted 2026-07-29; Version 1.1 records the 2026-07-30 P-2 closure, Version 1.2 the 2026-07-30 race-safe uniqueness closure, Version 1.3 the 2026-07-30 terminal-reason persistence closure, and Version 1.4 the 2026-07-31 bounded reference-only correction). **Version 1.5** contains the Production Artwork Set reference reconciliation (its Sections 9 and 10.3), now approved as this document's current architecture/design authority. This DocType Specification is **not** made Published or safe for coding by that approval, and **no implementation, publication, or runtime-validation authority follows**; the remaining pre-Publication gates (Section 24) stay open.
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) — Draft — Open Register, Version 0.4.
- [../decisions/ADR-001-ERPNext-Framework.md](../decisions/ADR-001-ERPNext-Framework.md) — Accepted.
- [../decisions/ADR-014-Production-Terminology.md](../decisions/ADR-014-Production-Terminology.md) — Accepted.
- [../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) — Accepted, Version 1.0.
- [../implementation/Architecture_Freeze.md](../implementation/Architecture_Freeze.md) — Approval, Version 1.2.
- [../roadmap/01_Development_Roadmap.md](../roadmap/01_Development_Roadmap.md) — Approval, Version 1.2.
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — Draft, Version 0.5.

Official v16 evidence pins: `frappe/frappe@06613fc60b44d5736007ae3107cdab029b2ae045` (`version-16`, v16.29.0); `frappe/erpnext@a5de60c357d531cb31da093f0b86301776965173` (`version-16`, v16.29.0). Accepted conclusions from that evidence are as recorded in `JobCard_TierA_System_Design.md` Section 5 and are not restated in full here.

---

## 4. Project Owner Design Decision Baseline

The Project Owner approved the Job Card Tier A design defaults (decision set O-1 through O-27) on **2026-07-29**. This approval authorizes documentation drafting only and does not authorize `printos_core` scaffolding, application code, DocType creation, hooks, fixtures, migrations, site provisioning, prototype execution, Publication, implementation, or production use — see `JobCard_TierA_System_Design.md` Section 4 for the full boundary statement, which applies equally to this document.

---

## 5. Technical DocType Identity

- **Technical name:** `PrintHub Job Card`.
- **Business term:** `Job Card` (unchanged; the technical name is distinct to avoid collision with ERPNext's own `Job Card`).
- **Owning custom app:** `printos_core`.
- ERPNext's Manufacturing `Job Card` is **not** reused, per Accepted ADR-014.
- **App module:** delegated technical decision, to be resolved before Publication.
- **The `printos_core` custom app skeleton does not yet exist** in this repository.
- **Implementation remains unauthorized.**

---

## 6. DocType-Level Properties (Proposed Draft)

- App-owned standard DocType (not a Single, not a virtual DocType).
- Submittable: **Yes**.
- Track Changes: **Yes**.
- No child table.
- No Tenant field.
- No ERPNext core customization.
- No Property Setter requirement identified.
- No Custom Field requirement on any ERPNext DocType identified.

Properties for which evidence or Owner approval is absent are **not** finalized here (e.g., exact naming-series pattern, exact module name — both delegated, Section 14/24).

---

## 7. Status and `docstatus` Mapping

| Business Status | `docstatus` | Entry Mechanism |
|---|---:|---|
| Registered | 0 | Insert |
| Released | 0 | Controlled server transition |
| In Progress | 0 | Controlled server transition |
| Completed | 1 | Submit |
| Discarded | 2 | Controlled action invoking `discard()` |
| Voided | 2 | Controlled action invoking `cancel()` |

The `status` field is **system-managed** — not directly user-editable outside the controlled transition actions.

---

## 8. Permitted Transition Matrix

| From | To | Mechanism |
|---|---|---|
| Registered | Released | Controlled server transition |
| Released | In Progress | Controlled server transition |
| In Progress | Completed | Submit |
| Registered | Discarded | Controlled discard (with reason) |
| Released | Discarded | Controlled discard (with reason) |
| In Progress | Discarded | Controlled discard (with reason) |
| Completed | Voided | Controlled cancel (with reason) |

No other transition is permitted. No reopening of Discarded or Voided is defined for Tier A.

### 8.1 Status/`docstatus` Consistency Invariant (Finding F-1)

Per `JobCard_TierA_System_Design.md` Section 12.1, the following is **normative**: no Desk, REST, internal API, background-job, or ordinary document-save operation may produce a `status` value inconsistent with `docstatus`. Specifically:

- **Insert:** a new record must begin as `Registered` (`docstatus = 0`); direct insert as any other status is rejected.
- **Ordinary Draft save:** permitted only for no-status-change saves, or Registered→Released and Released→In Progress via their controlled transitions; direct assignment to Completed, Discarded, or Voided, skipped transitions, backward transitions, and unsupported status values are all rejected.
- **Submit:** permitted only from In Progress; must result in `status = Completed` and `docstatus = 1`; a record must not remain In Progress after a successful submit, and must not be Submitted from Registered or Released.
- **Discard:** `discard()` permitted only from Registered, Released, or In Progress; the controlled action must establish the terminal reason, `status = Discarded`, and final `docstatus = 2`.
- **Cancel:** `cancel()` permitted only from Completed; the controlled action must establish the terminal reason, `status = Voided`, and final `docstatus = 2`.
- `status` is system-managed; a client-side read-only or hidden field is **not** sufficient — server-side validation (controller `validate()`/transition guard) is mandatory regardless of caller (Desk, REST, background context, or privileged user).

The exact controller-method distribution implementing this invariant remains delegated (Section 14), but the invariant itself is normative and testable — see Section 21, tests T-29 through T-36.

---

## 9. Proposed Field Specification

### Required

| Field | Type | Options | Notes |
|---|---|---|---|
| `company` | Link | Company | Required; editable only while Registered; must equal `sales_order`'s Company |
| `sales_order` | Link | Sales Order | Required; Submitted Sales Orders only (`docstatus = 1`); editable only while Registered |
| `status` | Select | Registered / Released / In Progress / Completed / Discarded / Voided | System-managed |
| `terminal_reason` | **Small Text (confirmed 2026-07-30 — Finding F-3 closed)** | Database intent: **`TEXT NULL`** | **Authoritative terminal explanation.** Mandatory only for a successful controlled discard/cancel (nullable because active records have no terminal reason). **Plain text only** — never HTML or rich text; escaped on every rendered surface. **Application maximum 500 Unicode code points**, rejected before lifecycle mutation, **never silently truncated**. Read-only outside the controlled action; visible after termination; **immutable after successful termination**; not directly editable; **non-importable**; excluded from bulk edit; **no database index**. Accepted only as a controlled-action argument and persisted by the server (Section 15) |
| `terminal_request_id` | Data | Database intent: **`VARCHAR(140) NULL`**, **UNIQUE: Yes** (single-column unique index) | **Terminal request correlation.** Application format: **canonical lowercase UUIDv4, exactly 36 characters**, standard hyphenated representation, supplied by the caller before execution. **Mandatory for every controlled terminal action**; blank on active records; unique across PrintHub Job Card terminal actions within the site; **immutable**, written exactly once with successful termination. Client-visible for correlation where appropriate; **not accepted through ordinary document REST writes or any document-field mutation** — only as a controlled-action argument; **non-importable**; excluded from bulk edit (Section 15) |

### Internal / System-Managed

| Field | Type | Database intent | Notes |
|---|---|---|---|
| `active_key` | Data | `VARCHAR(140) NULL`, **Unique: Yes** (full-column single-column UNIQUE index, InnoDB) | **Internal, system-managed, server-derived active claim** implementing the exactly-one-active invariant (Section 13). **Nullable: Yes. Required: No at schema level**, because terminal records require NULL. **Hidden/internal: Yes. Read-only to clients: Yes. Importable: No.** Set **only** by the server invariant. **Active value:** the exact validated `sales_order` Link identity. **Terminal value:** NULL. This is **not an independently meaningful business field** and carries no business semantics of its own. |

### Optional

| Field | Type | Options | Notes |
|---|---|---|---|
| `description` | Small Text | — | User-entered; editable while Draft (`docstatus = 0`), subject to lifecycle rules (Section 10) |

### Framework Metadata (automatic)

`name`, `owner`, `creation`, `modified`, `modified_by`, `docstatus`.

### Explicitly Not Included

Tenant; Artwork Link; Customer copy; Machine; Workstation; Operation; BOM; material; cost; quantity; time log; schedule; Quotation; Estimation; Enquiry; global Customer ID; replacement-link field.

---

## 10. Field Mutability Matrix

| Field | Registered | Released | In Progress | Completed | Discarded/Voided |
|---|---|---|---|---|---|
| `company` | Mutable | Locked | Locked | Locked | Locked |
| `sales_order` | Mutable | Locked | Locked | Locked | Locked |
| `description` | Mutable | Mutable | Mutable | Locked (native, submitted) | Locked |
| `status` | System-managed transition only | Same | Same | Same | Terminal |
| `terminal_reason` | N/A | N/A | N/A | N/A | Written only by the controlled terminal action; immutable afterward |
| `terminal_request_id` | N/A | N/A | N/A | N/A | Written only by the controlled terminal action; immutable afterward |
| `active_key` | Server-derived | Server-derived | Server-derived | Server-derived | Released to NULL by the terminal action |

Submitted and terminal records are immutable except for framework-permitted metadata (e.g., comments, attachments per Section 18).

**Terminal metadata mutability is not user-controlled at any lifecycle stage.** `terminal_reason` and `terminal_request_id` are accepted **only** as arguments to the controlled terminal action (Section 15) and are never accepted as client-supplied document-field values. Ordinary persistence must **reject or restore** any unsupported mutation of the terminal reason, terminal request ID, terminal status or active key, across ordinary save, REST resource updates, standard document method endpoints, `frappe.client.cancel`, direct `discard()`/`cancel()`, background code, Administrator and `ignore_permissions=True`. Ordinary `validate()` remains necessary for non-terminal saves and field immutability but is **not sufficient** for terminal enforcement, because discard and cancel do not use it as the mandatory-reason enforcement point (Section 15.1). Raw SQL by a database administrator remains outside normal application guarantees.

**No separate `terminal_action` field is added.** The persisted terminal status already identifies the successful action — **Discarded means discard; Voided means cancel** — and idempotent replay compares the requested action against the persisted terminal status. This avoids redundant state and preserves the minimal Tier A aggregate.

**No dedicated terminal-actor or terminal-timestamp fields are added.** The terminal actor is **`modified_by`** and the terminal timestamp is **`modified`**. Version history is **supplemental only where tracking is enabled** and must not be treated as guaranteed authoritative storage.

**`active_key` mutability is not user-controlled at any lifecycle stage.** It is excluded from ordinary client editing, editable REST input, import and bulk editing. Any client-supplied value — including through ordinary save, direct REST field mutation, background execution, or Administrator/privileged server execution — must be **rejected, or overwritten by the server-derived value before persistence**. Client-supplied values are never trusted, and the field is recomputed and validated on **every** persistence path (insert, ordinary save, submit, discard, cancel). Permission bypass must not bypass either the database UNIQUE constraint or this derived-field invariant.

**Server invariant (normative):**

- Registered, Released, In Progress, Completed → `active_key == sales_order`
- Discarded, Voided → `active_key IS NULL`
- **No other persisted pair is valid.**

---

## 11. Sales Order Validation

The following are enforced, all server-side:

- Link existence (native, `_validate_links()`).
- Sales Order `docstatus = 1` (Submitted).
- Sales Order not Cancelled.
- Company equality between Job Card and linked Sales Order.
- Acting user's permission to access both the Company and the Sales Order.

Checked at: insert; ordinary save; before Release; before In Progress; before Submit (Completed).

---

## 12. Artwork Gate

- **Exactly one conceptual Artwork-related field is specified: `production_artwork_set`** (corrected 2026-08-13 — see Section 12.1). This replaces the prior "No Artwork field exists in this Draft specification" statement, which predated the governed Artwork reference contract.
- **Production-capable Publication remains blocked** until Artwork approval is server-verifiable, or the governing Business Rule ("A Job Card cannot begin production without approved Artwork," `05_Domain_Model.md`, Business Rules section — **corrected 2026-07-31, ART-ARCH-F10**: cited by rule wording rather than by source line number, since the prior literal line-number citation became stale after that document's 2026-07-31 cardinality correction) is formally changed through separate Architecture Review, Business Review, and Project Owner approval.
- A **demo-only** specification track may retain a **documented manual check before the Registered → Released transition** — see `JobCard_TierA_System_Design.md` Section 10.2.
- Demo-only scope must be **visibly marked non-production** wherever it appears; it does not satisfy the production-capable requirements defined in Section 25 (Publication Classification), specifically Section 25.2.

### 12.1 Production Artwork Set Reference Contract (added 2026-08-13)

Reconciles this specification with `../implementation/JobCard_TierA_System_Design.md` Section 10.3 (that document is **Approval, Version 1.5** as of 2026-08-19, prior historical baseline **Approval, Version 1.4**) and the governing `../blueprint/18_Artwork_Management.md` Section 13 and Sections 16.1/16.2 — all part of that document's **Approval, Version 1.1** baseline as of 2026-08-19 (prior historical baseline Approval, Version 1.0). **Release-time validation of the resolved Set is governed by `../blueprint/18_Artwork_Management.md` Section 14 (Production Release Contract)**, which is not duplicated here.

**Status of the governing Artwork document (as at 2026-08-13).** Artwork System Design Version 1.1 **completed Architecture Review and Business Review on 2026-08-13**; all identified Artwork corrections were **independently verified**, and **no material Artwork review finding remains**. This contract nevertheless **does not depend on that document's Section 7.2 cause-of-supersession rule** — it derives from the prior approved **Approval 1.0** baseline (Sections 13, 16.1, 16.2) plus the Section 14 release contract, consuming only the *state* of a Set, never the *cause*. Artwork System Design Version 1.1 **remains Draft** pending a separate Project Owner lifecycle decision.

| Aspect | Value |
|---|---|
| Conceptual field | `production_artwork_set` |
| Field type | Link → the proposed `PrintHub Production Artwork Set` (name pending governed naming treatment; AR-003 not resolved or modified) |
| Schema-required at creation | **No** — Registered records may be created before Artwork approval exists |
| Required for release | **Mandatory for a successful Registered → Released transition on the production-capable track** (see the track-scope note below) |
| Client mutability | **None** — not client-writable, not importable, not bulk-editable |
| Assignment | **Server-side only**, during the controlled Release transition |
| After Release | **Immutable** |

Rules: only an **Approved for Production** Set may authorize a **new** release; **Draft, Submitted, Rejected, Superseded and Withdrawn** Sets never authorize a new release; a **Superseded** Set does not invalidate an existing binding, so an already Released or In Progress Job Card **retains its historical link and may continue**; a **Withdrawn** Set follows the existing progression-blocking behaviour (Released → In Progress blocked, In Progress → Completed blocked, controlled `discard()` with mandatory terminal reason plus a replacement Job Card); Completed history is never rewritten; and **there is no silent rebinding** in any state.

**Normative status versus operational reliance.** The Approved-Set contract above **is already a normative production-capable design requirement** — it is not dormant and does not "switch on" later. `production_artwork_set` is **mandatory for a successful production-capable Registered → Released transition**, and a **Superseded or Withdrawn Set can never authorize a new release**.

What is not yet true is that it can be **relied upon operationally**. That requires **all** of: the required direct coding specifications are **Published**; implementation is **separately and explicitly authorized**; the required **implementation exists**; required **validation is completed under separate authorization**; the **Artwork production gate is explicitly closed**; and **production use is separately authorized**. **Closing the Artwork production gate alone does none of these** — it does not Publish any specification, does not implement this contract, does not grant implementation authorization, does not authorize production use, and does **not** automatically activate the production-capable track.

The **demo-only** track remains the bounded existing exception under Section 12, governed by its **manual out-of-system Artwork check** (`../implementation/JobCard_TierA_System_Design.md` Section 10.2). It is **not production authority**, must remain visibly marked non-production, **cannot support real production reliance**, and **can never become production authority**. The production-capable mandate is not weakened, the demonstration exception is not widened, and no endpoint or implementation mechanism is prescribed.

**No Artwork Revision child table, copied file hash, copied approval actor, copied approval timestamp, copied revision collection, Proof reference, or Customer Approval Evidence reference is introduced.** The Approved Production Artwork Set remains the sole authority. No field beyond this single governed conceptual field is invented.

**No implementation is claimed and no gate is closed.** The Artwork production gate remains **open**; this specification remains **not Published**, **not safe for coding**, and not production-capable. The existing Job Card lifecycle, status model, `docstatus` treatment, uniqueness mechanism, terminal-action design and permission rules are unchanged.

---

## 13. Multiplicity and Atomic Uniqueness

- **Exactly one active Job Card per Sales Order.** "Active" means Registered, Released, In Progress, or Completed.
- Discarded and Voided Job Cards **permit a replacement**.
- An **ordinary `exists`-style query is insufficient** as the final enforcement mechanism (race-prone under concurrent requests) — empirically reconfirmed: under REPEATABLE-READ an existence check cannot observe a concurrent uncommitted insert.
- **The race-safe mechanism is Selected and Validated** (2026-07-30). Accepted disposition: *Race-safe uniqueness gate closed — nullable active-key mechanism recommended.*

**Selected schema (normative; conceptual, not implementation):**

| Aspect | Requirement |
|---|---|
| Field | `active_key` (Section 9) |
| Field type | Data-compatible nullable string |
| Database representation | `VARCHAR(140) NULL` |
| Constraint | Full-column, single-column UNIQUE index |
| Storage engine | InnoDB |
| Site identity | Implicit |
| Tenant field | Prohibited |
| Company in claim | **Excluded** |

The claim is the **site-local Sales Order identity**. **Company must not be included** — doing so would weaken the invariant to one active Job Card *per Company per Sales Order*.

**Canonical claim value (normative):**

- `active_key` must equal the **exact, successfully validated `sales_order` Link value** while the Job Card is active.
- The Sales Order Link must **resolve successfully before** the claim is assigned.
- Surrounding whitespace in a submitted Sales Order identifier must be **rejected**, not silently converted into another identifier.
- **No application-level case transformation is prescribed**, and none may be applied unless a later controlled validation proves it identity-preserving for every permitted Sales Order name.
- Database comparison follows the **deployed MariaDB column collation**.
- The claim must not be independently editable or supplied by a client.

**UNIQUE-index authority.** The database UNIQUE index is the **authoritative** concurrent-integrity control. Application-level checking alone is never sufficient.

**Acquisition behavior.** The claim is acquired as part of **inserting the Job Card row**; the server assigns the validated Sales Order identity to `active_key`. **No separate claim-acquisition commit is permitted**, and there must be **no interval in which an active Job Card exists without its active claim**.

**Duplicate exception treatment.** A conflict surfaces as MariaDB error **1062**, reported by the governed Frappe version as an integrity/unique-validation failure, and must be translated by the future application into a **stable domain rejection** such as `ActiveJobCardAlreadyExists`. No exact user-facing message is prescribed here.

**Lifecycle release requirement and transaction boundary.** Terminal reason, terminal lifecycle outcome and `active_key` release must commit **in one database transaction**, with **no intermediate commit**. Release must remain coupled to the approved controlled `discard()` (Draft) and `cancel()` (Completed) lifecycles — see Section 13.2. Replacement may succeed only **after** the terminal transaction commits.

**Retry policy.** Duplicate-key **1062 is not automatically retried** — it is a deterministic active-claim conflict. Lock-wait timeout **1205** and deadlock **1213** may receive a **small bounded retry with backoff**; every retry re-enters the same database uniqueness protection; retry behavior must not depend on process-local locks.

**Timeout reconciliation.** On client timeout or lost response the **database state is authoritative**; a client timeout does **not** imply rollback. The caller must query the current active Job Card before attempting a replacement request. A later 1062 does **not**, by itself, prove the caller's original request succeeded — request correlation or equivalent ownership evidence is required before treating an existing record as the result of the timed-out request. **1062 alone must not be classified as idempotent success.**

**Migration requirements.** Migration must (1) populate or derive `active_key` for every existing active record; (2) leave terminal records with NULL claims; (3) run a duplicate-detection query **before** creating the UNIQUE index; (4) identify every Sales Order with more than one active record; (5) require explicit governed remediation; (6) **prohibit silent deletion, silent termination and automatic winner selection**; (7) create the UNIQUE index **only after** duplicate resolution; and (8) **fail visibly** if duplicates remain. Representative detection logic is conceptually a grouping of non-NULL claims having a count greater than one, reported with the offending record identities; the disposable validation used probe table names, which are **not** production names. Historical Discarded and Voided records may coexist for one Sales Order because **multiple NULL values are permitted** by the tested MariaDB UNIQUE semantics.

**Rejected alternatives.** A **separate active-claim record or DocType** is rejected for Tier A: it can provide database-backed uniqueness, but adds a second table or DocType, increases aggregate, migration and operational complexity, introduces separate claim-ownership and orphan-state concerns, and supplies **no integrity advantage** over the selected same-row active key for this invariant. **Sales Order row locking as the sole mechanism** is rejected: it works only when every path cooperates, omission of the lock produced concurrent duplicates, it supplies no durable database constraint, and an existence query under REPEATABLE-READ can miss concurrent uncommitted inserts. A row lock may later be used for orchestration or contention management, but **never as the sole uniqueness guarantee**. Also rejected: ordinary pre-insert existence queries; process-local locks; client-side validation; Redis locking as the only integrity guarantee; and post-commit duplicate reconciliation.

No executable SQL or implementation code is specified here.

### 13.1 Terminal Atomicity Requirement (Finding F-2)

Normative. Each was **validated at the database-transaction level on 2026-07-30**:

- Acquisition of the uniqueness claim must be atomic with Job Card creation.
- Release of the uniqueness claim must be atomic with a **successful** Discard or Cancel.
- A failed or rolled-back terminal action must **not** release the claim.
- A successful terminal action must **not** leave the claim held.
- Replacement creation must not become possible until the prior terminal transaction commits.
- Concurrent replacement attempts must still produce at most one active record.
- Migration and recovery behavior must preserve this invariant.

**Validated transaction boundaries.** On the governed Frappe version, **submit introduces no intermediate commit** and **cancel introduces no intermediate commit** — transactionally coupled claim release is therefore feasible.

See Section 21, test T-22 (expanded) and tests T-51 through T-70 for the concurrency test requirements.

### 13.2 Terminal-Release Boundary (Normative)

- Draft termination must preserve the approved controlled `discard()` lifecycle.
- Completed termination must preserve the approved controlled `cancel()` lifecycle.
- Standard Frappe `docstatus`, hooks, permissions and lifecycle behavior **must not be bypassed**.
- Terminal reason, terminal lifecycle outcome and `active_key` release must commit in **one** database transaction; **no intermediate commit is permitted**.
- Failure at any point must restore the previous active record and its active claim.
- Replacement may succeed only after the terminal transaction commits.
- **The raw guarded SQL statement used inside the disposable validation harness is explicitly NOT selected as the production terminal-action implementation** — it existed solely to demonstrate transactional coupling.

**Concurrent terminal actions (normative required outcome):** only one concurrent terminal action may win; the losing action must fail or return a **deterministic already-terminal result**; the terminal reason must **not** be overwritten; the active claim must be released **exactly once**; and **no replacement window may open before successful terminal commit**. A guarded status/ownership predicate with affected-row verification may form part of the future mechanism, but only if integrated **without bypassing the standard Frappe lifecycle APIs**.

**The exact mechanism** coordinating terminal-reason persistence, `discard()`/`cancel()` invocation, idempotency, concurrent terminal actions and claim release **remains delegated to the separately open terminal-reason persistence validation gate (Section 15). This validation does not close that gate.**

---

## 14. Controlled Transition Requirements

Functional requirements only; exact method names are delegated:

- **Release** — transitions Registered → Released; requires the acting user hold the "release" capability. Artwork obligations differ by track: on the **demo-only track** (currently the only available track), it requires the recorded **manual out-of-system Artwork check** under the governed exception (`JobCard_TierA_System_Design.md` Section 10.2), which is **not production authority**; on the **production-capable track**, it additionally requires **resolution, validation and server-side assignment of an Approved Production Artwork Set** under the reference contract in Section 12.1, with release-time validation of the resolved Set governed by `../blueprint/18_Artwork_Management.md` Section 14. That production-capable obligation **is already a normative design requirement**; it becomes **operationally relied upon** only once every applicable prerequisite exists — Published specifications, separately authorized and completed implementation, validation under separate authorization, explicit closure of the Artwork production gate, and separate production authorization — and **closing that gate alone publishes, implements, authorizes and activates nothing** (Section 12.1). No endpoint or implementation mechanism is prescribed here.
- **Begin Progress** — transitions Released → In Progress; requires the "mark In Progress" capability.
- **Complete (Submit)** — transitions In Progress → Completed via native Submit; requires the "complete" capability; triggers native `docstatus = 1` and the associated cancelled-link/backlink protections.
- **Discard with reason** — transitions any Draft-`docstatus` status to Discarded; requires the "discard" capability; requires `terminal_reason` to be atomically persisted before `discard()` is invoked (Section 15).
- **Void/cancel with reason** — transitions Completed to Voided; requires the "void" capability; requires `terminal_reason` to be atomically persisted before `cancel()` is invoked (Section 15).
- **Delete (Finding F-6)** — permitted only while `status = Registered` and `docstatus = 0`, and the record has not been Released; requires the narrowly scoped "delete erroneous Registered record" capability, distinct from discard/void/administer; prohibited from Released, In Progress, Completed, Discarded, or Voided; requires an explicit server-side lifecycle guard, not merely a client-side rule.

### 14.1 Controlled Terminal-Action API Policy (Finding F-9)

**Validated 2026-07-30.** One conceptual mutation operation equivalent to `terminate_job_card(name, action, reason, request_id)` — the exact Python name remains subject to current naming standards.

- **Method:** POST or the equivalent mutation-only method. **Authentication required; Guest rejected; stable authorization failure.**
- **Single operation:** Desk and restricted API clients use the **same** operation; background execution calls the **same** domain service. **No client-side independent lifecycle path.**
- **Supported actions:** `discard` and `cancel`. Discard is allowed only from Registered, Released or In Progress with `docstatus 0`; cancel only from Completed with `docstatus 1`; all other combinations are rejected **without mutation**.
- The server action must independently validate, **from the authoritative locking read** (Section 15.1): current status; current `docstatus`; active key; terminal reason; terminal request ID; permission capability; Company access; Sales Order integrity where applicable; and uniqueness-claim release behavior (Section 13.1).
- **No early commit.** The final state is returned only after successful lifecycle execution, and an **idempotent replay is identified explicitly in the response**, distinguishable from a newly applied result.
- **Permissions:** ordinary Role Permissions remain necessary and Company User Permissions remain effective. Discard requires the approved governed **discard** capability plus the applicable standard write permission; cancel requires the approved **void/cancel** capability plus standard cancellation permission. Administrator remains privileged but is **not exempt from the lifecycle invariant**, and `ignore_permissions=True` must not bypass the mandatory reason or request correlation. **No `has_permission` or `permission_query_conditions` hook is required by the evidence.**
- **Ordinary REST field updates must not substitute for the controlled action.**

API surface and permission behavior are validated at the design and evidence level; **production implementation of the controller/service mechanism remains unbuilt and unauthorized.**

---

## 15. Terminal-Reason Persistence

**Normative requirement:** the terminal reason must be validated as present, then persisted, then the corresponding native action (`discard()` or `cancel()`) invoked — all within the same atomic server-side transaction — so that the reason is guaranteed stored before the terminal transition completes, and is immutable thereafter.

**Native Frappe `discard()` and `cancel()` do not themselves capture or prompt for a custom reason field** — this must not be assumed by any future implementation.

**Gate status: Closed** (validated 2026-07-30). Accepted disposition: *Terminal-reason persistence gate closed — controlled lifecycle-context mechanism recommended.*

### 15.0 Pinned Lifecycle Findings

- **`discard()`** applies only to Draft records; requires **write** permission; invokes **`before_discard`**; changes `docstatus` to Cancelled/2; invokes **`on_discard`**; **does not run ordinary `validate()`**.
- **`cancel()`** sets in-memory `docstatus` to 2; executes the cancellation save path; invokes **`before_cancel`**; persists the cancellation; invokes **`on_cancel`**; performs backlink checks; **does not use ordinary `validate()` as the terminal invariant enforcement point**.
- Lifecycle methods and their DocType hooks execute **inside the request transaction**; the tested paths introduced **no hidden intermediate commit**; and exceptions from material stages rolled back terminal reason, request ID, lifecycle state, `docstatus` and active-key release together.

**Normative consequence:** a rule implemented only in ordinary `validate()` **cannot** enforce mandatory terminal metadata.

### 15.1 Selected Mechanism — Controlled Lifecycle Context

The production design is: (1) one authenticated controlled server action (Section 14.1) used by Desk, restricted API and background callers; (2) a **transaction-authoritative locking read**; (3) validation of action, state, capability, Company access, reason and request ID; (4) a **private transient lifecycle context** attached by the service to the loaded document; (5) invocation of native **`discard()`** (Draft) or **`cancel()`** (Completed); (6) **mandatory enforcement in the `PrintHub Job Card` controller's `before_discard` and `before_cancel`**; and (7) **one database transaction** containing terminal metadata, terminal lifecycle outcome, `docstatus` mutation and `active_key` release.

Enforcement must be scoped to the **custom PrintHub DocType controller, or an equivalently narrow DocType-specific adapter**. A **broad global `doc_events` hook is not prescribed** as the production mechanism — the disposable validation app used `doc_events` only because its probe DocType was temporary.

**Locking and authoritative state.** The Job Card row must be acquired through a current locking read equivalent to `SELECT ... FOR UPDATE`, and that locking read must be the **first authoritative lifecycle-state read used to make the terminal decision**. The service must not decide from a previously loaded document, a prior consistent-read snapshot, or a later plain read that may reflect an earlier REPEATABLE-READ snapshot. The locked values are authoritative for current status, `docstatus`, active key, terminal reason and terminal request ID. The subsequent document instance must correspond to the locked record state; if it cannot be reconciled, the operation must **fail as a concurrent-modification conflict rather than proceed on stale data**. Validation evidence: a stale decision path produced `TimestampMismatchError`, while decisions from locked values produced stable domain outcomes.

**Transient lifecycle context.** A private, non-persisted context carries the validated terminal reason, canonical terminal request ID and requested terminal action. It is **not a DocType field**, not REST-writable, not importable, not accepted from ordinary document input, exists only for the in-process controlled invocation, and must be **absent from reloaded or independently created document instances**. The controller lifecycle methods must **reject terminal execution when the valid controlled context is absent**; direct callers must not satisfy the invariant merely by setting document fields.

**Discard coordination.** `before_discard` must reject when the controlled context is absent or invalid; verify the locked state is Draft and in an allowed active status; verify terminal metadata is currently blank; persist in the same transaction `status = Discarded`, `terminal_reason`, `terminal_request_id` and `active_key = NULL`; and perform **no commit**. Native `discard()` then preserves standard permission behavior, persists `docstatus 2`, runs standard lifecycle hooks and completes inside the same request transaction. A failure after `before_discard`, including from `on_discard`, rolls all changes back.

**Cancel coordination.** `before_cancel` must reject when the controlled context is absent or invalid; verify the locked state was Completed / `docstatus 1`; verify terminal metadata is blank; and assign `status = Voided`, `terminal_reason`, `terminal_request_id` and `active_key = NULL` on the document. The native cancellation save persists these with `docstatus 2` in the **same database transaction**. Standard cancellation hooks, permissions and backlink checks remain active. A failure from `on_cancel`, backlink validation or another late stage rolls back all terminal metadata, `docstatus` and the active-key release.

### 15.2 Terminal-Reason Validation Rule

Reject `None`; reject empty input; reject whitespace-only input; **reject leading or trailing whitespace rather than silently trimming it**; allow internal newline, carriage-return and tab characters; reject other C0 control characters; enforce a maximum of **500 Unicode code points**; **reject over-length input before lifecycle mutation**; **never silently truncate**; preserve accepted content exactly; escape it on every rendered surface; and **do not treat it as HTML or rich text**.

### 15.3 Idempotency, Errors, Retry and Timeout

**Idempotent replay.** A retry carrying the successful terminal request ID must **not** invoke discard or cancel again. After the row lock, the service returns the persisted terminal outcome when the request ID matches, the reason matches exactly, and the requested action matches the persisted terminal status. A replay alters neither reason, request ID, modified metadata, lifecycle nor active-key state, and is **distinguishable from a newly applied result**.

**Request-ID behavior.** Same Job Card, same request ID, same reason and compatible action → **idempotent replay**. Same Job Card and request ID but different reason → **request conflict**. Same Job Card and request ID but incompatible action → **request conflict**. Same request ID used for another Job Card → **rejected through the database UNIQUE constraint** with stable domain translation. Different request IDs racing → one action wins, the loser receives an already-terminal/different-request conflict, and the stored reason is **not overwritten**. **Reason text must never be used as an idempotency key.**

**Error and HTTP treatment.** Unauthenticated → **401** where supported. Authenticated but unauthorized or wrong Company → **403**. Invalid terminal reason, malformed request ID or missing required request ID → **422**. Action invalid for current state, already terminal under another request, request ID reused with a different reason, request ID reused with an incompatible action, duplicate request ID on another Job Card, or concurrent document mismatch → **409**. Exhausted lock-wait or deadlock retry → **503**. Material underlying errors — **MariaDB 1062**, **lock-wait timeout 1205**, **deadlock 1213**, and Frappe timestamp/concurrent-modification errors — must be translated into stable domain errors. **Never expose framework stack traces, raw SQL or database credentials.**

**Retry policy.** **Never retry 1062 automatically**, and never auto-retry domain conflicts. Bounded retry applies only to **1205** and **1213**, using a small bounded count such as two or three attempts with backoff; retries must reuse the **same** terminal request ID, reacquire the row lock and repeat all validation. **No process-local or Redis-only lock is an integrity guarantee.**

**Timeout reconciliation.** The database state is authoritative; retry with the same request ID; a committed original action returns idempotent replay; a rolled-back original action executes normally; and reason and request ID must **not** be inferred from response loss alone.

### 15.4 Transaction Guarantees

Every material stage remained inside an active database transaction; no lifecycle or custom operation introduced an intermediate commit; reason, request ID, status, `docstatus` and active-key release commit together; exceptions from metadata persistence, active-key release, `on_discard`, `on_cancel`, backlink validation, worker termination or database connection loss all result in database rollback; replacement remains blocked until the successful terminal transaction commits and remains prohibited when terminal processing rolls back. Notification, search-index, file and external side effects are **not** relied upon for transactional correctness.

### 15.5 Migration Requirements

(1) Add nullable terminal-reason and request-ID fields. (2) Existing active records retain a blank terminal reason, a blank terminal request ID and a non-NULL active key. (3) Terminal records created after enforcement require **both** reason and request ID. (4) Detect historical Discarded or Voided records lacking a reason. (5) **Do not fabricate historical reasons.** (6) Require explicit governed treatment for historical exceptions. (7) Detect duplicate non-NULL terminal request IDs **before** creating the UNIQUE index. (8) Prohibit silent request-ID reassignment. (9) Create the UNIQUE index **only after** duplicates are resolved. (10) **Fail visibly** if duplicates remain. Because `PrintHub Job Card` is not yet implemented, the initial implementation is expected to have no legacy product rows; these rules are preserved for later migration safety.

### 15.6 Rejected Alternatives

**Controlled service pre-persistence** is rejected: it is safe only when every caller uses the service; direct lifecycle callers could otherwise bypass mandatory reason enforcement; and it does not place the invariant at the lifecycle-guaranteed enforcement point. A **separate terminal-action record** is rejected: it duplicates terminal information already held by the Job Card, adds a second table or DocType, increases aggregate, migration and operational complexity, introduces cross-record and orphan-state concerns, and still requires lifecycle-hook enforcement to stop direct discard/cancel bypass — adding complexity without removing the essential requirement. Also rejected: client-side-only enforcement; ordinary save followed by a separate terminal request; committing metadata before a separately committed lifecycle operation; raw SQL replacing native lifecycle APIs; Workflow-only enforcement without the server invariant; process-local locks; Redis locks as the sole integrity mechanism; reason text as an idempotency key; and post-commit repair as the primary correctness model.

---

## 16. Sales Order Cancellation and Amendment

As defined in `JobCard_TierA_System_Design.md` Section 15: Sales Order cancellation is allowed while the Job Card is Registered, Released, or In Progress (no native block); it is natively blocked once the Job Card is Completed; a Completed Job Card must be Voided before the Sales Order may be cancelled or amended; no automatic relinking occurs; the replacement sequence (terminate → cancel/amend Sales Order → create replacement Job Card → rely on history/terminal reason for traceability) applies.

---

## 17. Permission Specification — Validated Standard-Permission Baseline — P-2 Closed

**P-2 permission-behavior validation is Closed** (accepted final disposition, 2026-07-30: "P-2 Closed — standard mechanisms sufficient"), following a dedicated execution (2026-07-29, disposition "P-2 Partially Closed — additional validation required") and a narrow closure execution (2026-07-30) against a genuine, exact-commit-verified Frappe v16.29.0 / ERPNext v16.29.0 environment (Section 3 pins). Validation evidence is recorded in Section 21 (test coverage) and the evidence-manifest note below.

Validated baseline:

- Standard DocType Role Permissions.
- Company User Permissions — provide Company scoping when the acting user holds **at least one** matching Company User Permission.
- **Normative provisioning invariant (mandatory):** every user granted an operational PrintHub Job Card role must receive at least one explicit Company User Permission as part of the same governed provisioning action; multi-Company access is the explicit union of the Companies assigned through User Permissions; removing a user's final Company User Permission must occur together with either removal of every operational Job Card role, or account suspension/disablement; a user must never remain active with an operational Job Card role and zero Company User Permissions — this is an **invalid provisioned state**, since a role-bearing user with no Company User Permission is empirically **unrestricted** across Companies under standard Frappe behavior (P-2 finding, confirmed across read/list, export, Company Link search, Sales Order Link search, and REST create surfaces), not denied; direct administrative role assignment outside the governed provisioning procedure must be controlled and auditable; Administrator remains a privileged framework identity unconstrained by ordinary Company User Permissions (Section 17.1); this invariant does not weaken Tenant isolation, which remains site/database based (Accepted ADR-015); the invariant must be validated through provisioning and security acceptance tests before production (Section 21).
- Server-side Company/Sales Order consistency checks (Section 11).
- **No custom `has_permission` hook required** by current governed evidence.
- **No custom `permission_query_conditions` hook required** by current governed evidence.
- Neither hook is prohibited forever: either would require a future concrete, reproduced standard-permission gap plus a separately approved, controlled design change.
- A narrowly scoped **Delete** capability (Finding F-6, Section 14), separate from discard/void/administer, limited to eligible Registered records.
- Exact role names remain a delegated technical decision.

**Validated product-surface and permission-behavior coverage:** Desk list server path; Desk document-load server path (rendered browser UI not directly exercised — non-blocking observation, not an open gate); REST collection access; REST direct-read, create, and update; cross-Company reassignment denial; Company Link-search scoping; Sales Order Link-search scoping for Company-scoped users; private File metadata access; private-file download; authorized private-attachment upload; unauthorized private-file upload denial (server-side, with no orphaned File document or parent-record mutation produced); export scoping; Administrator behavior; U-NOROLE (no operational role — denied File metadata and private downloads for both Companies) behavior; U-NONE (role-bearing, zero Company User Permission — see provisioning invariant above; unrestricted, able to create records and search Sales Orders for both Companies) behavior across both Companies.

### 17.1 Administrator Policy (Findings F-7 / F-10)

- Frappe `Administrator` is a privileged framework identity and may bypass ordinary Role and User Permission checks (accepted v16 evidence).
- Administrator is **not** a normal PrintHub operational role; Company User Permissions must not be described as restricting Administrator.
- Privileged access does not change Tenant isolation, which remains site/database-based (Accepted ADR-015).
- Administrator operations must still preserve the status/`docstatus` consistency invariant (Section 8.1) — privileged access is not a license to violate it through ordinary operation.
- Privileged repair outside normal lifecycle behavior requires a separately governed operational procedure.
- Audit visibility of privileged changes must be validated before production.
- This document does not claim every ERPNext System Manager role holder shares Administrator's unconditional bypass behavior absent separate v16 evidence.

---

## 18. Product Surface Specification

| Surface | Policy |
|---|---|
| Desk form | Included |
| Desk list | Included |
| REST API | Included with restriction |
| Attachments | Included with restriction |
| Sharing | Disabled |
| Bulk import | Disabled |
| Export | Included, for authorized Company-scoped users |
| Print formats | Deferred |
| Reports | Excluded — Configuration Studio retains ownership of future Report/Dashboard Definitions |

---

## 19. API and Permission Requirements

REST resource API access must enforce the same permission stack as Desk access (standard Frappe behavior, pending P-2 confirmation, Section 17). Bulk import is disabled for Tier A and requires no import-specific permission design at this stage. Export must respect Company scoping identically to list-view access.

---

## 20. Audit Requirements

Native `owner`/`creation`/`modified`/`modified_by`, Track Changes, and the controlled terminal-reason mechanism (Section 15) constitute the Tier A audit baseline. No claim of legal compliance is made.

---

## 21. Test Specification

| Test ID | Scenario | Test Class |
|---|---|---|
| T-1 | Valid creation | `IntegrationTestCase` |
| T-2 | Sales Order missing | `IntegrationTestCase` |
| T-3 | Sales Order Draft (not Submitted) | `IntegrationTestCase` |
| T-4 | Sales Order cancelled | `IntegrationTestCase` |
| T-5 | Company mismatch | `IntegrationTestCase` |
| T-6 | Unauthorized Company | `IntegrationTestCase` |
| T-7 | Unauthorized Sales Order | `IntegrationTestCase` |
| T-8 | Valid transitions (full lifecycle) | `IntegrationTestCase` |
| T-9 | Invalid transitions | `IntegrationTestCase` |
| T-10 | Company/Sales Order mutation attempted after Registered | `IntegrationTestCase` |
| T-11 | Released and In Progress remain Draft (`docstatus = 0`) | `IntegrationTestCase` |
| T-12 | Submit produces Completed (`docstatus = 1`) | `IntegrationTestCase` |
| T-13 | Sales Order cancellation while Draft-status Job Card exists (permitted) | `IntegrationTestCase` |
| T-14 | Sales Order cancellation blocked by Completed Job Card | `IntegrationTestCase` |
| T-15 | Discard with reason | `IntegrationTestCase` |
| T-16 | Cancel (Void) with reason | `IntegrationTestCase` |
| T-17 | Missing reason rejected | `IntegrationTestCase` |
| T-18 | Reason immutability after terminal transition | `IntegrationTestCase` |
| T-19 | Second active Job Card against same Sales Order rejected | `IntegrationTestCase` |
| T-20 | Replacement after Discarded permitted | `IntegrationTestCase` |
| T-21 | Replacement after Voided permitted | `IntegrationTestCase` |
| T-22 | Concurrent uniqueness attempt (race-safety), expanded per Finding F-2 to cover both simultaneous creation against the same Sales Order and simultaneous replacement creation immediately following a terminal action's release of the uniqueness claim | `IntegrationTestCase` |
| T-23 | REST permission behavior | `IntegrationTestCase` |
| T-24 | List visibility (Company scoping) | `IntegrationTestCase` |
| T-25 | Attachment permissions | `IntegrationTestCase` |
| T-26 | Export scoping | `IntegrationTestCase` |
| T-27 | Administrator behavior | `IntegrationTestCase` |
| T-28 | Install/migration smoke test | `IntegrationTestCase` |
| T-29 | Direct API status-bypass (Finding F-5): REST update attempts `status = Completed` while `docstatus = 0` — expect server-side rejection | `IntegrationTestCase` |
| T-30 | Direct API status-bypass: REST update attempts to set status directly to Discarded or Voided without the controlled action — expect rejection | `IntegrationTestCase` |
| T-31 | Direct API status-bypass: direct document-save attempt skips Registered → Released → In Progress — expect rejection | `IntegrationTestCase` |
| T-32 | Direct API status-bypass: direct submit attempted from Registered — expect rejection | `IntegrationTestCase` |
| T-33 | Direct API status-bypass: direct submit attempted from Released — expect rejection | `IntegrationTestCase` |
| T-34 | Direct API status-bypass: direct `discard()` attempted without a terminal reason — expect rejection | `IntegrationTestCase` |
| T-35 | Direct API status-bypass: direct `cancel()` attempted without a terminal reason — expect rejection | `IntegrationTestCase` |
| T-36 | Direct API status-bypass: post-terminal attempt to alter `status` — expect rejection, no partial transition, no inconsistent status/`docstatus` pair, no uniqueness-claim corruption | `IntegrationTestCase` |
| T-36a | Direct API status-bypass: post-terminal attempt to alter `terminal_reason` — expect rejection, immutability of the previously persisted reason preserved, no inconsistent status/`docstatus` pair, no uniqueness-claim corruption | `IntegrationTestCase` |
| T-37 | Artwork demo-only track (Finding F-4): manual Artwork approval check required before Registered → Released; release rejected/prevented when the documented manual confirmation is absent — governance-validation / release-check assertion, not a product unit test, since no executable Artwork mechanism exists | `IntegrationTestCase` (governance-validation assertion) |
| T-38 | Artwork demo-only track: specification and environment remain visibly labelled non-production; demo-only classification does not permit production authorization — documentation/configuration assertion | `IntegrationTestCase` (governance-validation assertion) |
| T-39 | Artwork production-capable track: Publication classification cannot become production-capable unless Artwork approval is server-verifiable or the governing rule has been formally changed — governance-validation assertion, not executable against product code while no Artwork mechanism exists | `IntegrationTestCase` (governance-validation assertion) |
| T-40 | Artwork production-capable track: a demo-only manual check cannot satisfy the production gate — governance-validation assertion | `IntegrationTestCase` (governance-validation assertion) |
| T-41 | Delete capability (Finding F-6): permitted deletion of an eligible Registered (`docstatus = 0`, pre-Release) record by a user holding the delete capability | `IntegrationTestCase` |
| T-42 | Delete capability: denied deletion after Release (Released, In Progress, Completed, Discarded, Voided) | `IntegrationTestCase` |
| T-43 | Delete capability: denied deletion attempted by a user without the delete capability | `IntegrationTestCase` |
| T-44 | Delete capability: discard remains available and is the normal audited termination path for a saved Draft-status record, independent of delete capability | `IntegrationTestCase` |
| T-45 | Provisioning invariant: a user granted an operational Job Card role together with at least one Company User Permission is correctly Company-scoped | `IntegrationTestCase` |
| T-46 | Provisioning invariant: a user granted Company User Permissions for multiple Companies has access to the explicit union of those Companies | `IntegrationTestCase` |
| T-47 | Provisioning invariant: a user granted an operational Job Card role with zero Company User Permissions is rejected by provisioning controls before activation (invalid provisioned state) | `IntegrationTestCase` |
| T-48 | Provisioning invariant: removal of a user's final Company User Permission is validated to occur together with removal of every operational Job Card role, or account suspension/disablement | `IntegrationTestCase` |
| T-49 | Provisioning invariant: unauthorized direct administrative role assignment outside the governed provisioning procedure is detected and audited | `IntegrationTestCase` |
| T-50 | Provisioning invariant: Administrator is separately classified and not subject to ordinary Company User Permission scoping | `IntegrationTestCase` |
| T-51 | Race-safe uniqueness: two-way concurrent initial creation against one Sales Order — exactly one commits, the loser fails deterministically | `IntegrationTestCase` |
| T-52 | Race-safe uniqueness: higher-contention concurrent creation (multiple simultaneous attempts) — exactly one active record, no duplicate ever visible after commit | `IntegrationTestCase` |
| T-53 | Race-safe uniqueness: losing transaction rolls back its entire unit of work, leaving no partial Job Card and no stale claim | `IntegrationTestCase` |
| T-54 | Race-safe uniqueness: winning transaction fails before commit — claim acquisition rolls back, later clean creation succeeds without manual cleanup | `IntegrationTestCase` |
| T-55 | Race-safe uniqueness: duplicate request retry cannot create a second active record; the error is stable and classifiable | `IntegrationTestCase` |
| T-56 | Terminal release: Draft-like controlled discard releases the claim atomically with terminal reason and lifecycle outcome | `IntegrationTestCase` |
| T-57 | Terminal release: Completed/Submitted controlled cancel releases the claim atomically with terminal reason and lifecycle outcome | `IntegrationTestCase` |
| T-58 | Terminal release: replacement attempted before the terminal transaction commits cannot commit until release commits | `IntegrationTestCase` |
| T-59 | Terminal release: terminal action rolled back leaves the record active, the claim held, and replacement prohibited | `IntegrationTestCase` |
| T-60 | Terminal release: attempted release using a different Job Card identity is rejected; the original claim remains intact | `IntegrationTestCase` |
| T-61 | Race-safe uniqueness: concurrent replacement attempts after release produce exactly one active record | `IntegrationTestCase` |
| T-62 | Race-safe uniqueness: concurrent terminal actions — one wins, the loser returns a deterministic already-terminal result, terminal reason is not overwritten, claim released exactly once | `IntegrationTestCase` |
| T-63 | Derived-field invariant: direct field mutation of `active_key` through ordinary save is rejected or overwritten by the server-derived value before persistence | `IntegrationTestCase` |
| T-64 | Derived-field invariant: direct REST mutation of `active_key` cannot release the claim or enable a duplicate active record | `IntegrationTestCase` |
| T-65 | Derived-field invariant: privileged, background and Administrator execution cannot bypass the database UNIQUE constraint or the derived-field invariant | `IntegrationTestCase` |
| T-66 | Migration: UNIQUE index creation succeeds on clean valid data and enforces uniqueness immediately | `IntegrationTestCase` |
| T-67 | Migration: pre-existing duplicate active records cause visible migration failure; duplicates are not silently discarded, terminated or auto-resolved | `IntegrationTestCase` |
| T-68 | Migration: multiple historical Discarded/Voided records for one Sales Order coexist while exactly one active replacement remains enforceable | `IntegrationTestCase` |
| T-69 | Claim value: field length and deployed collation behavior; whitespace-bearing Sales Order identifiers are rejected rather than silently converted; no case transformation is applied | `IntegrationTestCase` |
| T-70 | Failure recovery: database connection loss, worker termination, lock wait, client timeout and service restart each leave no stale claim and no committed duplicate; committed claims persist | `IntegrationTestCase` |
| T-71 | Controlled discard: successful Draft termination persists reason and request ID, sets Discarded/`docstatus 2`, releases `active_key`, in one transaction | `IntegrationTestCase` |
| T-72 | Controlled cancel: successful Completed termination persists reason and request ID, sets Voided/`docstatus 2`, releases `active_key`, in one transaction | `IntegrationTestCase` |
| T-73 | Rollback: failure before and after terminal metadata persistence leaves lifecycle, reason, request ID and claim unchanged | `IntegrationTestCase` |
| T-74 | Rollback: failure after active-key release logic restores the claim and leaves no terminal metadata | `IntegrationTestCase` |
| T-75 | Rollback: late `on_discard` / `on_cancel` / backlink-validation failure rolls back all terminal metadata, `docstatus` and claim release | `IntegrationTestCase` |
| T-76 | No hidden intermediate commit occurs between reason validation, metadata persistence, claim release and lifecycle completion | `IntegrationTestCase` |
| T-77 | Direct `discard()` without the controlled context is rejected; record remains active with claim held and no reason written | `IntegrationTestCase` |
| T-78 | Direct `cancel()` without the controlled context is rejected; Submitted record remains Completed with claim held | `IntegrationTestCase` |
| T-79 | Standard framework lifecycle endpoints (document `run_method`, `frappe.client.cancel`) cannot achieve a terminal outcome | `IntegrationTestCase` |
| T-80 | Ordinary save and REST resource updates cannot set or alter terminal reason, request ID, terminal status or active key | `IntegrationTestCase` |
| T-81 | Client-supplied internal fields at create cannot fabricate a completed terminal action | `IntegrationTestCase` |
| T-82 | Post-terminal reason modification is rejected through Desk-equivalent API, REST, background, Administrator and `ignore_permissions=True` | `IntegrationTestCase` |
| T-83 | Reason validation: empty, whitespace-only, and leading/trailing-whitespace inputs are rejected before lifecycle mutation | `IntegrationTestCase` |
| T-84 | Reason validation: Unicode, emoji and multiline reasons persist and reload exactly | `IntegrationTestCase` |
| T-85 | Reason validation: 500 code points accepted, 501 and grossly over-length rejected, with no silent truncation and no partial lifecycle mutation | `IntegrationTestCase` |
| T-86 | Reason validation: disallowed C0 control characters rejected; content stored as plain text and escaped on render | `IntegrationTestCase` |
| T-87 | Concurrency: two simultaneous terminal actions with different request IDs — one wins, loser receives a deterministic conflict, reason never overwritten, claim released once | `IntegrationTestCase` |
| T-88 | Concurrency: simultaneous and sequential retry with the same request ID returns idempotent replay without a second lifecycle action | `IntegrationTestCase` |
| T-89 | Request ID reused on the same Job Card with a different reason or incompatible action is rejected as a request conflict | `IntegrationTestCase` |
| T-90 | Request ID reused on another Job Card is rejected via the UNIQUE constraint with stable domain translation | `IntegrationTestCase` |
| T-91 | Simultaneous discard actions and simultaneous cancel actions each yield one winner and one deterministic loser | `IntegrationTestCase` |
| T-92 | Invalid action for current state (cancel on Draft, discard on Completed, terminal action on an already-terminal record) is rejected without mutation | `IntegrationTestCase` |
| T-93 | Committed-state scan: no row has terminal status without a reason, terminal status with a non-NULL active key, terminal status with mismatched `docstatus`, active status carrying terminal metadata, or active status without an active key | `IntegrationTestCase` |
| T-94 | Timeout reconciliation: client timeout after commit followed by same-request retry returns idempotent replay; timeout followed by rollback permits normal execution | `IntegrationTestCase` |
| T-95 | Locking: the terminal decision is taken from the authoritative locking read; a stale-snapshot decision path is rejected as a concurrent-modification conflict | `IntegrationTestCase` |
| T-96 | Privileged paths: Administrator and `ignore_permissions=True` cannot terminate without a valid reason and request ID | `IntegrationTestCase` |
| T-97 | Background execution uses the same domain service with identical validation, transaction behavior and request-ID handling; a retried job is idempotent | `IntegrationTestCase` |
| T-98 | Controlled action API: mutation-only method, authentication required, Guest rejected, unauthorized and wrong-Company access denied without mutation | `IntegrationTestCase` |
| T-99 | Error translation: 1062, 1205, 1213 and timestamp/concurrent-modification errors map to stable domain errors and HTTP categories; no stack traces or SQL exposed | `IntegrationTestCase` |
| T-100 | Migration: terminal metadata fields added; active records retain blank metadata and a non-NULL active key; historical terminal rows lacking a reason are detected without fabrication; duplicate request IDs block index creation visibly | `IntegrationTestCase` |

`UnitTestCase` is reserved for any genuinely isolated, non-database pure logic identified during implementation (none currently identified). No test code is written in this specification.

---

## 22. Schema, Metadata, Fixture, and Migration Treatment

DocType JSON, controller, and Role Permission rows are app-owned metadata/Python, installed via ordinary `bench migrate` — no fixtures presumed necessary. No patches are anticipated for initial installation. Exact module placement and Workspace configuration are delegated technical decisions requiring pre-Publication validation (Section 24).

---

## 23. Explicit Exclusions

As Section 9's "Explicitly Not Included" plus: ERPNext Manufacturing Job Card reuse; Machine, Material, BOM, scheduling, Estimation, Quotation, Enquiry, Tier B, Tenant Override, central identity, analytics, provider, Marketplace, AI, deployment, or provisioning scope of any kind.

---

## 24. Pre-Publication Gate Checklist

- [ ] Architecture Review complete
- [ ] Business Review complete
- [ ] Project Owner lifecycle approval complete
- [x] P-2 permission-behavior validation complete (Closed, 2026-07-30 — "P-2 Closed — standard mechanisms sufficient")
- [x] Race-safe uniqueness mechanism selected and validated (Closed, 2026-07-30 — nullable server-derived active key under a database UNIQUE constraint)
- [x] Database UNIQUE enforcement confirmed under concurrent creation
- [x] Atomic release and replacement ordering validated at the database-transaction level
- [x] Migration duplicate detection and index-failure behavior validated
- [x] Privileged execution cannot bypass the database constraint
- [x] Terminal-reason persistence mechanism selected and validated (Closed, 2026-07-30 — controlled lifecycle-context mechanism)
- [x] `terminal_reason` field type, storage and application bound confirmed (Small Text / `TEXT NULL` / 500 Unicode code points)
- [x] Terminal request correlation and timeout reconciliation validated
- [x] Controlled discard coordination validated
- [x] Controlled cancel coordination validated
- [x] Atomic reason, lifecycle, `docstatus` and active-key release validated
- [x] Direct lifecycle and REST bypass protections validated at the design/evidence level
- [x] Controlled terminal-action API and permission behavior validated
- [x] Concurrent terminal-action and idempotent replay behavior validated
- [x] Terminal metadata migration behavior validated
- [ ] Production implementation of the controller/service mechanism
- [ ] Production security and acceptance execution
- [x] Standard Role and Company User Permission behavior validated
- [x] Attachment permissions (metadata, download, upload) validated
- [x] REST/API permission behavior validated
- [x] List-query and Link-search permission behavior validated
- [x] Export scoping validated
- [x] Administrator behavior recorded
- [ ] Operational-role and Company-User-Permission provisioning invariant has an approved enforcement and audit procedure before production
- [ ] Exact app module decided
- [ ] Production Artwork gate closed, or specification explicitly classified demo-only
- [ ] Documentation references synchronized
- [ ] No unresolved normative coding behavior remains
- [x] `terminal_reason` fieldtype, length, and storage behavior confirmed against the approved terminal-action design (Finding F-3 — closed 2026-07-30; see the field-type item above and Section 15.2)
- [ ] Status/`docstatus` consistency invariant (Section 8.1) confirmed testable and enforced, including direct API/field-level bypass rejection (Finding F-1)
- [ ] Active-key terminal atomicity (Section 13.1) mechanism selected and validated, including concurrent-replacement behavior (Finding F-2)
- [ ] Delete capability boundary (Section 14) confirmed limited to eligible Registered records, with denial-after-Release validated (Finding F-6)
- [ ] Artwork-track and direct-API-bypass test coverage (tests T-29 through T-40) executed (Findings F-4, F-5)
- [x] Controlled terminal-action API exposure and permission enforcement (Section 14.1) validated (Finding F-9 — closed 2026-07-30 at the design/evidence level; production implementation remains open above)

### 24.1 P-2 Validation Evidence

- **Dedicated execution** — 2026-07-29, disposition "P-2 Partially Closed — additional validation required."
- **Narrow closure execution** — 2026-07-30, accepted final disposition "P-2 Closed — standard mechanisms sufficient."
- **Environment:** Frappe `frappe/frappe@06613fc60b44d5736007ae3107cdab029b2ae045` (reported v16.29.0); ERPNext `frappe/erpnext@a5de60c357d531cb31da093f0b86301776965173` (reported v16.29.0); Python 3.14.2; Node v24.16.0; MariaDB 10.6; Redis 6.2-alpine; Debian 12, linux/x86_64; multi-worker Gunicorn 23.0.0 (narrow closure); localhost-only disposable sites; no production or personal data. Docker image evidence from the narrow closure (`frappe/bench:latest` image ID beginning `sha256:c66af151`, `mariadb:10.6` beginning `sha256:114d40be`, `redis:6.2-alpine` beginning `sha256:ec5e187c`) is recorded for traceability only — mutable image tags are **not** the source-of-truth pins; the verified Git commits above are authoritative.
- **Evidence manifest:** `manifest.csv`, SHA-256 `d59a33b96877fd784bb51f0f837bedf1957d41424061d11e8d27e9449b5ec456`, 26 artifacts, every reported narrow-closure test mapped to at least one artifact, all artifacts sanitized and secret-scanned (no passwords, cookies, Authorization headers, session IDs, database credentials, or CSRF tokens retained). Evidence is retained outside this repository under `/tmp/printhub-p2-closure-20260730/evidence/` — this path is **disposable and not a permanent repository artifact**; the durable record is this evidence summary together with the exact source pins, environment details, and manifest digest above, plus the accepted disposition. The raw evidence directory is not added to Git.
- **Standard-permission conclusion:** no standard-permission gap requiring `permission_query_conditions` or `has_permission` was reproduced against the tested surfaces (Section 17).

### 24.3 Terminal-Reason Persistence Validation Evidence

- **Validation date:** 2026-07-30. **Accepted disposition:** *Terminal-reason persistence gate closed — controlled lifecycle-context mechanism recommended.*
- **Source pins:** Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` (reported 16.29.0); ERPNext `a5de60c357d531cb31da093f0b86301776965173` (reported 16.29.0). **Exact Git commits are authoritative; mutable branches, version strings and container tags are not authoritative source pins.**
- **Runtime baseline:** Python 3.14.2; Node v24.13.0; MariaDB 10.6.27; Redis 6.2.23; InnoDB; REPEATABLE-READ; autocommit disabled for the tested request transactions; Gunicorn with four workers; one background worker; independent database connections; localhost-only disposable site; synthetic data only.
- **Validated coverage:** successful Draft discard; successful Completed cancellation; failures before and after metadata persistence; failures after claim-release logic; late discard and cancel hook failure; absence of hidden commits; ordinary-save metadata mutation; REST metadata and lifecycle mutation; direct discard without context; direct cancel without context; standard framework lifecycle endpoints; client-supplied internal fields; post-terminal reason modification; empty and whitespace-only reasons; surrounding whitespace; Unicode, emoji and multiline reasons; 500/501-character boundaries; control characters; concurrent different requests; same-request concurrent and sequential replay; request-ID reuse with conflicting reason; cross-document request-ID reuse; simultaneous discard; simultaneous cancel; invalid state/action; replacement before and after terminal commit; terminal rollback; prohibited committed-state scans; timeout after commit; timeout followed by rollback; worker termination; database connection loss; lock wait; service restart; Administrator controlled action; privileged direct lifecycle bypass; background execution and retry; hook order and context availability; authenticated mutation endpoint; unauthorized and wrong-Company access; Desk-equivalent endpoint execution; ordinary resource-API bypass; field migration; legacy terminal rows; duplicate request-ID migration; and existing active records.
- **Rendered browser UI was not directly exercised** — recorded as a **non-blocking observation**; Desk must use the validated controlled endpoint.
- **Evidence manifest:** `manifest.csv`, SHA-256 `cc119cf796062ba78871ecc56507d08f1e9a15fa5384ae9aedbb29b47b5e85a2`; **18 evidence files including the manifest**; **17 listed artifacts**; a **structured event log of 251 records**. Retained outside this repository under `/tmp/printhub-terminal-reason-20260730/evidence/`.
- The retained path is **disposable and outside the PrintHub repository**; **every material reported test maps to evidence**; evidence was sanitized and secret-scanned; **no passwords, tokens, cookies, Authorization headers, CSRF tokens, session identifiers or database credentials were retained**; **raw evidence must not be added to Git**. The durable record is this documentation summary together with the exact source pins, environment baseline, manifest digest and accepted disposition.

### 24.2 Race-Safe Uniqueness Validation Evidence

- **Validation date:** 2026-07-30. **Accepted disposition:** *Race-safe uniqueness gate closed — nullable active-key mechanism recommended.*
- **Source pins:** Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` (reported 16.29.0); ERPNext `a5de60c357d531cb31da093f0b86301776965173` (reported 16.29.0). **Exact Git commits are authoritative; mutable branch names and container tags are not authoritative source pins.**
- **Material runtime baseline:** Python 3.14.2; Node v24.13.0; MariaDB 10.6.27; Redis 6.2.23; InnoDB; transaction isolation REPEATABLE-READ; autocommit disabled for the tested request transactions; multi-worker Gunicorn runtime; independent database connections; localhost-only disposable environment; synthetic data only.
- **Validated coverage:** two-way concurrent initial creation; higher-contention creation; losing-transaction rollback; winning-transaction rollback; duplicate request retry; Draft-like terminal release; Submitted-like cancellation release; replacement blocked before terminal commit; terminal rollback; wrong-owner release attempts; concurrent replacement; concurrent terminal actions; direct field-mutation bypass; REST mutation bypass; privileged and Administrator execution; clean index creation; duplicate-data migration failure; terminal historical records; field length and collation behavior; database connection loss; worker termination; lock wait; client timeout; and service restart. **116 concurrent attempts under the selected mechanism produced no committed duplicate.**
- **Evidence manifest:** `manifest.csv`, SHA-256 `52f269358d44ceea72c7562cdb6ab6daa42d457a8fa721ec454b7ef197adb8f2`; **12 evidence files including the manifest**; **11 artifacts listed by the manifest**; a **structured event log of 327 records** covering the material RSU scenarios. Retained outside this repository under `/tmp/printhub-uniqueness-20260730/evidence/`.
- The retained path is **disposable and outside the PrintHub repository**; evidence was **secret-scanned**; **no credentials, tokens, cookies, session identifiers or database passwords were retained**; **raw evidence must not be added to Git**. The durable record is this documentation summary together with the source pins, environment baseline, manifest digest and accepted disposition.

---

## 25. Publication Classification

Two possible future classifications — **neither is chosen or applied by this Draft:**

### 25.1 Demo-Only Published Specification

May be Published only when: explicitly marked non-production; the manual Artwork check is preserved and documented; no production reliance is allowed; a **separate demo-only implementation authorization** is still separately required.

### 25.2 Production-Capable Published Specification

May be Published only when: Artwork approval is server-verifiable; **or** the Artwork prerequisite has been formally changed per Section 12.

---

## 26. Review Status

**Architecture Review**
- Date: 2026-07-29
- Disposition: **Corrections Required**
- Blocking findings: F-1 (status/`docstatus` consistency invariant, addressed in Section 8.1), F-6 (delete-capability alignment, addressed in Sections 14/17)
- Non-blocking findings: F-2 (addressed, Section 13.1), F-3 (fieldtype gate, addressed, Section 9), F-4 (Artwork-track tests, addressed, Section 21), F-5 (API-bypass tests, addressed, Section 21), F-7/F-10 (Administrator policy, addressed, Section 17.1), F-8 (cross-reference correction, addressed, Section 12), F-9 (controlled terminal-action API policy, addressed, Section 14.1)
- Corrections applied in **Version 0.2**
- **Targeted Architecture re-review pending**
- Architecture Review is **not** marked Accepted by this correction

**Business Review**
- Date: 2026-07-29
- Disposition: **Accepted with non-blocking observations**
- No mandatory business correction identified
- Approved business baseline unchanged
- No repeat Business Review required unless a later correction changes business semantics

**Dedicated Job Card Version 1.5 Review (completed read-only, 2026-08-13)**
- Scope: the Version 1.5 Production Artwork Set reference contract in `../implementation/JobCard_TierA_System_Design.md` Sections 9 and 10.3, together with its mirror in this specification's Sections 12 and 12.1.
- **Architecture Review: Accepted with non-blocking corrections. Business Review: Accepted. Documentation Governance: Passed with non-blocking corrections.**
- Local findings **JC15-ARCH-F1** (this specification's Section 14 controlled-Release inventory omitted the production-capable Approved-Set obligation), **JC15-ARCH-F2** (missing cross-reference to the governing Artwork Section 14 release contract), **JC15-ARCH-F3** (Set-mandatory release rule not scoped to the production-capable track), and **JC15-GOV-F1** (stale "unreviewed / Pending review" wording about Artwork Version 1.1 Section 7.2).
- **All four corrections are applied in the current uncommitted Version 0.7 revision. Closure is pending a separate targeted read-only post-correction verification; no JC15 finding is marked Closed.**
- **These are local review labels, not Architecture Review Register identifiers.** No AR identifier was created; **AR-003 remains unresolved.**
- **Review acceptance is not lifecycle Approval.** At the time of this review, this specification remained **Draft, Version 0.7**, and the companion System Design remained **Draft, Version 1.5**; the Project Owner subsequently granted lifecycle Approval to the System Design on **2026-08-19**, promoting it to **Approval, Version 1.5** (with **Approval, Version 1.4** as prior historical baseline). This specification itself remains **Draft, Version 0.8**, **not Published** and **not safe for coding** — the System Design's Approval does **not** publish or approve this companion specification. **No Publication, implementation authorization, runtime-validation authorization, production readiness, or gate closure follows**; the Artwork production gate and every other open pre-Publication gate remain **Open**.

**Post-Correction Verification of the Version 1.5 Review (completed read-only, 2026-08-13)**
- **JC15-ARCH-F1: Closed by verified correction.** **JC15-ARCH-F2: Closed by verified correction.** **JC15-GOV-F1: Closed by verified correction.**
- **JC15-ARCH-F3: Partially Closed** — the production-capable/demo-only separation was present, but the activation wording understated the contract's normative standing.
- New local observations: **JC15-PCV-F1** — wording in Sections 12.1 and 14 here, and Section 10.3 of the companion System Design, implied the Approved-Set obligation only "takes effect" once the Artwork production gate closes and that gate closure alone activates the production-capable track; **JC15-PCV-F2** — two stale "Pending review" current-state premises in `../Documentation_Status.md`.
- **JC15-PCV-F1 is corrected in both Job Card documents, and JC15-PCV-F2 in `../Documentation_Status.md`, in this same change.** The corrected rule records that the Approved-Set contract **is already a normative production-capable design requirement**, that operational reliance additionally requires Published specifications, separately authorized and completed implementation, validation under separate authorization, explicit Artwork production gate closure and separate production authorization, and that **closing that gate alone publishes, implements, authorizes and activates nothing**.
- **JC15-ARCH-F3, JC15-PCV-F1 and JC15-PCV-F2 remain open pending a separate read-only verification and are not marked Closed by this correction.**
- **All JC15, LR and LV labels are local review labels and are not Architecture Review Register identifiers.** No AR identifier was created; **AR-003 remains unresolved.** **Review correction does not constitute lifecycle Approval**; this specification remains **Draft, Version 0.7**, **not Published**, **not safe for coding**, and the companion System Design remains **Draft, Version 1.5** with **Approval, Version 1.4** as prior approved baseline.

- Project Owner Document Lifecycle Approval: **Not Granted**
- Publication: **Not Granted**
- Implementation Authorization: **Not Granted**

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-29 | PrintHub Architecture Team | Initial Draft DocType Specification for PrintHub Job Card Tier A, subordinate to `JobCard_TierA_System_Design.md` (Draft 0.1). Records the Project Owner-approved design baseline; the technical DocType identity (`PrintHub Job Card`); proposed DocType-level properties; the Registered/Released/In Progress/Completed/Discarded/Voided status-to-docstatus mapping and permitted transition matrix; the proposed required/optional/framework-metadata field specification and mutability matrix; Sales Order validation requirements; the Artwork production gate and its bounded demo-only exception, with the demonstration boundary fixed at the Registered→Released transition; the multiplicity and race-safe uniqueness requirement (mechanism delegated, pre-Publication validation mandatory); controlled-transition functional requirements; the terminal-reason atomic-persistence normative requirement (mechanism pending pre-Publication validation); the provisional permission baseline (pending P-2); the approved product-surface policy; a 28-item test specification; schema/fixture/migration treatment; the pre-Publication gate checklist; and the two mutually exclusive future Publication classifications (demo-only vs. production-capable), neither selected here. No Architecture Review, Business Review, or Project Owner document lifecycle approval has yet occurred. No implementation authorization was granted. This document is not safe for coding while Draft. |
| 0.2 | 2026-07-29 | Architecture and Business Review Correction | Applied the findings of the formal combined Architecture Review (Disposition: Corrections Required) and Business Review (Disposition: Accepted with non-blocking observations), both dated 2026-07-29. Added Section 8.1, a normative status/`docstatus` consistency invariant closing Finding F-1 (blocking): exact insert/save/submit/discard/cancel safeguards and an explicit prohibition on direct API/field-level bypass, cross-referenced to `JobCard_TierA_System_Design.md` Section 12.1. Added a "Delete" controlled transition (Section 14) and the corresponding narrowly scoped delete capability (Section 17), closing Finding F-6 (blocking): eligible only for Registered, `docstatus = 0`, pre-Release records, distinct from discard/void/administer, resolving the prior omission relative to the System Design's correction model. Added Section 13.1, closing Finding F-2 (non-blocking): terminal atomicity requirements for the race-safe uniqueness claim. Marked `terminal_reason`'s fieldtype as "proposed; confirmation required before Publication" (Section 9) and added a corresponding Pre-Publication Gate Checklist item, closing Finding F-3 (non-blocking). Added Section 17.1, closing Findings F-7/F-10 (non-blocking): Administrator policy. Added Section 14.1, closing Finding F-9 (non-blocking): controlled terminal-action API policy. Corrected the imprecise cross-reference in Section 12 from "the production-capable gate (Section 25)" to explicitly identify Section 25 as Publication Classification and Section 25.2 as the production-capable requirements, closing Finding F-8 (non-blocking). Expanded the Test Specification (Section 21) with tests T-29 through T-36 (direct API status-bypass, Finding F-5), T-37 through T-40 (Artwork demo-only and production-capable track governance-validation assertions, Finding F-4), and T-41 through T-44 (delete capability, Finding F-6), and expanded T-22's description to cover terminal-action release and simultaneous replacement creation (Finding F-2). Expanded the Pre-Publication Gate Checklist (Section 24) with explicit items for each of the above. Recorded the formal review results in Section 26: Architecture Review Corrections Required (blocking findings F-1, F-6, both addressed in this version; non-blocking findings F-2 through F-10 addressed); Business Review Accepted with non-blocking observations; targeted Architecture re-review pending; Project Owner Document Lifecycle Approval remains Not Granted; Publication remains Not Granted. No approved Project Owner design decision was changed: technical name, business purpose, Sales Order eligibility, multiplicity, lifecycle states and transition sequence, the Artwork two-track boundary, Company/Tenant treatment, the approved surface policy, and excluded scope are all unchanged. This document remains Draft and is not promoted to Published by this correction; it remains not safe for coding. No implementation authorization was granted. No other tracked document was modified. |
| 0.3 | 2026-07-30 | P-2 Closure Evidence Synchronization | Factual synchronization recording the Project Owner-authorized P-2 dedicated execution (2026-07-29, disposition "P-2 Partially Closed — additional validation required") and narrow closure execution (2026-07-30, accepted final disposition "P-2 Closed — standard mechanisms sufficient"), both conducted against a genuine, exact-commit-verified Frappe v16.29.0 / ERPNext v16.29.0 environment. Updated the System Design cross-reference (Section 3) to Approval, Version 1.1. Rewrote Section 17 from "Provisional pending P-2" to "Validated Standard-Permission Baseline — P-2 Closed": standard DocType Role Permissions and Company User Permissions confirmed sufficient across Desk list/document-load server paths, REST collection/direct-read/create/update, cross-Company reassignment denial, Company and Sales Order Link-search scoping, private File metadata/download/upload (authorized and unauthorized-denial), export scoping, and Administrator behavior; recorded that neither `has_permission` nor `permission_query_conditions` is required by current governed evidence, and that neither is prohibited forever — either requires a future concrete reproduced gap plus a separately approved, controlled design change; added the mandatory eight-point operational-role/Company-User-Permission provisioning invariant, arising from the confirmed finding that a role-bearing user with zero Company User Permissions (U-NONE) is unrestricted, not denied, across Companies. Expanded the Test Specification (Section 21) with tests T-45 through T-50 (permanent acceptance coverage for the provisioning invariant) and closed residual observation R-2 by splitting T-36 (post-terminal attempt to alter status or terminal_reason) into distinct rows T-36 (status) and T-36a (terminal_reason). Updated the Pre-Publication Gate Checklist (Section 24) to mark complete: P-2 permission-behavior validation; standard Role and Company User Permission behavior validation; REST/API permission behavior validation; attachment metadata/download/upload permission validation; list-query and Link-search permission behavior validation; export scoping validation; Administrator behavior recorded — and added an unchecked item requiring an approved enforcement and audit procedure for the provisioning invariant before production. Added Section 24.1, recording the P-2 validation evidence: exact environment and source-commit pins, Docker image evidence (recorded for traceability only, not as source-of-truth pins), the evidence manifest (`manifest.csv`, SHA-256 `d59a33b96877fd784bb51f0f837bedf1957d41424061d11e8d27e9449b5ec456`, 26 artifacts, sanitized and secret-scanned, retained outside this repository under a disposable `/tmp` path not added to Git), and the standard-permission conclusion. Race-safe uniqueness mechanism selection/validation, terminal-reason persistence mechanism selection/validation, the Artwork production gate, and the exact app module decision all **remain open** and unmarked in the Pre-Publication Gate Checklist. No approved Project Owner design decision was changed; this document remains Draft, targets Published, and remains **not safe for coding**; Publication was **not** granted; no implementation authorization was granted. No repeat Business Review was required and none was recorded; no new Architecture Review disposition was recorded. No other tracked document was modified by this task beyond the companion System Design and Documentation Status synchronizations. |
| 0.4 | 2026-07-30 | Race-Safe Uniqueness Closure Evidence Synchronization | Factual synchronization recording the completed race-safe uniqueness validation (2026-07-30), accepted disposition "Race-safe uniqueness gate closed — nullable active-key mechanism recommended", validated against Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` and ERPNext `a5de60c357d531cb31da093f0b86301776965173` (both reported 16.29.0) on Python 3.14.2, Node v24.13.0, MariaDB 10.6.27, Redis 6.2.23, InnoDB, REPEATABLE-READ isolation, autocommit disabled for the tested request transactions, a multi-worker Gunicorn runtime with independent database connections, a localhost-only disposable environment and synthetic data only; exact Git commits are authoritative and mutable branch names and container tags are not. Updated the governing System Design reference to Approval, Version 1.2. Added `active_key` to the field specification (Section 9) as an internal, system-managed, server-derived Data field with database intent `VARCHAR(140) NULL`, Unique Yes, Required No at schema level because terminal records require NULL, hidden/internal, read-only to clients, not importable, set only by the server invariant, holding the exact validated Sales Order Link identity while active and NULL when terminal, and explicitly not an independently meaningful business field. Extended the field mutability matrix (Section 10) so that `active_key` is server-derived at every lifecycle stage and any client-supplied value — through ordinary save, direct REST mutation, background execution, or Administrator/privileged execution — is rejected or overwritten by the server-derived value before persistence, with the field recomputed and validated on every persistence path and permission bypass unable to bypass either the database UNIQUE constraint or the derived-field invariant. Recorded the normative server invariant (`active_key == sales_order` for Registered/Released/In Progress/Completed; `active_key IS NULL` for Discarded/Voided; no other persisted pair valid). Rewrote Section 13 with the selected schema (full-column single-column InnoDB UNIQUE index; site identity implicit; Tenant field prohibited; Company excluded from the claim because including it would weaken the invariant to one active Job Card per Company per Sales Order); the canonical claim value as the exact successfully validated `sales_order` Link value with the Link resolving before assignment, surrounding whitespace rejected rather than silently converted, no application-level case transformation prescribed unless later proven identity-preserving, and comparison following the deployed MariaDB column collation; UNIQUE-index authority; acquisition as part of the row insert with no separate claim-acquisition commit and no interval in which an active Job Card lacks its claim; duplicate exception treatment (MariaDB 1062 translated into a stable domain rejection such as `ActiveJobCardAlreadyExists`, with no user-facing message prescribed); the lifecycle release requirement and single-transaction boundary; retry policy (1062 never automatically retried; bounded retry with backoff only for 1205 and 1213; no dependence on process-local locks); timeout reconciliation (database state authoritative, timeout does not imply rollback, caller must query the current active Job Card before replacement, 1062 alone does not prove the original request succeeded and must not be classified as idempotent success, request correlation or equivalent ownership evidence required); migration requirements (derive claims for active records, NULL for terminal records, duplicate detection before index creation, explicit governed remediation, no silent deletion/termination/automatic winner selection, index only after resolution, visible failure if duplicates remain, historical terminal records coexisting via multiple permitted NULLs, with probe table names noted as non-production); and rejected alternatives. Marked Section 13.1's atomicity requirements as validated at the database-transaction level and recorded that submit and cancel introduce no intermediate commit. Added Section 13.2, a normative terminal-release boundary preserving the approved controlled `discard()` and `cancel()` lifecycles without bypassing standard Frappe `docstatus`, hooks, permissions or lifecycle behavior, explicitly **not** selecting the validation harness's raw guarded SQL statement as the production implementation, and recording the concurrent-terminal requirements (one winner, deterministic already-terminal result for the loser, terminal reason never overwritten, claim released exactly once, no premature replacement window) — with the exact coordinating mechanism left delegated to the still-open terminal-reason persistence gate, which this validation does not close. Added tests T-51 through T-70 covering every material validated scenario. Updated the Pre-Publication Gate Checklist to mark complete: race-safe uniqueness mechanism selected and validated; database UNIQUE enforcement under concurrent creation; atomic release and replacement ordering at the database-transaction level; migration duplicate detection and index-failure behavior; and privileged execution unable to bypass the database constraint — while adding an unchecked item for the exact production coordination of claim release with the controlled terminal actions. Added Section 24.2 recording the validation evidence, environment baseline, and the manifest (`manifest.csv`, SHA-256 `52f269358d44ceea72c7562cdb6ab6daa42d457a8fa721ec454b7ef197adb8f2`, 12 evidence files including the manifest, 11 listed artifacts, 327-record structured event log, disposable out-of-repository path, secret-scanned, no credentials retained, raw evidence not added to Git). Terminal-reason persistence mechanism selection and validation, the Artwork production gate, the exact app-module decision, the provisioning-invariant enforcement and audit procedure, the status/`docstatus` implementation confirmation, the delete-capability implementation confirmation, the controlled terminal-action API implementation validation, and every other previously unchecked checklist item all **remain open**. No approved Project Owner design decision was changed; the technical name, business term, custom-app ownership, lifecycle/status model, Submitted Sales Order eligibility, one-active-per-Sales-Order rule, Company/Tenant distinction, Artwork two-track treatment, sharing-disabled and bulk-import-disabled policies, product-surface boundaries and excluded Tier B scope are all unchanged. No Business Review was repeated and no new Architecture Review disposition was recorded. This document **remains Draft**, targets Published, and **remains not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. No other tracked document was modified by this task beyond the companion System Design and Documentation Status synchronizations. |
| 0.5 | 2026-07-30 | Terminal-Reason Persistence Closure Evidence Synchronization | Factual synchronization recording the completed terminal-reason persistence and controlled terminal-action validation (2026-07-30), accepted disposition "Terminal-reason persistence gate closed — controlled lifecycle-context mechanism recommended", validated against Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` and ERPNext `a5de60c357d531cb31da093f0b86301776965173` (both reported 16.29.0) on Python 3.14.2, Node v24.13.0, MariaDB 10.6.27, Redis 6.2.23, InnoDB, REPEATABLE-READ, autocommit disabled for tested request transactions, Gunicorn with four workers, one background worker, independent database connections, a localhost-only disposable site and synthetic data only; exact Git commits authoritative, mutable branches, version strings and container tags not. Updated the governing System Design reference to Approval, Version 1.3. Finalized `terminal_reason` in Section 9 as Small Text with database intent `TEXT NULL`, mandatory only for a successful controlled terminal action, plain text only, an application maximum of 500 Unicode code points rejected before lifecycle mutation and never silently truncated, read-only outside the controlled action, visible after termination, immutable after successful termination, non-importable, excluded from bulk edit and unindexed — closing Finding F-3. Added `terminal_request_id` as Data with database intent `VARCHAR(140) NULL` and a single-column UNIQUE index, application format canonical lowercase UUIDv4 of exactly 36 characters supplied by the caller before execution, mandatory for every controlled terminal action, blank on active records, immutable, written exactly once with successful termination, accepted only as a controlled-action argument and never through document-field mutation, non-importable and excluded from bulk edit. Recorded explicitly that **no** separate `terminal_action` field is added (terminal action is derived from terminal status: Discarded means discard, Voided means cancel) and that **no** dedicated terminal-actor or terminal-timestamp fields are added (actor uses `modified_by`, timestamp uses `modified`, with Version history supplemental only where tracking is enabled and never treated as guaranteed authoritative storage). Extended the field mutability matrix and immutability rules so terminal metadata is never client-controlled and any unsupported mutation through ordinary save, REST resource updates, standard document method endpoints, `frappe.client.cancel`, direct `discard()`/`cancel()`, background code, Administrator or `ignore_permissions=True` is rejected or restored, noting that ordinary `validate()` remains necessary for non-terminal saves but is not sufficient for terminal enforcement, that the database `active_key` UNIQUE constraint remains independently authoritative, and that raw SQL by a database administrator remains outside normal application guarantees. Rewrote Section 14.1 with the validated controlled-action specification (mutation-only method, authentication required, Guest rejected, stable authorization failure, one operation shared by Desk, restricted API and background callers, no client-side independent lifecycle path, no early commit, final state returned only after successful lifecycle execution, idempotent replay explicitly identified, permitted state combinations, and the permission requirements including governed discard and void/cancel capabilities, effective Company User Permissions, a privileged but non-exempt Administrator, `ignore_permissions=True` unable to bypass mandatory reason or request correlation, and no `has_permission` or `permission_query_conditions` hook required). Rewrote Section 15 with the pinned lifecycle findings (Section 15.0), the selected controlled lifecycle-context mechanism (Section 15.1) including the transaction-authoritative locking-read rule, the private transient lifecycle context, and the discard and cancel coordination requirements; the terminal-reason validation rule (Section 15.2); idempotency, error, retry and timeout treatment (Section 15.3); transaction guarantees (Section 15.4); migration requirements (Section 15.5); and rejected alternatives (Section 15.6). Recorded that enforcement must be scoped to the custom PrintHub DocType controller or an equivalently narrow DocType-specific adapter and that a broad global `doc_events` hook is not prescribed as the production mechanism. Added permanent tests T-71 through T-100 covering every material validated scenario. Updated the Pre-Publication Gate Checklist to mark complete: terminal-reason persistence mechanism selected and validated; `terminal_reason` field type, storage and application bound confirmed; terminal request correlation and timeout reconciliation validated; controlled discard coordination validated; controlled cancel coordination validated; atomic reason, lifecycle, `docstatus` and active-key release validated; direct lifecycle and REST bypass protections validated at the design/evidence level; controlled terminal-action API and permission behavior validated; concurrent terminal-action and idempotent replay behavior validated; and terminal metadata migration behavior validated — additionally marking the pre-existing Finding F-3 and Finding F-9 checklist items closed by the same evidence to avoid self-contradiction, and adding unchecked items for production implementation of the controller/service mechanism and for production security and acceptance execution. Added Section 24.3 recording the validation evidence, runtime baseline, validated coverage, the non-blocking observation that rendered browser UI was not directly exercised, and the manifest (`manifest.csv`, SHA-256 `cc119cf796062ba78871ecc56507d08f1e9a15fa5384ae9aedbb29b47b5e85a2`, 18 evidence files including the manifest, 17 listed artifacts, 251-record structured event log, disposable out-of-repository path, sanitized and secret-scanned, no credentials retained, raw evidence not added to Git). The Artwork production enforcement or explicit demo-only classification, the exact app-module decision, the provisioning-invariant enforcement and audit procedure, the status/`docstatus` implementation confirmation, the delete-capability implementation confirmation, production implementation and production security/acceptance execution, document Architecture Review, Business Review and Project Owner lifecycle requirements, documentation-reference synchronization, and every other currently open gate all **remain open**. No approved Project Owner design decision was changed; the technical name, business term, custom-app ownership, approved lifecycle/status model, Submitted Sales Order eligibility, exactly-one-active rule, selected nullable active-key mechanism, Company/Tenant distinction, Artwork two-track treatment, sharing-disabled and bulk-import-disabled policies, product-surface boundaries and excluded Tier B scope are all unchanged. No Business Review was repeated and no new Architecture Review disposition was recorded. This document **remains Draft**, targets Published, and **remains not safe for coding**; Publication remains **Not Granted**; no implementation authorization was granted. No other tracked document was modified by this task beyond the companion System Design and Documentation Status synchronizations. |
| 0.6 | 2026-07-31 | Bounded Reference Correction (ART-ARCH-F10) | Citation-only correction arising from the formal Architecture Review of the Artwork production-authority documentation package. Replaced the stale `docs/blueprint/05_Domain_Model.md` "line 141" citation in Section 12 with a stable citation by document, section and rule wording, because the prior literal line number became stale after that document's 2026-07-31 cardinality correction. This is a bounded reference correction only: **no other content in this document was changed**; the Production Artwork Set field is **not** added by this entry; the Artwork gate is **not** claimed closed; the Draft Artwork authority schema is **not** synchronized into this direct coding specification — that synchronization remains explicitly deferred until the Artwork corrections pass targeted Architecture and Business re-review, the Artwork runtime validations are authorized and executed, and the resulting Artwork documents receive their governed lifecycle treatment. Status remains Draft; target lifecycle remains Published; Publication and Implementation Authorization remain **Not Granted**; this document remains **not safe for coding**. No implementation authorization was granted. No other tracked document was modified by this entry beyond the companion System Design's identical citation correction and the Documentation Status synchronization. |
| 0.8 | 2026-08-19 | Governing Reference Update — Job Card and Artwork System Design Approval Synchronization | Bounded governing-reference synchronization only, per `../Documentation_Workflow.md` Section 8 ("Minor Updates — Clarifications, added examples, or non-contradictory expansions are minor updates, incrementing the MINOR version"), consistent with this specification's own precedent for identical prior updates (e.g., Version 0.6). Recorded that the Project Owner granted independent Document Lifecycle Approvals on **2026-08-19** to both `../implementation/JobCard_TierA_System_Design.md` (Draft, Version 1.5 → **Approval, Version 1.5**; prior historical baseline Approval, Version 1.4) and `../blueprint/18_Artwork_Management.md` (Draft, Version 1.1 → **Approval, Version 1.1**; prior historical baseline Approval, Version 1.0), each following completed Architecture Review, Business Review, Documentation Governance verification, and independent verification with all applicable local findings Closed. Updated Section 3 and Section 26's current-state citations of both governing documents accordingly. **No conceptual field, state, DocType, controlled operation, permission, test, migration rule, or gate was modified.** This specification remains **Draft, Version 0.8**, targets Published, and remains **not safe for coding**; its own Project Owner lifecycle approval, Publication, and Implementation Authorization all remain **Not Granted**; the Artwork production gate and every other open pre-Publication gate remain **Open**; no implementation was authorized; no source was inspected or modified; no environment, test, or migration was run. No Architecture Review Register item was created or modified; no AR identifier was invented; AR-003 was not resolved or modified. |
| 0.7 | 2026-08-13 | Artwork Reference Contract Reconciliation | Bounded synchronization with the companion `../implementation/JobCard_TierA_System_Design.md` Section 10.3 (**Draft, Version 1.5**; prior approved baseline Approval, Version 1.4; its Version 1.5 reconciliation is Pending Architecture Review and Business Review) and the governing `../blueprint/18_Artwork_Management.md` Section 13 and Sections 16.1/16.2 — all part of that document's fully reviewed **Approval, Version 1.0** baseline. **No content was drawn from its Version 1.1 Section 7.2 addition**, which is Pending targeted Architecture Review and Business Review. Corrected Section 12's "No Artwork field exists in this Draft specification" statement, which predated the governed Artwork reference contract. Added Section 12.1 specifying **exactly one** conceptual field, `production_artwork_set` (Link → the proposed `PrintHub Production Artwork Set`, name pending governed naming treatment): not schema-required at creation, so Registered records may exist before Artwork approval; mandatory for a successful Registered → Released transition; assigned server-side only during the controlled Release transition; immutable after Release; not client-writable, importable, or bulk-editable. Recorded that only an **Approved for Production** Set may authorize a new release; that Draft, Submitted, Rejected, Superseded and Withdrawn Sets never authorize a new release; that a **Superseded** Set does not invalidate an existing binding, so an already Released or In Progress Job Card retains its historical link and may continue; that a **Withdrawn** Set follows the existing progression-blocking behaviour with controlled `discard()` and a replacement Job Card; that Completed history is never rewritten; and that **no silent rebinding** occurs. Explicitly reaffirmed that **no** Artwork Revision child table, copied file hash, copied approval actor, copied approval timestamp, copied revision collection, Proof reference, or Customer Approval Evidence reference is introduced, and that **no field beyond the single governed conceptual field was invented**. No existing Job Card lifecycle, status model, `docstatus` treatment, uniqueness mechanism, terminal-action design, permission rule, test, or exclusion was changed; no new test row or pre-Publication gate was marked complete. **No implementation is claimed and no gate is closed** — the Artwork production gate remains **open**, and this document remains **Draft**, **not Published**, **not safe for coding**, and not production-capable; Publication and Implementation Authorization remain **Not Granted**. **Bounded reference correction included in this same 0.7 change:** corrected a **pre-existing stale current-state citation** in Section 3 (Governing Decisions and Source Documents), which had described the companion System Design as "Approval, Version 1.3" — stale since that document reached Version 1.4 on 2026-07-31, and unrelated to the Artwork reconciliation. Section 3 now records the System Design as currently **Draft, Version 1.5** with prior approved baseline **Approval, Version 1.4**; that Version 1.5 holds the Project Owner-authorized Production Artwork Set reference reconciliation and **awaits the applicable Architecture Review and Business Review** (Pending, with no review completion claimed); that **the Approval 1.4 baseline does not automatically approve the Version 1.5 additions**; and that **no implementation, publication, or runtime-validation authority follows**. Historical revision-history entries citing Approval 1.3 or Approval 1.4 were **preserved unchanged**, as they correctly describe the state at their respective dates. No version increment beyond 0.7 was made. No Architecture Review Register item was created or modified; no AR identifier was invented; AR-003 was not resolved or modified. **Dedicated Job Card Version 1.5 review and corrections applied within this same Version 0.7 revision (2026-08-13).** A dedicated read-only review of the Version 1.5 reference contract was completed 2026-08-13 (Section 26): **Architecture Review — Accepted with non-blocking corrections; Business Review — Accepted; Documentation Governance — Passed with non-blocking corrections.** Corrections applied here without a version increment, since Version 0.7 remains uncommitted: **JC15-ARCH-F1** — Section 14's Release entry now distinguishes the demo-only manual Artwork check from the production-capable obligation to resolve, validate and server-side assign an Approved Production Artwork Set under Section 12.1, citing `../blueprint/18_Artwork_Management.md` Section 14 for release-time validation and prescribing no endpoint or mechanism; **JC15-ARCH-F2** — Section 12.1 now cites that same governing Section 14 release contract without duplicating it; **JC15-ARCH-F3** — Section 12.1 now scopes the Set-mandatory rule to the **production-capable track**, recording that the demo-only exception remains governed by its manual out-of-system check until the Artwork production gate closes, is not production authority, and cannot support real production reliance; **JC15-GOV-F1** — the stale "unreviewed / Pending review" description of Artwork Version 1.1 Section 7.2 is replaced with its accurate status (reviewed and accepted 2026-08-13, corrections independently verified, no material finding remaining, **Artwork 1.1 remains Draft** pending a separate Project Owner lifecycle decision), while recording that this contract does not depend on Section 7.2. **No JC15 finding is marked Closed; closure awaits a separate targeted read-only post-correction verification.** These are **local review labels, not Architecture Review Register identifiers**. No conceptual field beyond the already-governed `production_artwork_set`, and no state, DocType, API, route, workflow, implementation mechanism, test row or pre-Publication gate, was added, changed or closed; this specification remains **Draft, Version 0.7**, **not Published**, **not safe for coding**; **review acceptance is not lifecycle Approval**. **Post-correction verification and JC15-PCV corrections applied within this same revision (2026-08-13).** An independent read-only post-correction verification (Section 26) recorded **JC15-ARCH-F1, JC15-ARCH-F2 and JC15-GOV-F1 Closed by verified correction** and **JC15-ARCH-F3 Partially Closed**, raising **JC15-PCV-F1** (Sections 12.1 and 14 here, and Section 10.3 of the companion System Design, implied the Approved-Set obligation only "takes effect" once the Artwork production gate closes, and that gate closure alone activates the production-capable track) and **JC15-PCV-F2** (two stale "Pending review" premises in `../Documentation_Status.md`). **JC15-PCV-F1 is corrected here and in the companion System Design, and JC15-PCV-F2 in `../Documentation_Status.md`, within this same change**: Sections 12.1 and 14 now record that the Approved-Set contract **is already a normative production-capable design requirement**, that a **Superseded or Withdrawn Set can never authorize a new release**, that operational reliance additionally requires Published specifications, separately authorized and completed implementation, validation under separate authorization, explicit Artwork production gate closure and separate production authorization, and that **closing that gate alone publishes nothing, implements nothing, authorizes nothing and activates nothing**; the demo-only exception remains visibly non-production and **can never become production authority**. **JC15-ARCH-F3, JC15-PCV-F1 and JC15-PCV-F2 are not marked Closed; their closure awaits a separate read-only verification.** No business behavior, Artwork normative rule, conceptual field beyond the already-governed `production_artwork_set`, state, DocType, API, route, workflow, implementation mechanism, test row or gate was changed, added or closed; no version or status changed; no document was promoted. |
