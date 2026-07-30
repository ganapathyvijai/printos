# PrintHub Job Card Tier A — DocType Specification

## 1. Document Control

Version:
0.4

Status:
Draft

Date:
2026-07-30

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

- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — **Approval, Version 1.2** (approved architecture/design authority for this specification, per Project Owner Document Lifecycle Approval granted 2026-07-29; Version 1.1 records the 2026-07-30 P-2 closure evidence synchronization and Version 1.2 the 2026-07-30 race-safe uniqueness closure). This Approval does **not** make this DocType Specification Published or safe for coding; the remaining pre-Publication gates (Section 24) stay open.
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
| `terminal_reason` | Small Text (**proposed; confirmation required before Publication — Finding F-3**) | — | Required only for controlled discard/cancel; immutable after the terminal action; exact persistence treatment pending pre-Publication validation (Section 15) |

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
| `terminal_reason` | N/A | N/A | N/A | N/A | Written only by the terminal action; immutable afterward |
| `active_key` | Server-derived | Server-derived | Server-derived | Server-derived | Released to NULL by the terminal action |

Submitted and terminal records are immutable except for framework-permitted metadata (e.g., comments, attachments per Section 18).

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

- **No Artwork field exists in this Draft specification.**
- **Production-capable Publication remains blocked** until Artwork approval is server-verifiable, or the governing Business Rule ("A Job Card cannot begin production without approved Artwork," `05_Domain_Model.md` line 141) is formally changed through separate Architecture Review, Business Review, and Project Owner approval.
- A **demo-only** specification track may retain a **documented manual check before the Registered → Released transition** — see `JobCard_TierA_System_Design.md` Section 10.2.
- Demo-only scope must be **visibly marked non-production** wherever it appears; it does not satisfy the production-capable requirements defined in Section 25 (Publication Classification), specifically Section 25.2.

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

- **Release** — transitions Registered → Released; requires the acting user hold the "release" capability; if the demo-only Artwork track is in effect, requires the recorded manual Artwork check.
- **Begin Progress** — transitions Released → In Progress; requires the "mark In Progress" capability.
- **Complete (Submit)** — transitions In Progress → Completed via native Submit; requires the "complete" capability; triggers native `docstatus = 1` and the associated cancelled-link/backlink protections.
- **Discard with reason** — transitions any Draft-`docstatus` status to Discarded; requires the "discard" capability; requires `terminal_reason` to be atomically persisted before `discard()` is invoked (Section 15).
- **Void/cancel with reason** — transitions Completed to Voided; requires the "void" capability; requires `terminal_reason` to be atomically persisted before `cancel()` is invoked (Section 15).
- **Delete (Finding F-6)** — permitted only while `status = Registered` and `docstatus = 0`, and the record has not been Released; requires the narrowly scoped "delete erroneous Registered record" capability, distinct from discard/void/administer; prohibited from Released, In Progress, Completed, Discarded, or Voided; requires an explicit server-side lifecycle guard, not merely a client-side rule.

### 14.1 Controlled Terminal-Action API Policy (Finding F-9)

- The controlled discard/cancel action may require an explicitly exposed server method; exact method name and exposure mechanism remain delegated.
- It must **not** become a permission bypass: API callers require the same discard or void capability as Desk users.
- The server action must independently validate: current status; current `docstatus`; terminal reason; permission capability; Sales Order and Company integrity where applicable; and uniqueness-claim release behavior (Section 13.1).
- **Ordinary REST field updates must not substitute for the controlled action.**
- Method exposure, CSRF/authentication behavior where applicable, and permission enforcement are included in the pre-Publication REST/API validation gate (Section 24).

This document does not claim the method has been implemented or validated.

---

## 15. Terminal-Reason Persistence

**Normative requirement:** the terminal reason must be validated as present, then persisted, then the corresponding native action (`discard()` or `cancel()`) invoked — all within the same atomic server-side transaction — so that the reason is guaranteed stored before the terminal transition completes, and is immutable thereafter.

**Native Frappe `discard()` and `cancel()` do not themselves capture or prompt for a custom reason field** — this must not be assumed by any future implementation.

**Exact v16-compatible persistence mechanics: Pending pre-Publication technical validation.**

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
- [ ] Terminal-reason persistence mechanism selected and validated
- [ ] Exact production coordination of claim release with the controlled `discard()` and `cancel()` actions confirmed
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
- [ ] `terminal_reason` fieldtype, length, and storage behavior confirmed against the approved terminal-action design (Finding F-3)
- [ ] Status/`docstatus` consistency invariant (Section 8.1) confirmed testable and enforced, including direct API/field-level bypass rejection (Finding F-1)
- [ ] Active-key terminal atomicity (Section 13.1) mechanism selected and validated, including concurrent-replacement behavior (Finding F-2)
- [ ] Delete capability boundary (Section 14) confirmed limited to eligible Registered records, with denial-after-Release validated (Finding F-6)
- [ ] Artwork-track and direct-API-bypass test coverage (tests T-29 through T-40) executed (Findings F-4, F-5)
- [ ] Controlled terminal-action API exposure and permission enforcement (Section 14.1) validated (Finding F-9)

### 24.1 P-2 Validation Evidence

- **Dedicated execution** — 2026-07-29, disposition "P-2 Partially Closed — additional validation required."
- **Narrow closure execution** — 2026-07-30, accepted final disposition "P-2 Closed — standard mechanisms sufficient."
- **Environment:** Frappe `frappe/frappe@06613fc60b44d5736007ae3107cdab029b2ae045` (reported v16.29.0); ERPNext `frappe/erpnext@a5de60c357d531cb31da093f0b86301776965173` (reported v16.29.0); Python 3.14.2; Node v24.16.0; MariaDB 10.6; Redis 6.2-alpine; Debian 12, linux/x86_64; multi-worker Gunicorn 23.0.0 (narrow closure); localhost-only disposable sites; no production or personal data. Docker image evidence from the narrow closure (`frappe/bench:latest` image ID beginning `sha256:c66af151`, `mariadb:10.6` beginning `sha256:114d40be`, `redis:6.2-alpine` beginning `sha256:ec5e187c`) is recorded for traceability only — mutable image tags are **not** the source-of-truth pins; the verified Git commits above are authoritative.
- **Evidence manifest:** `manifest.csv`, SHA-256 `d59a33b96877fd784bb51f0f837bedf1957d41424061d11e8d27e9449b5ec456`, 26 artifacts, every reported narrow-closure test mapped to at least one artifact, all artifacts sanitized and secret-scanned (no passwords, cookies, Authorization headers, session IDs, database credentials, or CSRF tokens retained). Evidence is retained outside this repository under `/tmp/printhub-p2-closure-20260730/evidence/` — this path is **disposable and not a permanent repository artifact**; the durable record is this evidence summary together with the exact source pins, environment details, and manifest digest above, plus the accepted disposition. The raw evidence directory is not added to Git.
- **Standard-permission conclusion:** no standard-permission gap requiring `permission_query_conditions` or `has_permission` was reproduced against the tested surfaces (Section 17).

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
