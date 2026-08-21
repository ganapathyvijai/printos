# Artwork Runtime-Validation Readiness Register

Version:
0.1

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-08-21

---

# Document Class

This document is:

- a compact architecture and validation-readiness planning register;
- non-normative except where it cites already approved governing design;
- **not** an Architecture Review Register;
- **not** a coding specification;
- **not** runtime evidence;
- **not** authorization to inspect source, implement, validate, publish, or close gates.

---

# Purpose

Tracks, in one compact register, the current governed state of the Artwork bounded context against four categories: approved normative design that requires no further decision; genuinely unresolved governance decisions; questions that require authorized source inspection; and behaviors whose normative rule is known but whose runtime evidence does not yet exist. It exists to make readiness status legible without repeating the full System Design or DocType Specification text, and without claiming any authority those documents themselves do not grant.

---

# Scope

Covers the Artwork bounded context as governed by `docs/blueprint/18_Artwork_Management.md` (Approval, Version 1.1) and its consuming reference contract in `docs/implementation/JobCard_TierA_System_Design.md` (Approval, Version 1.5), together with the companion `docs/database/Artwork_Authority_DocType_Specification.md` and `docs/database/JobCard_TierA_DocType_Specification.md` (both Draft). Excludes application source, ERPNext/Frappe internals, `platform/erpnext/`, schemas, databases, runtime environments, services, logs, and containers — none of which was inspected to produce this register.

---

# 1. Identifier-provenance legend

- **`ART-RVR-*`** — local register identifiers only, invented for this register. Not Architecture Review Register IDs, approvals, gates, findings, or execution records.
- **`AR-003`** — the sole genuine Architecture Review Register ID in scope.
- **`RO-13`, `UV-017`** — planning identifiers from the canonical review baseline, **not approved normative identifiers**, **not Architecture Review Register IDs**, and **absent from the current tracked repository**. Their absence from tracked documentation does not authorize deleting, renaming, or replacing them. Their closest governed anchors are Artwork System Design §21.2's transaction, locking, revalidation, and global lock-order gates (items 11–12, 31–33, 42–43).
- Gate numbers (e.g., "items 7–8") refer to the numbered list in Artwork System Design §21.2.

---

# 2. Reconciliation findings

- **`ART-RVP29-F1`**: absent from tracked documentation. Its subject — the consequence for an already-Approved Production Artwork Set when a member Revision is superseded — **is resolved** as approved normative content in Artwork System Design §7.2 (Approval, Version 1.1). Recorded in Section A, not as an open governance decision.
- **`RO-13`**: absent from tracked documentation. Preserved here as a planning identifier per the canonical review baseline, recorded in Section C as an unresolved source-inspection question, not deleted or renamed.
- **`UV-017`**: absent from tracked documentation. Preserved here as a planning identifier, recorded in Section D as blocked-derived on `RO-13`, not executable, not validated.
- **`ART-RVP57-F3`** and **`CP-037`**: absent from tracked documentation. Not recreated.
- The 126-experiment / 39-checkpoint / 35-phase executable-looking specification: absent from the repository. Not reconstructed.
- Correction 16 Part A-2: not continued or recreated.

---

# 3. Section A — Governed and stable

