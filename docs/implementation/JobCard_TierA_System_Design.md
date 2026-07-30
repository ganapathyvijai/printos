# PrintHub Job Card Tier A — System Design

## 1. Document Control

Version:
1.2

Status:
Approval

Date:
2026-07-30

Owner:
PrintHub Architecture Team

Target Lifecycle:
Approval

Document Class:
Architecture and design authority document. This is **not** a direct coding specification — see `docs/database/JobCard_TierA_DocType_Specification.md` for the document intended to become the direct coding specification once Published.

---

## 2. Purpose

This document records the Project Owner-approved design baseline for PrintHub Job Card Tier A — the minimum custom `printos_core`-owned production work-unit record — and defines its business purpose, aggregate boundary, lifecycle, status model, Sales Order relationship, permission approach, and the open technical gates that remain before the companion DocType Specification may be Published. It translates the accepted analysis-only design proposals produced during this session's Job Card Tier A review into a single governed Draft document.

---

## 3. Governance Authority

This document is subordinate to and does not amend:

- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) — Draft — Open Register, Version 0.4; AR-001 and AR-002 Resolved / Critical / Blocks Development: No; AR-003 through AR-011 unchanged.
- [../decisions/ADR-001-ERPNext-Framework.md](../decisions/ADR-001-ERPNext-Framework.md) — Accepted; ERPNext v16 / Frappe v16 governed target.
- [../decisions/ADR-014-Production-Terminology.md](../decisions/ADR-014-Production-Terminology.md) — Accepted; PrintHub Job Card is a custom, print-domain concept distinct from ERPNext's Manufacturing Job Card.
- [../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md](../decisions/ADR-015-Tenant-Company-Multi-Tenancy-Model.md) — Accepted, Version 1.0.
- [Architecture_Freeze.md](Architecture_Freeze.md) — Approval, Version 1.2. Full Architecture Freeze remains **not achieved**.
- [../roadmap/01_Development_Roadmap.md](../roadmap/01_Development_Roadmap.md) — Approval, Version 1.2. Every workstream remains **Not Authorized**.
- [Module_Dependency_Matrix.md](Module_Dependency_Matrix.md) — Draft, Version 0.5.
- [../blueprint/05_Domain_Model.md](../blueprint/05_Domain_Model.md), [../blueprint/09_PrintOS_Modules.md](../blueprint/09_PrintOS_Modules.md) — business-purpose evidence.

This document does not resolve any Architecture Review item, does not amend any Accepted ADR, does not promote any other document's lifecycle status, and does not authorize implementation.

---

## 4. Project Owner Design Decision Baseline

