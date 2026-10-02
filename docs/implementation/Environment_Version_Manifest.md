# Environment Version Manifest

**Status:** Review
**Version:** 0.5
**Owner:** Project Owner
**Category:** Implementation

---

## Purpose

This document proposes a schema for recording the exact, independently verifiable version identity of the frameworks and application code PrintOS depends on. At this Draft stage, it is a **proposed future evidence location only**. Per `docs/Documentation_Workflow.md:118`, a document reaches Published status only once "Approved and in force; the authoritative version" — a status this Draft document has not reached. This Draft document therefore cannot yet be cited as authoritative or treated as satisfying any milestone criterion. No framework, version, or commit value is recorded here, and none is implied by this document's creation.

---

## Scope

Proposes version-identity evidence coverage for Frappe, ERPNext, and `printos_core`. Does not cover environment reproducibility (see `docs/milestones/M00_Project_Setup.md`'s reproducibility criterion, evidenced separately by a committed setup script or configuration artifact, per Project Owner Option B decision).

---

## Proposed Schema

| Field | Description |
|---|---|
| Framework Name | e.g., Frappe, ERPNext, `printos_core` |
| Source Repository / Canonical Origin | The authoritative upstream repository this version identity is drawn from (e.g., the official Frappe/ERPNext upstream), distinguished from any project-local fork, mirror, or vendored copy. Must name the exact repository, not only the framework. |
| Release/Version Label | The named release version, where available |
| Exact Commit Hash | The exact Git commit identifying this version, per `docs/standards/Naming_Registry.md:336`'s precedent that "a usable identity must be reproducible and verifiable, normally by an exact Git commit, together with the relevant release/version label where available" |
| Verification Date | Date the entry was verified |
| Verifying Party | Who performed the verification |
| Verification Method | The exact method used (e.g., `bench version`, `git rev-parse`) — the method must be named, not only the result |
| Evidence Status | Not Verified / Verified / Superseded |

No row exists yet in this document. This schema is proposed, not adopted as authoritative. Populating any row with an actual value, and promoting this document to Published, are both separate, later, explicitly authorized acts — neither is performed, started, or implied by this document's creation.

---

## Non-Authorization and Reliance Statement

This document does not authorize implementation, coding, runtime execution, environment provisioning, or any Docker/service/script/migration activity. As a Draft document, it has not reached the "Approved and in force; the authoritative version" status that `Documentation_Workflow.md:118` describes; it does not satisfy, resolve, or make eligible for assessment any milestone criterion cited by `docs/milestones/M00_Project_Setup.md` or any other document. It does not authorize inspection of `platform/`, `.artifacts/`, or any runtime configuration.

---

## Related Documents

- `docs/milestones/M00_Project_Setup.md`
- `docs/implementation/12_Project_Milestones.md`
- `docs/standards/Naming_Registry.md` (version-identity evidence-format precedent, line 336)

---

## Revision History

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-29 | Initial Draft — proposes a schema (framework name, release/version label, exact commit hash, verification date, verifying party, verification method, evidence status) as a future evidence location for version-identity evidence. No actual Frappe, ERPNext, or `printos_core` version value recorded. As a Draft document, cannot be relied upon per `Documentation_Workflow.md:118`; does not satisfy, resolve, or make eligible for assessment any milestone criterion. No implementation, runtime, or verification act authorized. |
| 0.2 | 2026-09-30 | Added the Source Repository / Canonical Origin field to the Proposed Schema, distinguishing an authoritative upstream repository from a project-local fork or mirror. Corrected the Purpose and Non-Authorization and Reliance Statement to quote `Documentation_Workflow.md:118` precisely ("Approved and in force; the authoritative version") rather than paraphrasing it as general document-to-document reliance. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.3 | 2026-09-30 | Corrected a stale self-reference in the Proposed Schema section: "No row exists yet in this Version 0.1" incorrectly cited the document's original version number after it had been bumped to Version 0.2, producing a self-contradiction with the header. Replaced with a version-neutral statement ("No row exists yet in this document") so this cannot recur at future version bumps. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.4 | 2026-10-02 | Non-contradictory clarification, per `Documentation_Workflow.md` Section 8's MINOR-increment rule: removed the version-number self-reference in Purpose ("At this Draft, Version 0.2 stage") that had gone stale after the document was bumped to Version 0.3, producing a self-contradiction with the header — the same defect class previously corrected at the Proposed Schema section's "this Version 0.1" reference. Replaced with version-neutral wording ("At this Draft stage") so this cannot recur at future version bumps. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.5 | 2026-10-02 | Moved from Draft to Review status, per `docs/Documentation_Workflow.md` Section 5's lifecycle sequence. Review outcomes (AI, Architecture, Business, Documentation-Governance) are recorded in the new durable review record `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.1), per `Documentation_Workflow.md:173`. No content, schema, or substantive wording changed in this version. No framework, version, or commit value recorded. Approval and Publication are not proposed or authorized by this change. |

---
