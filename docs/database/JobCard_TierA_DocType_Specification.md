# PrintHub Job Card Tier A — DocType Specification

## 1. Document Control

Version:
0.2

Status:
Draft

Date:
2026-07-29

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

- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — Draft, Version 0.1 (architecture/design authority for this specification).
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

Submitted and terminal records are immutable except for framework-permitted metadata (e.g., comments, attachments per Section 18).

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
- An **ordinary `exists`-style query is insufficient** as the final enforcement mechanism (race-prone under concurrent requests).
- The **exact race-safe mechanism remains unresolved.**
- **Preferred candidate:** a nullable, database-enforceable "active key" (a conditionally unique indexed value present only while a Job Card is active, cleared on termination).
- **Row-locking on the Sales Order during creation remains an alternative candidate.**
- **Selection and validation of the exact mechanism are mandatory before Publication** (Section 24).

No executable SQL or implementation code is specified here.

### 13.1 Terminal Atomicity Requirement (Finding F-2)

Normative, without selecting the final mechanism:

- Acquisition of the uniqueness claim must be atomic with Job Card creation.
- Release of the uniqueness claim must be atomic with a **successful** Discard or Cancel.
- A failed or rolled-back terminal action must **not** release the claim.
- A successful terminal action must **not** leave the claim held.
- Replacement creation must not become possible until the prior terminal transaction commits.
- Concurrent replacement attempts must still produce at most one active record.
- Migration and recovery behavior must preserve this invariant.

See Section 21, test T-22 (expanded) for the concurrency test requirement.

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

## 17. Permission Specification — Draft Baseline

**Provisional pending P-2.**

Proposed baseline:

- Standard DocType Role Permissions.
- Company User Permissions.
- Server-side Company/Sales Order consistency checks (Section 11).
- **No custom `has_permission` or `permission_query_conditions` hook** unless P-2 demonstrates a concrete standard-permission gap.
- A narrowly scoped **Delete** capability (Finding F-6, Section 14), separate from discard/void/administer, limited to eligible Registered records.
- Exact role names remain a delegated technical decision.

This baseline is **not yet empirically validated** across Desk, REST, report, export, and list surfaces.

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
| T-36 | Direct API status-bypass: post-terminal attempt to alter `status` or `terminal_reason` — expect rejection, no partial transition, no inconsistent status/`docstatus` pair, no uniqueness-claim corruption | `IntegrationTestCase` |
| T-37 | Artwork demo-only track (Finding F-4): manual Artwork approval check required before Registered → Released; release rejected/prevented when the documented manual confirmation is absent — governance-validation / release-check assertion, not a product unit test, since no executable Artwork mechanism exists | `IntegrationTestCase` (governance-validation assertion) |
| T-38 | Artwork demo-only track: specification and environment remain visibly labelled non-production; demo-only classification does not permit production authorization — documentation/configuration assertion | `IntegrationTestCase` (governance-validation assertion) |
| T-39 | Artwork production-capable track: Publication classification cannot become production-capable unless Artwork approval is server-verifiable or the governing rule has been formally changed — governance-validation assertion, not executable against product code while no Artwork mechanism exists | `IntegrationTestCase` (governance-validation assertion) |
| T-40 | Artwork production-capable track: a demo-only manual check cannot satisfy the production gate — governance-validation assertion | `IntegrationTestCase` (governance-validation assertion) |
| T-41 | Delete capability (Finding F-6): permitted deletion of an eligible Registered (`docstatus = 0`, pre-Release) record by a user holding the delete capability | `IntegrationTestCase` |
| T-42 | Delete capability: denied deletion after Release (Released, In Progress, Completed, Discarded, Voided) | `IntegrationTestCase` |
| T-43 | Delete capability: denied deletion attempted by a user without the delete capability | `IntegrationTestCase` |
| T-44 | Delete capability: discard remains available and is the normal audited termination path for a saved Draft-status record, independent of delete capability | `IntegrationTestCase` |

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
- [ ] P-2 complete
- [ ] Race-safe uniqueness mechanism selected and validated
- [ ] Terminal-reason persistence mechanism selected and validated
- [ ] Attachment permissions validated
- [ ] REST/API permission behavior validated
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