| ID | Requirement or question | Governing document or design rule | Current status | Missing evidence | Blocking effect | Next authorized action |
|---|---|---|---|---|---|---|
| ART-RVR-A01 | Artwork bounded-context ownership and Production's consumer-only role | Artwork SD §5, §13 | Approved, normative | — | — | None; stable |
| ART-RVR-A02 | Artwork/Revision/File cardinality: a Sales Order may require multiple Artworks; each Artwork may have multiple Revisions; each Revision belongs to exactly one Artwork; each Revision has exactly one authoritative private File | Artwork SD §6.1, §6.2, §6.1b, §10 | Approved, normative | — | — | None; stable |
| ART-RVR-A03 | Customer approval mandatory before internal production approval | Artwork SD §5.1, §6.2a | Approved, normative | — | — | None; stable |
| ART-RVR-A04 | Revision lifecycle states and terminal-state rules (Draft→Submitted→Approved/Rejected; Superseded/Withdrawn terminal) | Artwork SD §7, §7.1 | Approved, normative | — | — | None; stable |
| ART-RVR-A05 | Customer Approval Evidence uniqueness: at most one Approved outcome ever for an exact Revision; permanent key (`approved_revision_key`) represents exact Revision identity; NULL for Rejected evidence; key remains occupied after revocation; reapproval requires a new Revision; database single-column UNIQUE constraint is the final enforcement authority | Artwork SD §6.2d | Approved, normative | — | — | None; stable |
| ART-RVR-A06 | Production Artwork Set authority: sole release authority, membership, completeness, immutability, six-state lifecycle | Artwork SD §6.3, §8, §8.1, §9 | Approved, normative | — | — | None; stable |
| ART-RVR-A07 | Newer-Revision consequence for an already-Approved Set: prior Revision and containing Set both move to Superseded (not Withdrawn); complete-propagation, no-silent-winner guard; new Set required for future release | Artwork SD §7.2 (resolves the former `ART-RVP29-F1` question) | Approved, normative | — | — | None; stable |
| ART-RVR-A08 | Atomic revocation — Approved Revision branch: revoke the approval evidence; withdraw the Approved Revision; clear the Revision's active approval key; withdraw every currently Approved containing Production Artwork Set; clear those Sets' active approval keys; all changes atomic in one transaction. Customer Approval Evidence uniqueness key remains occupied throughout. | Artwork SD §6.2c | Approved, normative | — | — | None; stable |
| ART-RVR-A09 | Atomic revocation — Submitted-at-revocation branch: the Revision remains Submitted; becomes permanently ineligible for approval; its Revision approval key remains NULL; it does **not** become Withdrawn; no Set withdrawal occurs for this branch unless a separately governed rule expressly requires it. Customer Approval Evidence uniqueness key remains occupied. | Artwork SD §6.2c | Approved, normative | — | — | None; stable |
| ART-RVR-A10 | Exact Approved-Set binding to Job Card during Registered→Released only, single conceptual `production_artwork_set` field | Artwork SD §13, §14; Job Card SD §9, §10.3 | Approved, normative | — | — | None; stable |
| ART-RVR-A11 | Superseded-Set/Job Card rule: a Superseded Set cannot authorize a new Job Card release; only Job Cards already Released **before** supersession retain the historical binding; those previously Released Job Cards may subsequently progress to In Progress or Completed, subject to other guards; no Job Card is silently rebound. This does not extend to any later-created or not-yet-Released Job Card. | Artwork SD §16.1, §16.2; Job Card SD §10.3 | Approved, normative | — | — | None; stable |

---

# 4. Section B — Governance decision required

| ID | Requirement or question | Governing document or design rule | Current status | Missing evidence | Blocking effect | Next authorized action |
|---|---|---|---|---|---|---|
| AR-003 | Naming Registry alignment: final governed technical names (e.g., `PrintHub Artwork`, `PrintHub Production Artwork Set`) and the `printos_core` module path | Architecture Review Register, AR-003 | Open | Project Owner naming decision via Term Change Policy | Blocks Publication of both DocType Specs per each Specification's own Publication conditions | Project Owner resolves AR-003 per Naming Registry §33 process (not performed here) |

No other genuine unresolved governance decision was found; the newer-Revision/Approved-Set question is resolved (Section A, ART-RVR-A07).

---

# 5. Section C — Source inspection required

*(Source inspection is unauthorized; questions are recorded, not answered.)*