The Project Owner approved the recommended Job Card Tier A design defaults (decision set O-1 through O-27, produced during this session's design-correction analysis) on **2026-07-29**.

**This approval authorizes documentation drafting only.** It explicitly does **not** authorize:

- `printos_core` scaffolding;
- application code;
- DocType creation;
- hooks;
- fixtures;
- migrations;
- site provisioning;
- prototype execution;
- Publication of this document or the companion DocType Specification;
- implementation of any kind;
- production use.

This document has since completed Architecture Review, targeted Architecture Re-review, Business Review, and Project Owner document lifecycle Approval (see Section 27). **No implementation authorization has been granted** by the Owner's design-baseline approval, by the drafting of this document, or by this document's lifecycle Approval.

---

## 5. Official v16 Evidence Baseline

Accepted, version-pinned source evidence, established and not reopened by this document:

**Frappe Framework** — repository `frappe/frappe`, branch `version-16`, commit `06613fc60b44d5736007ae3107cdab029b2ae045`, release line v16.29.0.

**ERPNext** — repository `frappe/erpnext`, branch `version-16`, commit `a5de60c357d531cb31da093f0b86301776965173`, release line v16.29.0.

Accepted conclusions drawn from that evidence:

- ERPNext owns the technical DocType name `Job Card`, in module Manufacturing, submittable, coupled to Work Order, Workstation, Operation, BOM, and Company — therefore PrintHub uses the distinct technical name `PrintHub Job Card`.
- Sales Order is submittable and has a required Link field `company` targeting Company.
- Missing Link targets are rejected by Frappe.
- Links to cancelled targets are rejected on insert and ordinary save only when the linking DocType is itself submittable.
- A Draft backlink does not block target cancellation; a Submitted backlink blocks target cancellation; a Cancelled backlink does not block.
- Draft documents may be discarded via `discard()`; Submitted documents may be cancelled via `cancel()`; these are distinct native actions.
- `has_permission` hooks are deny-only; a falsy return (including `None`) denies access.
- `UnitTestCase` and `IntegrationTestCase` are the current v16 test classes; `FrappeTestCase` is deprecated.

No unverified source claim is introduced by this document.

---

## 6. Tier A Scope

Tier A is limited to: one custom `printos_core`-owned `PrintHub Job Card` DocType; Company scoping; linkage to the applicable, Submitted ERPNext Sales Order; minimum operational identity and description; the approved Registered/Released/In Progress/Completed lifecycle and its Discarded/Voided terminations; role and document permissions; server-side validation; auditability; automated tests; standard Frappe Desk and API behavior (per the approved surface policy, Section 19); and app-owned schema/migration treatment.

---

## 7. Explicit Exclusions

Tier A explicitly excludes: ERPNext Manufacturing Job Card reuse; Machine ownership or fields; Material or Product Template ownership; material consumption; BOM behavior; Quotation; Estimation; Enquiry; Job Card Tier B; Production Planning; Machine Scheduling; Tenant Override; global Customer identity; cross-tenant analytics; provider adapters; MachineIQ; public or plugin Marketplace capabilities; AI Assistant; product pricing; tenant provisioning; deployment implementation; Docker or CI implementation; and any Tenant field or Tenant DocType.

---

## 8. Business Purpose

Tier A supports the following observable business meanings:

- **Registered** — a production work-unit record has been created against a Submitted, confirmed Sales Order. Observable event: the record is inserted.
- **Released** — the work unit is authorized to proceed. Observable event: an authorized-role action.
- **In Progress** — an operator has declared that work has started on this unit. This is a **basic declaration**, not detailed shop-floor tracking — it asserts nothing about Machine assignment, material consumption, costing, or scheduling.
- **Completed** — an authorized user declares the Tier A work unit complete. **`Completed` is an authorized business declaration, not proof of physical production completion or of any Machine, Material, costing, or scheduling fact.** Those facts remain exclusively within Tier B and other excluded domains.

Tier A deliberately does not manage production scheduling, machine allocation, material usage, costing, or detailed shop-floor execution evidence.

---

## 9. Aggregate Boundary

- **Aggregate root:** `PrintHub Job Card`.
- **No child table** is included in Tier A.
- **External references:** Company and Sales Order (both owned elsewhere; not duplicated).
- **No Sales-Order-owned facts are duplicated** into the Job Card (e.g., no copied Customer identity or pricing).
- **No Artwork field exists in this Draft.** Approved Artwork is a governed hard gate (Section 10) but is not represented in the Tier A aggregate in this Draft.

---

## 10. Artwork Gate and Demonstration Exception

Approved Artwork is a governed hard gate, established by repository evidence:

- `docs/blueprint/05_Domain_Model.md` line 141: "A Job Card cannot begin production without approved Artwork" (Business Rule).
- `docs/implementation/Module_Dependency_Matrix.md`: "Approved Artwork is a hard gate before Job Cards production may begin, per Business Rules," and lists Approved Artwork among Job Card Tier A's own required prerequisites.
- `docs/implementation/Architecture_Freeze.md` §15: Tier A is "Defined from the Sales Order and Approved Artwork."

### 10.1 Production-Capable Track

Before the companion DocType Specification may be Published as **production-capable**:

- Approved Artwork must be represented through a governed, server-verifiable mechanism; **or**
- the governing Artwork prerequisite must be formally changed through separate Architecture Review, Business Review, and Project Owner approval.

**Manual enforcement is not sufficient for production-capable Publication.**

### 10.2 Non-Production Demonstration Exception

The Project Owner approved a temporary demonstration exception:

- Artwork approval **may be checked manually**, outside the system;
- the demonstration must be **explicitly labelled non-production**;
- **no real production reliance is permitted** under this exception;
- the unimplemented Artwork invariant is prominently recorded here and in the DocType Specification;
- **production-capable Publication and production authorization remain blocked** by this exception — it does not satisfy Section 10.1.

**Demonstration boundary:** the manual Artwork approval check occurs, if performed, **before the transition from Registered to Released.**

No Artwork field or invented Artwork DocType is added by this document.

---

## 11. Lifecycle Design

Business status is a distinct concept from Frappe `docstatus`. The approved lifecycle:

| Business Status | Frappe `docstatus` |
|---|---:|
| Registered | 0 (Draft) |
| Released | 0 (Draft) |
| In Progress | 0 (Draft) |
| Completed | 1 (Submitted) |
| Discarded | 2 (via `discard()`) |
| Voided | 2 (via `cancel()`) |

Submit occurs only at Completed. **Release is not finality.** `Discarded` and `Voided` both resolve to `docstatus = 2` but are reached by different native Frappe actions with different permission requirements and different automatic consequences (Section 15).

---

## 12. Status Transition Model

Permitted transitions:

- Registered → Released
- Released → In Progress
- In Progress → Completed (via Submit)
- Registered → Discarded (via controlled discard)
- Released → Discarded (via controlled discard)
- In Progress → Discarded (via controlled discard)
- Completed → Voided (via controlled cancel)

No reopening of a terminal state is defined. Native Amend is **not** used as a Tier A correction path — correction is via a Replacement Job Card (Section 16).

### 12.1 Status/`docstatus` Consistency Invariant (Finding F-1)

The following is a **normative requirement**, not merely descriptive: no Desk, REST, internal API, background-job, or ordinary document-save operation may ever produce a business `status` inconsistent with Frappe `docstatus`.

**Required valid mappings** (restated from Section 11):

| Business Status | Required `docstatus` |
|---|---:|
| Registered | 0 |
| Released | 0 |
| In Progress | 0 |
| Completed | 1 |
| Discarded | 2 |
| Voided | 2 |

- `status` is system-managed. **Hiding or making the field read-only in the client is insufficient** — server-side validation is mandatory.
- Any direct field assignment that bypasses a controlled transition (Release, Begin Progress, Complete, Discard, Void) must be rejected server-side.
- Ordinary REST resource updates must not be able to bypass transition validation.
- Background or privileged execution context must not silently bypass this invariant.
- Administrator remains a privileged identity (Section 20.1) but must still preserve this data-integrity invariant unless an explicitly governed repair procedure exists.

**Required transition safeguards:**

- **Insert** — a new record must begin as `Registered`; a client or API request cannot insert it directly as Released, In Progress, Completed, Discarded, or Voided.
- **Ordinary Draft save** — permitted: no status change; Registered → Released via the controlled Release transition; Released → In Progress via the controlled Begin Progress transition. Rejected: direct assignment to Completed, Discarded, or Voided; skipped transitions; backward transitions; unsupported status values.
- **Submit** — permitted only from In Progress; must result in `status = Completed` and `docstatus = 1`; no record may remain In Progress after a successful submit; no record may be Submitted with status Registered or Released.
- **Discard** — `discard()` permitted only from Registered, Released, or In Progress; the controlled action must establish the terminal reason, `status = Discarded`, and final `docstatus = 2`; a discard attempted without the controlled requirements must be rejected through the supported server-side lifecycle guard.
- **Cancel** — `cancel()` permitted only from Completed; the controlled action must establish the terminal reason, `status = Voided`, and final `docstatus = 2`; a Submitted record must not become cancelled while retaining status Completed.

The exact controller-method distribution implementing this invariant remains a delegated technical decision, but **the invariant itself is normative and must be testable** — see the companion DocType Specification's test inventory (Finding F-5).

---

## 13. Sales Order Eligibility and Multiplicity

- Eligible Sales Orders: **Submitted only** (`docstatus = 1`).
- **Exactly one active PrintHub Job Card per Sales Order.** "Active" means Registered, Released, In Progress, or Completed.
- A replacement Job Card is permitted only once the previous record has reached **Discarded** or **Voided**, and only after that terminal transaction has committed (Section 21.1).
- The exactly-one-active invariant is enforced by a **database UNIQUE constraint** on a nullable, server-derived active claim (Section 21) — not by application checking alone.
- One-to-many decomposition (e.g., per Sales Order line item) is **deferred** to later governed design — no discriminator for multiplicity exists within Tier A scope.

---

## 14. Company Scoping

- The Job Card's `company` must equal the linked Sales Order's `company`.
- Standard Frappe permissions (Role Permissions, Company User Permissions, server-side consistency validation) are the **validated baseline** — **P-2 is Closed** (Section 25): standard mechanisms proved sufficient on every tested surface (direct read; create/update; cross-Company reassignment denial; list query/reportview; Company and Sales Order Link search; REST collection/resource access; private attachment metadata, download, and authorized upload; unauthorized private-file upload denial; export; Administrator behavior). No custom `has_permission` or `permission_query_conditions` hook is required by current governed evidence (Section 17).
- P-2 validation surfaced a mandatory **provisioning invariant**, normative regardless of implementation mechanism: (1) every user granted an operational PrintHub Job Card role must receive at least one explicit Company User Permission as part of the same governed provisioning action; (2) multi-Company access is the explicit union of the Companies assigned through User Permissions; (3) removing a user's final Company User Permission must occur together with either removal of every operational Job Card role, or account suspension/disablement; (4) a user must never remain active with an operational Job Card role and zero Company User Permissions — this is an **invalid provisioning state** (a role-bearing user with no Company User Permission is unrestricted across Companies under standard Frappe behavior, not denied); (5) direct administrative role assignment outside the governed provisioning procedure must be controlled and auditable; (6) Administrator remains a privileged framework identity and is not constrained by ordinary Company User Permissions; (7) this invariant does not weaken Tenant isolation, which remains site/database based (Accepted ADR-015); (8) the invariant must be validated through provisioning and security acceptance tests before production. This invariant is **not** represented as a Job Card `has_permission` or `permission_query_conditions` hook; its exact enforcement mechanism is a delegated technical decision outside this document's scope.
- Site/database isolation, per Accepted ADR-015, supplies Tenant isolation. **No Tenant field is present or required.**

---

## 15. Sales Order Cancellation and Amendment

- Sales Order cancellation is **allowed** while the linked Job Card remains in a Draft-`docstatus` business status (Registered, Released, In Progress) — no native block applies at these states (accepted evidence: Draft backlinks do not block).
- Any subsequent Job Card insert, save, or progression attempted against a now-cancelled Sales Order is **rejected** by controller validation.
- A **Completed** (Submitted) Job Card **natively blocks** Sales Order cancellation (accepted evidence: Submitted backlinks block).
- To permit Sales Order cancellation or amendment once a Job Card has reached Completed, the Job Card must first be transitioned to **Voided**.
- **No automatic relinking** occurs when a Sales Order is amended.
- **Explicit replacement sequence:**
  1. Terminate the active Job Card (Voided, if Completed; Discarded, if pre-Completed) where required.
  2. Cancel or amend the original Sales Order.
  3. Create a replacement Job Card against the amended Sales Order.
  4. Rely on native document history and the recorded terminal reason for traceability — Tier A does not add a dedicated replacement-link field.

---

## 16. Correction and Replacement Model

| Scenario | Model |
|---|---|
| Erroneous Registered/Released/In Progress record | Controlled discard, with reason (Section 22) |
| Erroneous Completed record | Controlled cancel (Void), with reason |
| Terminal record (Discarded or Voided) | No further correction; a Replacement Job Card is the only path forward |
| Changed/amended Sales Order | Manual correction per the explicit sequence in Section 15 |
| Accidental duplicate | Prevented at creation by the uniqueness rule (Section 13, Section 21); if one exists, discard the duplicate with reason "Duplicate" |

Deletion (as distinct from discard) is permitted **only** for a clearly erroneous record before it acquires operational significance (i.e., before any meaningful save/business action) — not as a general correction mechanism for a saved, operationally significant Draft record, which uses `discard()` instead.

### 16.1 Delete Capability Boundary (Finding F-6)

To resolve the contradiction between this section (which permits deletion) and the Permission Capability Model (Section 17, which previously omitted it), the Tier A deletion boundary is defined precisely:

- Deletion may be considered **only** while `status = Registered` and `docstatus = 0`, and the record has **not** been Released.
- Deletion is **prohibited** from Released, In Progress, Completed, Discarded, or Voided — for all of those, controlled discard (or cancel, for Completed) is the only correction path.
- Deletion requires an explicit server-side lifecycle guard, not merely a client-side button rule.
- An unsaved form may be abandoned without creating or deleting a repository record at all — this is not "deletion" in the governed sense and requires no permission capability.
- Delete permission must **not** be broadly granted to ordinary operational users; exact role names remain delegated (Section 17).

This boundary is reflected as a distinct capability in Section 17.

---

## 17. Permission Capability Model

Capabilities (not final role names): view; create; edit Draft; release; mark In Progress; complete; discard; void; **delete erroneous Registered record before operational significance** (Finding F-6 — narrowly scoped, distinct from discard/void/administer, per Section 16.1's boundary); administer. Exact role names remain a delegated technical decision. **P-2 is Closed** (Section 25): standard Role Permissions, Company User Permissions, and server-side consistency validation proved sufficient across every tested surface, and no standard-permission gap requiring a custom hook was reproduced. Accordingly, no `has_permission` or `permission_query_conditions` hook is required by current governed evidence. Neither hook is described as prohibited forever — either would require a future concrete, reproduced standard-permission gap plus a separately approved, controlled design change before adoption. The mandatory provisioning invariant (Section 14) governs Company User Permission assignment and is not itself a custom permission hook.

---

## 18. Auditability Baseline

- Native `owner`, `creation`, `modified`, `modified_by` — required.
- Track Changes — required (enabled on the DocType).
- Controlled terminal reason (Section 22) — required, immutable after the terminal action.
- Comments/timeline — optional.
- No claim of legal compliance is made by this document.

---

## 19. API and Product Surface Policy

| Surface | Policy |
|---|---|
| Desk form | Included |
| Desk list | Included |
| REST resource API | Included with restriction — permission behavior validated, P-2 Closed |
| Attachments | Included with restriction — permission behavior validated, P-2 Closed |
| Document sharing | Disabled for Tier A |
| Bulk import | Disabled for Tier A |
| Export | Included, for authorized Company-scoped users |
| Print formats | Deferred |
| Reports | Excluded from Tier A — Configuration Studio retains ownership of future Report and Dashboard Definitions |

---

## 20. Security Boundaries

Cross-Company leakage, guessed record names, direct API access, list-query access, document sharing, attachments, privileged users, unsafe client-side-only validation, bulk import, export, and audit history are all in-scope security concerns for the companion DocType Specification's permission and validation sections; this document does not itself define final controls. This is not the dedicated production Security Review required by the Multi-Tenant Architecture approval.

**Race-safe uniqueness evidence summary (2026-07-30).** Accepted disposition: *Race-safe uniqueness gate closed — nullable active-key mechanism recommended.* Validated against the governed pins (Section 5) — Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` (reported 16.29.0) and ERPNext `a5de60c357d531cb31da093f0b86301776965173` (reported 16.29.0) — on Python 3.14.2, Node v24.13.0, MariaDB 10.6.27, Redis 6.2.23, InnoDB, transaction isolation REPEATABLE-READ, autocommit disabled for the tested request transactions, a multi-worker Gunicorn runtime with independent database connections, a localhost-only disposable environment, and synthetic data only. **Exact Git commits are authoritative; mutable branch names and container tags are not authoritative source pins** — the ERPNext branch tip had drifted and was explicitly re-pinned before testing. Coverage: two-way concurrent initial creation; higher-contention creation; losing-transaction rollback; winning-transaction rollback; duplicate request retry; Draft-like terminal release; Submitted-like cancellation release; replacement blocked before terminal commit; terminal rollback; wrong-owner release attempts; concurrent replacement; concurrent terminal actions; direct field-mutation bypass; REST mutation bypass; privileged and Administrator execution; clean index creation; duplicate-data migration failure; terminal historical records; field length and collation behavior; database connection loss; worker termination; lock wait; client timeout; and service restart. **116 concurrent attempts under the selected mechanism produced no committed duplicate.** Evidence manifest `manifest.csv`, SHA-256 `52f269358d44ceea72c7562cdb6ab6daa42d457a8fa721ec454b7ef197adb8f2`, 12 evidence files including the manifest, 11 listed artifacts, and a structured event log of 327 records covering the material RSU scenarios, retained outside this repository under the disposable path `/tmp/printhub-uniqueness-20260730/evidence/`. Evidence was secret-scanned; no credentials, tokens, cookies, session identifiers or database passwords were retained; **raw evidence must not be added to Git**, and the durable record is this documentation summary together with the source pins, environment baseline, manifest digest and accepted disposition.

**P-2 evidence summary:** cross-Company leakage, direct API access, list-query access, attachments (metadata, download, upload — authorized and unauthorized), export, and privileged-user (Administrator) behavior were each empirically validated against a genuine Frappe v16.29.0 / ERPNext v16.29.0 environment (exact commit pins, Section 5) across two sequential executions dated 2026-07-29 and 2026-07-30; the second, narrow-closure execution conclusively validated the previously inconclusive unauthorized private-file upload denial case (root cause was a local host tooling artifact, not a server-side or permission-model defect), and validated U-NOROLE and U-NONE behavior for both a Company-scoped and a Company-unscoped role-bearing user. No standard-permission gap requiring `has_permission` or `permission_query_conditions` was reproduced. Rendered Desk browser UI was not directly exercised, but its underlying list and document-load server paths were exercised via equivalent server-side requests — this is retained as a non-blocking observation, not an open gate. Document sharing and bulk import remain Disabled for Tier A (Section 19) and were not in scope for P-2.

### 20.1 Administrator Policy (Findings F-7 / F-10)

- Frappe `Administrator` is a privileged framework identity and may bypass ordinary Role and User Permission checks (accepted v16 evidence).
- Administrator is **not** a normal PrintHub operational role.
- Company User Permissions must **not** be described as restricting Administrator.
- Privileged access does **not** change Tenant isolation — Tenant isolation remains site/database-based (Accepted ADR-015), independent of any in-site permission bypass.
- Administrator operations must still preserve the Job Card's data-integrity and lifecycle invariants (Section 12.1) — privileged access is not a license to violate the status/`docstatus` consistency requirement through ordinary operation.
- Privileged repair outside normal lifecycle behavior requires a **separately governed operational procedure**, not an assumed silent bypass.
- Audit visibility of privileged changes must be validated before production.
- This document does **not** claim that every user holding the ERPNext System Manager role has the same unconditional bypass behavior as Administrator — that would require separate v16 evidence not established in this session.

---

## 21. Race-Safe Uniqueness Requirement

**Gate status: Closed** (validated 2026-07-30). Accepted disposition: *Race-safe uniqueness gate closed — nullable active-key mechanism recommended.*

- An **ordinary existence query alone is prohibited** as the final uniqueness-enforcement mechanism, because it is race-prone under concurrent requests. This was empirically reconfirmed: an existence check under REPEATABLE-READ cannot observe a concurrent uncommitted insert.
- The mechanism must be **transaction-safe**.
- **Selected mechanism:** a **nullable, server-derived active claim on the PrintHub Job Card row, protected by a database UNIQUE constraint.**

**Conceptual schema (normative; not an implementation):**

| Aspect | Requirement |
|---|---|
| Field | `active_key` |
| Field type | Data-compatible nullable string |
| Database representation | `VARCHAR(140) NULL` |
| Constraint | Full-column, single-column UNIQUE index |
| Storage engine | InnoDB |
| Site identity | Implicit (Tenant isolation remains site/database based) |
| Tenant field | Prohibited |
| Company in claim | **Excluded** |

The claim is the **site-local Sales Order identity**. Company must **not** be included in the claim: doing so would weaken the invariant to one active Job Card *per Company per Sales Order*, contradicting Section 13.

**Canonical claim value (normative):**

- `active_key` must equal the **exact, successfully validated `sales_order` Link value** while the Job Card is active.
- The Sales Order Link must resolve successfully **before** the claim is assigned.
- Surrounding whitespace in a submitted Sales Order identifier must be **rejected**, not silently converted into a different identifier.
- **No application-level case transformation is prescribed**, and none may be applied unless a later controlled validation proves it identity-preserving for every permitted Sales Order name.
- Database comparison follows the deployed MariaDB column collation.
- The claim must not be independently editable or supplied by a client.

**Server invariant (normative):**

- For Registered, Released, In Progress, Completed: `active_key == sales_order`
- For Discarded and Voided: `active_key IS NULL`
- **No other persisted pair is valid.**

**`active_key` is internal, system-managed and server-derived** — excluded from ordinary client editing, editable REST input, import and bulk editing; it is **not a user-facing business field**, and is recomputed and validated on every persistence path (insert, ordinary save, submit, discard, cancel, REST execution, background execution, and Administrator/privileged server execution). Client-supplied values are never trusted. Permission bypass does **not** bypass the database UNIQUE constraint or the derived-field invariant — validated under concurrent privileged execution.

**Acquisition:** the claim is acquired as part of **inserting the Job Card row** — the server assigns the validated Sales Order identity to `active_key`. The UNIQUE index is the **authoritative** concurrent-integrity control; a prior existence query is not sufficient; **no separate claim-acquisition commit is permitted**; and there must be no interval in which an active Job Card exists without its active claim. A duplicate surfaces as MariaDB error **1062**, reported by the governed Frappe version as an integrity/unique-validation failure, and is to be translated by the future application into a stable domain rejection such as `ActiveJobCardAlreadyExists`. No user-facing message is prescribed here.

**Rejected alternatives:** a separate active-claim record or DocType (adds a second table, aggregate/migration/operational complexity, and separate claim-ownership and orphan-state concerns, with no integrity advantage over the same-row active key for this invariant); Sales Order row locking as the **sole** guarantee (works only while every path cooperates — omitting the lock produced concurrent duplicates, and it supplies no durable database constraint); ordinary pre-insert existence queries; process-local locks; client-side validation; Redis locking as the only integrity guarantee; and post-commit duplicate reconciliation. A row lock may later be used for orchestration or contention management, but **never as the sole uniqueness guarantee**.

This document records the selected mechanism as validated at the database and transaction level. It does **not** authorize implementation, and the exact production coordination with the controlled terminal actions remains delegated (Sections 22 and 25).

### 21.1 Terminal Atomicity Requirement (Finding F-2)

The following are normative. Each was **validated at the database-transaction level on 2026-07-30** against the governed v16 pins (Section 5):

- Acquisition of the uniqueness claim must be atomic with Job Card creation.
- Release or clearing of the uniqueness claim must be atomic with a **successful** Discard or Cancel.
- A failed or rolled-back terminal action must **not** release the claim.
- A successful terminal action must **not** leave the claim held.
- Replacement creation must not become possible until the prior terminal transaction commits.
- Concurrent replacement attempts must still produce at most one active record.
- Migration and recovery behavior must preserve this invariant.

**Validated lifecycle transaction boundaries.** On the governed Frappe version, **submit introduces no intermediate commit** and **cancel introduces no intermediate commit**; transactionally coupled claim release is therefore **feasible**. Terminal reason, terminal lifecycle outcome and `active_key` release must commit **in one database transaction**, with **no intermediate commit permitted**; failure at any point must restore the previous active record and its active claim; and replacement may succeed only after the terminal transaction commits.

**Terminal-release boundary (normative).** Draft termination must preserve the approved controlled `discard()` lifecycle, and Completed termination the approved controlled `cancel()` lifecycle. Standard Frappe `docstatus`, hooks, permissions and lifecycle behavior **must not be bypassed**. The raw guarded SQL statement used inside the disposable validation harness is **explicitly not selected** as the production terminal-action implementation — it existed only to demonstrate transactional coupling.

**Concurrent terminal actions (normative).** Only one concurrent terminal action may win; the losing action must fail or return a deterministic already-terminal result; the terminal reason must not be overwritten; the active claim must be released exactly once; and no replacement window may open before successful terminal commit. A guarded status/ownership predicate with affected-row verification may form part of the future mechanism, but only if integrated **without bypassing the standard Frappe lifecycle APIs**.

The exact mechanism coordinating terminal-reason persistence, `discard()`/`cancel()` invocation, idempotency, concurrent terminal actions and claim release **remains delegated to the separately open terminal-reason persistence validation gate (Section 22), which this validation does not close.**

An explicit concurrency test requirement — covering both terminal-action release and simultaneous replacement creation — is recorded in the companion DocType Specification's test inventory (Finding F-5/test T-22 and its expansion).

### 21.2 Retry, Timeout and Migration Requirements

**Retry policy (normative):** duplicate-key error **1062 is not automatically retried** — it represents a deterministic active-claim conflict. Lock-wait timeout **1205** and deadlock **1213** may receive a **small bounded retry with backoff**; every retry re-enters the same database uniqueness protection, and retry behavior must not depend on process-local locks.

**Client timeout or lost response (normative):** the database state is authoritative; a client timeout does **not** imply rollback; the caller must query the current active Job Card before attempting a replacement request; and a later 1062 does **not**, by itself, prove the caller's original request succeeded. Request correlation or equivalent ownership evidence is required before treating an existing record as the result of a timed-out request. **1062 alone must not be classified as idempotent success.**

**Migration (normative):** migration must populate or derive `active_key` for every existing active record; leave terminal records with NULL claims; run a duplicate-detection query **before** creating the UNIQUE index; identify every Sales Order holding more than one active record; require explicit governed remediation; **prohibit silent deletion, silent termination and automatic winner selection**; create the UNIQUE index only after duplicate resolution; and **fail visibly** if duplicates remain. Historical Discarded and Voided records may coexist for one Sales Order because multiple NULL values are permitted by the tested MariaDB UNIQUE semantics.

---

## 22. Terminal-Reason Requirement

A terminal reason is required for every controlled discard or cancel action. A **controlled server-side action must atomically persist the reason and then perform the appropriate native action** (`discard()` or `cancel()`) within the same transaction, so the reason is guaranteed saved before the terminal action completes, and becomes immutable afterward. **Native Frappe `discard()` and `cancel()` do not themselves prompt for or store a custom reason** — this must not be assumed. The exact v16-compatible persistence mechanics remain a delegated technical decision requiring validation before Publication (Section 25).

### 22.1 Controlled Terminal-Action API Policy (Finding F-9)

- The controlled discard/cancel action **may require an explicitly exposed server method**; its exact method name and exposure mechanism remain delegated.
- It must **not** become a permission bypass: API callers require the same discard or void capability as Desk users.
- The server action must independently validate: current status; current `docstatus`; terminal reason; permission capability; Sales Order and Company integrity where applicable; and uniqueness-claim release behavior (Section 21.1).
- **Ordinary REST field updates must not substitute for the controlled action.**
- Method exposure, CSRF/authentication behavior where applicable, and permission enforcement are included in the pre-Publication REST/API validation gate (Section 25).

This document does not claim the method has been implemented or validated.

---

## 23. Testing Strategy

Automated tests must cover creation, Sales Order eligibility and cancellation interactions, Company consistency, all permitted and prohibited transitions, terminal-reason capture and immutability, uniqueness (including a concurrency scenario), permission behavior across Desk/REST/list/export surfaces, and an install/migration smoke test. `IntegrationTestCase` is used for all database-, permission-, and lifecycle-touching tests; `UnitTestCase` is reserved for genuinely isolated, non-database logic. No test code is written in this document — see the companion DocType Specification for the full test inventory.

---

## 24. Documentation and Governance Gates

This document targets lifecycle **Approval**. The companion DocType Specification (`docs/database/JobCard_TierA_DocType_Specification.md`) targets lifecycle **Published**, since it is intended to become the direct coding specification. Direct coding may rely only on the Published DocType Specification, not on this document. Scoped implementation authorization remains a separate, later governance decision, required regardless of either document's lifecycle status.

---

## 25. Open Pre-Publication Technical Closures

The following remain open and must close before the companion DocType Specification may be Published:

- **P-2** — permission-behavior validation. **Closed** (2026-07-30): standard Company/User Permission mechanisms proved sufficient across Desk, REST, list, Link-search, attachment, and export surfaces; no custom permission hook required by current evidence; the mandatory provisioning invariant (Section 14) was added as a result.
- **Race-safe uniqueness mechanism** — selected and validated (Section 21). **Closed** (2026-07-30): the nullable, server-derived active claim protected by a database UNIQUE constraint is selected; database UNIQUE enforcement, atomic release and replacement ordering, migration duplicate-detection and index-failure behavior, and non-bypass under privileged execution were all validated.
- **Terminal-reason persistence mechanics** — selected and validated (Section 22). **Remains open.**
- **Attachment permission validation.** **Closed** (2026-07-30) — see P-2 evidence summary, Section 20.
- **REST/API permission validation.** **Closed** (2026-07-30) — see P-2 evidence summary, Section 20.
- **Artwork production gate** (Section 10.1) — closed, or the specification is explicitly classified demo-only (Section 10.2) with production-capable Publication remaining blocked. **Remains open.**
- **Direct specification track selection** — demo-only or production-capable — must be explicitly chosen before Publication.
- **Status/`docstatus` consistency invariant** (Section 12.1) — testable implementation confirmed, including direct API/field-level bypass rejection.
- **Active-key terminal atomicity** (Section 21.1) — **Closed at the database-transaction level** (2026-07-30), including concurrent-replacement ordering and terminal-rollback behavior. The **exact production coordination with the controlled `discard()`/`cancel()` actions remains open** and is carried by the terminal-reason persistence gate above.
- **`terminal_reason` fieldtype, length, and storage behavior** — confirmed against the approved terminal-action design (companion DocType Specification).
- **Delete capability boundary** (Section 16.1) — confirmed limited to eligible Registered records, with denial-after-Release validated.
- **Artwork-track and direct-API-bypass test coverage** — present in the companion DocType Specification's test inventory.
- **Controlled terminal-action API exposure and permission enforcement** (Section 22.1) — validated.

---

## 26. Shortest Safe Path

1. Project Owner design decisions (recorded, Section 4).
2. Draft design-document creation (this document and the companion DocType Specification).
3. Documentation Status census synchronization.
4. Architecture Review.
5. Business Review.
6. Project Owner document lifecycle approval.
7. P-2 (Closed, 2026-07-30) and race-safe uniqueness (Closed, 2026-07-30) plus the remaining pre-Publication technical closures (Section 25 — terminal-reason persistence and the Artwork production gate remain open).
8. Publication of the DocType Specification (track explicitly selected).
9. Separate, scoped implementation authorization.
10. `printos_core` scaffolding.
11. Tier A implementation and automated tests.
12. Migration/install validation.
13. Working ERP demonstration.
14. Project Owner modification feedback.
15. Controlled change handling.

---

## 27. Review Status

**Architecture Review (initial)**
- Date: 2026-07-29
- Disposition: **Corrections Required**
- Blocking findings: F-1 (status/`docstatus` consistency invariant), F-6 (delete-capability alignment) — both **closed in Version 0.2** (Sections 12.1, 16.1/17)
- Non-blocking findings F-2 through F-10 — addressed in Version 0.2

**Architecture Re-review (targeted)**
- Date: 2026-07-29
- Disposition: **Accepted with non-blocking observations**
- Confirmed F-1 and F-6 Closed; confirmed F-2 through F-10 correctly and consistently applied
- Residual observations: R-1 (the DocType Specification's stale System Design version reference — now closed by this promotion's companion reference-synchronization task), R-2 (optional test-row granularity in the DocType Specification's test inventory — remains open, non-blocking, deferred)
- **Neither residual observation blocks System Design Approval**

**Business Review**
- Date: 2026-07-29
- Disposition: **Accepted with non-blocking observations**
- No mandatory business correction identified
- Approved business baseline unchanged
- No repeat Business Review required — the corrections applied in Version 0.2 did not change business semantics, and this promotion changes no content the Business Review evaluated

**Project Owner Document Lifecycle Approval**
- **Granted**
- Date: **2026-07-29**
- Approved lifecycle: **Approval**
- This approval is **design authority only** — it does not authorize Publication of this or any other document, and does not authorize implementation

**Publication treatment:** This document is **not** the direct coding specification and does not itself target Published — its target lifecycle is Approval, now reached. Direct coding may rely only on `docs/database/JobCard_TierA_DocType_Specification.md` once that document independently reaches Published — which it has not.

**Approval Statement:** This System Design is now the **approved architecture/design authority** for PrintHub Job Card Tier A. The companion DocType Specification **remains Draft** and is **not safe for coding**. Of the pre-Publication technical gates (Section 25), **P-2, attachment permission validation, REST/API permission validation, and race-safe uniqueness validation are Closed** as of 2026-07-30; **terminal-reason persistence validation and the Artwork production gate remain open**, as does every other currently unchecked pre-Publication or production-readiness item. **No coding or implementation is authorized by this Approval.**

- Target Lifecycle: **Approval** — reached
- Publication: **Not Applicable** as a direct coding authority (this document is not intended to reach Published)
- Implementation Authorization: **Not Granted**

---

## Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-07-29 | PrintHub Architecture Team | Initial Draft. Records the Project Owner-approved Job Card Tier A design baseline (decision set O-1 through O-27, approved 2026-07-29): technical name `PrintHub Job Card`; Tier A operational purpose (Registered/Released/In Progress/Completed as business declarations, not physical-production proof); Submitted-only Sales Order eligibility; exactly one active Job Card per Sales Order; the Registered→Released→In Progress→Completed lifecycle with Discarded/Voided terminations; the Artwork production gate and its bounded non-production demonstration exception; the race-safe uniqueness requirement (mechanism delegated, pre-Publication validation required); the terminal-reason atomic-persistence requirement (mechanism delegated, pre-Publication validation required); the approved product-surface policy; and the shortest safe path from this Draft through Publication, implementation authorization, and demonstration. No Architecture Review, Business Review, or Project Owner document lifecycle approval has yet occurred for this document. No implementation authorization was granted. This document does not resolve any Architecture Review item, does not amend any Accepted ADR, and does not modify any other tracked document. |
| 0.2 | 2026-07-29 | Architecture and Business Review Correction | Applied the findings of the formal combined Architecture Review (Disposition: Corrections Required) and Business Review (Disposition: Accepted with non-blocking observations), both dated 2026-07-29. Added Section 12.1, a normative status/`docstatus` consistency invariant closing Finding F-1 (blocking): required valid mappings, insert/save/submit/discard/cancel safeguards, and an explicit prohibition on direct API/field-level bypass of controlled transitions. Added Section 16.1, closing Finding F-6 (blocking): a precise Delete capability boundary (eligible only for Registered, `docstatus = 0`, pre-Release records; prohibited thereafter, where controlled discard/void remain the correction path), and added the corresponding "delete erroneous Registered record" capability to Section 17's Permission Capability Model, resolving its prior omission. Added Section 21.1, closing Finding F-2 (non-blocking): terminal atomicity requirements for the race-safe uniqueness claim (acquisition atomic with creation, release atomic with successful termination, no release on failed/rolled-back termination, no claim retained after successful termination). Added Section 20.1, closing Findings F-7/F-10 (non-blocking): an explicit Administrator policy (privileged bypass acknowledged, not a normal operational role, does not affect Tenant isolation, must still preserve lifecycle invariants, privileged repair requires a separately governed procedure). Added Section 22.1, closing Finding F-9 (non-blocking): controlled terminal-action API policy (method exposure delegated but must not bypass permission, independent server-side revalidation required, ordinary REST field updates must not substitute for the controlled action). Expanded Section 25's Open Pre-Publication Technical Closures to reference the new invariants and their required companion test coverage. Findings F-3, F-4, F-5, and F-8 were addressed in the companion DocType Specification, which this document's Section 25 now cross-references. Recorded the formal review results in Section 27: Architecture Review Corrections Required (blocking findings F-1, F-6, both addressed in this version; non-blocking findings F-2 through F-10 addressed or tracked as noted); Business Review Accepted with non-blocking observations; targeted Architecture re-review pending; Project Owner Document Lifecycle Approval remains Not Granted. No approved Project Owner design decision was changed: technical name, business purpose, Sales Order eligibility, multiplicity, lifecycle states and transition sequence, the Artwork two-track boundary, Company/Tenant treatment, the approved surface policy, excluded scope, and both documents' target lifecycles are all unchanged. This document remains Draft and is not promoted to Approval by this correction. No implementation authorization was granted. No other tracked document was modified. |
| 1.0 | 2026-07-29 | Project Owner Lifecycle Approval | Recorded the completed targeted Architecture Re-review (Disposition: Accepted with non-blocking observations, confirming blocking Findings F-1 and F-6 Closed and Findings F-2 through F-10 correctly and consistently applied, with residual observations R-1 and R-2 both non-blocking) and preserved the existing Business Review disposition (Accepted with non-blocking observations, not repeated, since the Version 0.2 corrections did not change business semantics and this promotion changes no content the Business Review evaluated). Recorded Project Owner Document Lifecycle Approval as Granted, dated 2026-07-29. Status transitioned from Draft to Approval; Version incremented from 0.2 to 1.0. No approved design content was changed by this promotion: technical name, business purpose, lifecycle, status model, Sales Order rules, multiplicity, the Artwork two-track treatment, Company/Tenant treatment, the permission baseline, product-surface policy, excluded scope, and the pre-Publication gate list are all unchanged. This document is now the approved architecture/design authority for PrintHub Job Card Tier A; the companion DocType Specification remains Draft and is not safe for coding; all pre-Publication technical gates (P-2, race-safe uniqueness validation, terminal-reason persistence validation, attachment and REST/API permission validation, and the Artwork production gate) remain open. No document was Published. No implementation authorization was granted. No other tracked document was modified by this task beyond the companion DocType Specification's reference synchronization and Documentation Status's lifecycle-census update. |
| 1.1 | 2026-07-30 | P-2 Closure Evidence Synchronization | Factual synchronization of an approved pre-Publication evidence gate and addition of its resulting normative provisioning invariant — records the Project Owner-authorized P-2 dedicated execution (2026-07-29, disposition "P-2 Partially Closed — additional validation required") and narrow closure execution (2026-07-30, accepted final disposition "P-2 Closed — standard mechanisms sufficient"), each conducted against a genuine, exact-commit-verified Frappe v16.29.0 / ERPNext v16.29.0 environment (Section 5 pins). Updated Section 14 (Company Scoping) to record P-2 Closed, the validated standard-permission baseline, and the mandatory eight-point provisioning invariant governing operational-role and Company User Permission assignment, arising from the U-NONE finding that a role-bearing user with zero Company User Permissions is unrestricted (not denied) under standard Frappe behavior. Updated Section 17 (Permission Capability Model) to record that neither `has_permission` nor `permission_query_conditions` is required by current governed evidence, and that neither is prohibited forever — either requires a future concrete reproduced gap plus a separately approved, controlled design change. Updated Section 19 (API and Product Surface Policy) to record REST and Attachment permission behavior as validated. Updated Section 20 (Security Boundaries) with a P-2 evidence summary covering cross-Company leakage, direct API access, list-query access, attachment (metadata/download/upload) permissions, export scoping, and Administrator behavior, including the root-cause resolution of the previously inconclusive unauthorized-upload-denial finding (a local host tooling artifact, not a server-side or permission-model defect) and the non-blocking observation that rendered Desk browser UI was not directly exercised while its underlying server paths were. Updated Section 25 (Open Pre-Publication Technical Closures) to mark P-2, attachment permission validation, and REST/API permission validation Closed, while race-safe uniqueness, terminal-reason persistence, and the Artwork production gate remain open. Updated Section 26 (Shortest Safe Path) step 7 and Section 27's Approval Statement to reflect the same gate disposition. This is a factual synchronization only: no approved architecture or business review disposition was reopened, no Project Owner lifecycle approval was revisited, and the technical DocType name, lifecycle model, aggregate boundary, Sales Order rules, Artwork treatment, and product-surface policy are all unchanged. No implementation authorization was granted. No DocType, coding specification, race-safe uniqueness selection, terminal-reason persistence mechanism, or Artwork production gate was closed by this task. No other tracked document was modified by this task beyond the companion DocType Specification and Documentation Status synchronizations. |
| 1.2 | 2026-07-30 | Race-Safe Uniqueness Closure Evidence Synchronization | Factual synchronization of a completed pre-Publication technical validation and addition of its resulting normative integrity design. Records the accepted disposition "Race-safe uniqueness gate closed — nullable active-key mechanism recommended" (validated 2026-07-30 against Frappe `06613fc60b44d5736007ae3107cdab029b2ae045` and ERPNext `a5de60c357d531cb31da093f0b86301776965173`, both reported 16.29.0, on Python 3.14.2 / Node v24.13.0 / MariaDB 10.6.27 / Redis 6.2.23 / InnoDB / REPEATABLE-READ / autocommit disabled for the tested request transactions / multi-worker Gunicorn with independent database connections / localhost-only disposable environment / synthetic data only; exact Git commits authoritative, mutable branch names and container tags not authoritative). Rewrote Section 21 to select the nullable, server-derived active claim on the Job Card row protected by a full-column single-column InnoDB UNIQUE index (`active_key`, `VARCHAR(140) NULL`), with site identity implicit, no Tenant field, and Company deliberately excluded from the claim because including it would weaken the invariant to one active Job Card per Company per Sales Order. Recorded the canonical claim as the exact successfully validated `sales_order` Link value with the Link resolving before assignment, surrounding whitespace rejected rather than silently converted, and **no** application-level case transformation prescribed unless a later controlled validation proves it identity-preserving; database comparison follows the deployed MariaDB column collation. Recorded the server invariant (`active_key == sales_order` while Registered/Released/In Progress/Completed; `active_key IS NULL` while Discarded/Voided; no other persisted pair valid) and `active_key` as internal, system-managed, server-derived, excluded from ordinary client editing, editable REST input, import and bulk editing, not a user-facing business field, and recomputed and validated on every persistence path including REST, background, Administrator and privileged execution — with permission bypass unable to bypass the database constraint or the derived-field invariant. Recorded acquisition as part of the Job Card row insert with the UNIQUE index authoritative, no separate claim-acquisition commit, no interval in which an active Job Card lacks its claim, and MariaDB 1062 translated by the future application into a stable domain rejection such as `ActiveJobCardAlreadyExists`. Expanded Section 21.1 with the validated lifecycle transaction boundaries (submit and cancel introduce no intermediate commit; transactionally coupled release is feasible), the normative single-transaction requirement for terminal reason, terminal lifecycle outcome and claim release, and the terminal-release boundary preserving the approved controlled `discard()`/`cancel()` lifecycles without bypassing standard Frappe `docstatus`, hooks, permissions or lifecycle behavior — explicitly **not** selecting the validation harness's raw guarded SQL statement as the production implementation. Recorded the concurrent-terminal requirements (one winner, deterministic already-terminal result for the loser, terminal reason never overwritten, claim released exactly once, no premature replacement window). Added Section 21.2 covering retry policy (1062 never automatically retried as a deterministic active-claim conflict; bounded retry with backoff only for 1205 and 1213; no dependence on process-local locks), client-timeout reconciliation (database state authoritative; timeout does not imply rollback; caller must query the current active Job Card before replacement; 1062 alone does not prove the original request succeeded and must not be classified as idempotent success; request correlation or equivalent ownership evidence required), and migration requirements (derive claims for active records, NULL for terminal records, duplicate detection before index creation, explicit governed remediation, no silent deletion/termination/automatic winner selection, index created only after resolution, visible failure if duplicates remain, and coexistence of historical terminal records via multiple permitted NULLs). Recorded Candidate B (separate active-claim record) and Candidate C (Sales Order row lock) as rejected, along with ordinary pre-insert existence queries, process-local locks, client-side validation, Redis locking as sole guarantee, and post-commit reconciliation; a row lock may later serve orchestration but never as the sole uniqueness guarantee. Added the validation evidence summary to Section 20 (24 validated scenario classes; 116 concurrent attempts with no committed duplicate; manifest `manifest.csv` SHA-256 `52f269358d44ceea72c7562cdb6ab6daa42d457a8fa721ec454b7ef197adb8f2`; 12 evidence files including the manifest; 11 listed artifacts; 327-record structured event log; disposable out-of-repository evidence path, secret-scanned, with no credentials, tokens, cookies, session identifiers or database passwords retained and raw evidence not added to Git). Updated Section 13, Section 25, Section 26 step 7 and Section 27's Approval Statement accordingly. **Terminal-reason persistence remains open**, as does the exact production coordination with the controlled terminal actions, the Artwork production gate, and every other currently unchecked pre-Publication or production-readiness item. No approved business or architecture decision was reopened; no Business Review was repeated; no new Architecture Review disposition was recorded; Project Owner lifecycle approval is unchanged; the technical DocType name, lifecycle model, aggregate boundary, Sales Order rules, Artwork treatment and product-surface policy are unchanged. No document was Published and no implementation authorization was granted. No other tracked document was modified by this task beyond the companion DocType Specification and Documentation Status synchronizations. |