| ID | Requirement or question | Governing document or design rule | Current status | Missing evidence | Blocking effect | Next authorized action |
|---|---|---|---|---|---|---|
| RO-13 | Unresolved controlled-operation transaction, revalidation, and locking boundaries (planning identifier from the canonical review baseline; absent from tracked docs; not an approved normative identifier) | Closest governed anchor: Artwork SD §21.2 items 11–12, 31–33, 42–43 | Design-level only; boundaries not established | Transaction/locking implementation evidence for the specific controlled operation this identifier denotes | Blocks finalization of `UV-017` and any concurrency validation design depending on it | Authorized source inspection of transaction/locking implementation |
| ART-RVR-C01 | DocType/docstatus strategy for Artwork Revision and Production Artwork Set under Frappe's model | Artwork SD §21.2 items 1–2 | Design-level only | `printos_core` implementation / Frappe extension-point behavior | Blocks coding-readiness confirmation | Authorized source inspection of `printos_core` and Frappe docstatus mechanics |
| ART-RVR-C02 | Nullable UNIQUE `approved_key`/`approved_revision_key` behavior under MariaDB/Frappe ORM | Artwork SD §21.2 items 3–4, 48, 60 | Design-level only | Schema/constraint behavior evidence | Blocks coding-readiness confirmation of the uniqueness mechanism | Authorized schema/ORM inspection |
| ART-RVR-C03 | Global lock order and deadlock treatment across requirement-change, release, Revision/Set approval, withdrawal, and revocation operations | Artwork SD §21.2 item 43 (cross-referenced by §7.2's "provisional" lock order) | Design-level, provisional only | Reconciled single lock-order policy; Frappe/MariaDB locking behavior | Blocks validation design for items 11–12, 43 and any dependent concurrency scenario | Authorized source inspection of transaction/locking implementation |
| ART-RVR-C04 | Direct REST, `ignore_permissions`, Administrator, and background-job bypass prevention for approval/uniqueness/withdrawal fields | Artwork SD §21.2 items 20–21, 39, 58–59 | Design-level only | Controller/permission implementation | Leaves bypass-prevention behavior unproven | Authorized source inspection of controller/permission code paths |
| ART-RVR-C05 | Migration/duplicate-detection sequencing before constraint creation | Artwork SD §21.2 items 23, 56–57 | Design-level only | Migration script implementation | Leaves safe constraint rollout unproven | Authorized inspection of migration implementation |
| ART-RVR-C06 | Stable domain-error translation (no SQL/index/stack-trace exposure) | Artwork SD §21.2 items 44, 62 | Design-level only | Error-handling implementation | Leaves safe error-surface behavior unproven | Authorized source inspection of error-translation layer |
| ART-RVR-C07 | Two-hop private File access restriction for Production users | Artwork SD §21.2 item 13 | Design-level only | File/permission implementation | Leaves file-access boundary unproven | Authorized source inspection of file access controller |

---

# 6. Section D — Runtime validation required

*(Runtime validation is unauthorized. No gate or scenario is marked Passed, Closed, executable, or validated. All 62 runtime-validation gates listed in Artwork System Design §21.2 remain Open. None has been executed, Passed, Closed, or authorized; the grouped readiness rows below do not alter any individual gate’s status.)*

| ID | Requirement or question | Governing document or design rule | Current status | Missing evidence | Blocking effect | Next authorized action |
|---|---|---|---|---|---|---|
| UV-017 | Runtime scenario dependent on the `RO-13` transaction/locking boundaries (planning identifier from the canonical review baseline; absent from tracked docs; not an approved normative identifier) | Closest governed anchor: Artwork SD §21.2 items 11–12, 31–33, 42–43 | Blocked-derived; cannot be finalized or executed until `RO-13` is resolved | `RO-13` source-inspection findings; then runtime execution evidence | Blocks validation design and execution for this scenario until `RO-13` is established | Authorized source inspection to resolve `RO-13`, then separate runtime-validation authorization |
| ART-RVR-D01 | Atomic supersession of previously Approved Revision and Set (including the §7.2 newer-Revision path) | Artwork SD §21.2 items 7–8 | Rule known; no execution | Transactional test evidence | Leaves runtime correctness unproven; blocks Publication per §21.2 preamble | Authorized runtime validation under separate execution authorization |
| ART-RVR-D02 | Concurrent-approval and race scenarios (competing revisions/sets, release-vs-withdrawal, release-vs-supersession) | Artwork SD §21.2 items 5–6, 9–10 | Rule known; no execution | Concurrency test evidence | Leaves runtime correctness unproven; blocks Publication per §21.2 preamble | Authorized runtime validation |
| ART-RVR-D03 | Revocation atomicity and race scenarios (Revision/Set withdrawal, key release, rollback, races vs. approval/release/withdrawal) | Artwork SD §21.2 items 25–38, 41–42 | Rule known; no execution | Concurrency/rollback test evidence | Leaves runtime correctness unproven; blocks Publication per §21.2 preamble | Authorized runtime validation |
| ART-RVR-D04 | Customer Approval Evidence uniqueness under concurrency (first/second/duplicate/corrupt scenarios) | Artwork SD §21.2 items 45–47, 51–55 | Rule known; no execution | Concurrency test evidence | Leaves runtime correctness unproven; blocks Publication per §21.2 preamble | Authorized runtime validation |
| ART-RVR-D05 | Job Card Registered→Released contract execution against a resolved Set, including Company/Sales Order mismatch rejection | Artwork SD §14; Job Card SD §10.3; §21.2 item 61 | Rule known; no execution | End-to-end release-flow test evidence | Blocks production-capable readiness | Authorized runtime validation |
| ART-RVR-D06 | Server-verifiable Artwork mechanism required to close the Artwork production gate and unblock production-capable Publication | Job Card SD §10.1, §10.3 ("normative... not yet operationally relied upon") | No implementation evidence; production gate Open | Implementation evidence (if applicable) and runtime-validation evidence | Blocks production-capable Publication and production authorization; production gate remains Open | (1) Explicit implementation/source-inspection authorization → (2) implementation evidence, if applicable → (3) explicit runtime-validation authorization → (4) runtime evidence → (5) separate gate-closure decision |

---

# 7. Row counts and final disposition

- **Rows:** A = 11, B = 1, C = 8 (including `RO-13`), D = 7 (including `UV-017`).
- **Genuine governance decisions remaining:** 1 — `AR-003`.
- **Source-inspection authorization needed:** yes, for all 8 rows in Section C, including `RO-13` (not granted by this document).
- **Runtime-validation authorization needed:** yes, for all 7 rows in Section D, including `UV-017` (not granted by this document). The Artwork production gate remains Open and is not closed by this register.

**Design rules are documented to the extent shown above; implementation and runtime-validation readiness remain unproven until the identified governance, source-inspection, and runtime-evidence gaps are resolved.**

---

# Related Documents

- [../blueprint/18_Artwork_Management.md](../blueprint/18_Artwork_Management.md) — Approval, Version 1.1; the governing Artwork architecture and business-design baseline this register summarizes.
- [../implementation/JobCard_TierA_System_Design.md](../implementation/JobCard_TierA_System_Design.md) — Approval, Version 1.5; the consuming Production Artwork Set reference contract.
- [../database/Artwork_Authority_DocType_Specification.md](../database/Artwork_Authority_DocType_Specification.md) — Draft; not Published; not safe for coding.
- [../database/JobCard_TierA_DocType_Specification.md](../database/JobCard_TierA_DocType_Specification.md) — Draft; not Published; not safe for coding.
- [../decisions/Architecture_Review_Register.md](../decisions/Architecture_Review_Register.md) — source of `AR-003`, cited but not modified by this register.
- [../Documentation_Status.md](../Documentation_Status.md) — synchronized alongside this register's creation.

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | 2026-08-21 | AI-Assisted, Project Owner-Directed Documentation Work | Initial compact readiness-register draft. Consolidates approved normative facts (Section A, 11 rows), the remaining governance decision (Section B, `AR-003`), source-inspection questions (Section C, 8 rows including `RO-13`), and absent runtime evidence (Section D, 7 rows including `UV-017`). Preserves `RO-13` and `UV-017` as planning-only identifiers from the canonical review baseline — not approved normative identifiers, not Architecture Review Register IDs, absent from tracked documentation, and not deleted or renamed. Records that the subject of the former `ART-RVP29-F1` question is resolved by approved Artwork System Design §7.2 and is recorded in Section A accordingly. Creates **no** Architecture Review Register item; no AR identifier was invented. Does **not** reconstruct Correction 16 Part A-2 or the absent 126-experiment/39-checkpoint/35-phase specification. Grants **no** implementation, source-inspection, runtime-validation, Publication, production, or gate-closure authority. This document is Draft and has not undergone Architecture Review, Business Review, or Project Owner lifecycle approval. |

---

# Documentation Quality Checklist

- [ ] Purpose is clearly defined
- [ ] Scope is clearly defined, including what is explicitly excluded
- [ ] No information is duplicated from another document without reference
- [ ] Terminology matches the approved Business Glossary
- [ ] No conflicting information exists elsewhere in the documentation set
- [ ] Document has been reviewed under all applicable review lenses
- [ ] Document has received explicit Owner approval
- [ ] Revision History table is complete and current
- [ ] Related Documents section is complete and links resolve correctly
- [ ] Document contains no implementation code or code-level detail
