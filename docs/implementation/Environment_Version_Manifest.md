# Environment Version Manifest

**Status:** Review
**Version:** 0.9
**Owner:** Project Owner
**Category:** Implementation

---

## Purpose

This document proposes a schema for recording the exact, independently verifiable version identity of the frameworks and application code PrintOS depends on. Until it is Published, it is a **proposed future evidence location only**. Per `docs/Documentation_Workflow.md:118`, a document reaches Published status only once "Approved and in force; the authoritative version" — a status this document has not reached. It therefore cannot yet be cited as authoritative or treated as satisfying any milestone criterion. No component identity, installed version, or exact commit has been verified or entered into a manifest value row, and none is implied by this document's creation.

---

## Scope

Proposes version-identity evidence coverage for Frappe, ERPNext, and `printos_core`. Does not cover environment reproducibility (see `docs/milestones/M00_Project_Setup.md`'s reproducibility criterion, evidenced separately by a committed setup script or configuration artifact, per Project Owner Option B decision).

---

## Proposed Schema

| Field | Description |
|---|---|
| Component Name | The governed software component whose version identity is recorded — e.g., Frappe, ERPNext, or `printos_core` (the dedicated custom application established by Accepted ADR-002) |
| Source Repository / Canonical Origin | The authoritative canonical repository this version identity is drawn from — the official upstream for Frappe and ERPNext, or, for `printos_core`, its governed project repository once one is designated — distinguished from any fork, mirror, or vendored copy. Must name the exact repository, not only the component. |
| Release/Version Label | The named release version, where available |
| Exact Commit Hash | The exact Git commit identifying this version, per `docs/standards/Naming_Registry.md:336`'s precedent that "a usable identity must be reproducible and verifiable, normally by an exact Git commit, together with the relevant release/version label where available" |
| Verification Date | Date the entry was verified |
| Verifying Party | Who performed the verification |
| Verification Method | The exact method used (e.g., `bench version`, `git rev-parse`) — the method must be named, not only the result |
| Evidence Status | Not Verified / Verified / Superseded |

No row exists yet in this document. This schema is proposed, not adopted as authoritative. Populating any row with an actual value, and promoting this document to Published, are both separate, later, explicitly authorized acts — neither is performed, started, or implied by this document's creation.

---

## Non-Authorization and Reliance Statement

This document does not authorize implementation, coding, runtime execution, environment provisioning, or any Docker/service/script/migration activity. Until it is Published, it has not reached the "Approved and in force; the authoritative version" status that `Documentation_Workflow.md:118` describes; it does not satisfy, resolve, or make eligible for assessment any milestone criterion cited by `docs/milestones/M00_Project_Setup.md` or any other document. It does not authorize inspection of `platform/`, `.artifacts/`, or any runtime configuration.

---

## Related Documents

- `docs/milestones/M00_Project_Setup.md`
- `docs/implementation/12_Project_Milestones.md`
- `docs/standards/Naming_Registry.md` (version-identity evidence-format precedent, line 336)
- `docs/decisions/ADR-001-ERPNext-Framework.md` (Accepted; supplies the governed ERPNext v16 framework choice — no installed version, release label, source repository, or exact commit has been verified or entered into a manifest value row)
- `docs/decisions/ADR-002-PrintOS-Core.md` (Accepted; establishes `printos_core` as the dedicated custom application for all PrintOS-specific logic, whose version identity this document's Scope covers; this reference records no app existence, installed version, source repository, or verified commit)
- `docs/Documentation_Workflow.md` (Section 5 lifecycle definitions relied on by the Purpose and the Non-Authorization and Reliance Statement)
- `docs/reviews/Environment_Version_Manifest_Review.md` (durable review record for this document)

---

## Revision History

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-29 | Initial Draft — proposes a schema (framework name, release/version label, exact commit hash, verification date, verifying party, verification method, evidence status) as a future evidence location for version-identity evidence. No actual Frappe, ERPNext, or `printos_core` version value recorded. As a Draft document, cannot be relied upon per `Documentation_Workflow.md:118`; does not satisfy, resolve, or make eligible for assessment any milestone criterion. No implementation, runtime, or verification act authorized. |
| 0.2 | 2026-09-30 | Added the Source Repository / Canonical Origin field to the Proposed Schema, distinguishing an authoritative upstream repository from a project-local fork or mirror. Corrected the Purpose and Non-Authorization and Reliance Statement to quote `Documentation_Workflow.md:118` precisely ("Approved and in force; the authoritative version") rather than paraphrasing it as general document-to-document reliance. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.3 | 2026-09-30 | Corrected a stale self-reference in the Proposed Schema section: "No row exists yet in this Version 0.1" incorrectly cited the document's original version number after it had been bumped to Version 0.2, producing a self-contradiction with the header. Replaced with a version-neutral statement ("No row exists yet in this document") so this cannot recur at future version bumps. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.4 | 2026-10-02 | Non-contradictory clarification, per `Documentation_Workflow.md` Section 8's MINOR-increment rule: removed the version-number self-reference in Purpose ("At this Draft, Version 0.2 stage") that had gone stale after the document was bumped to Version 0.3, producing a self-contradiction with the header — the same defect class previously corrected at the Proposed Schema section's "this Version 0.1" reference. Replaced with version-neutral wording ("At this Draft stage") so this cannot recur at future version bumps. No framework, version, or commit value recorded. Remains Draft; cannot yet be cited as authoritative or promoted to satisfy any milestone criterion. |
| 0.5 | 2026-10-02 | Moved from Draft to Review status, per `docs/Documentation_Workflow.md` Section 5's lifecycle sequence. Review outcomes (AI, Architecture, Business, Documentation-Governance) are recorded in the new durable review record `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.1), per `Documentation_Workflow.md:173`. No content, schema, or substantive wording changed in this version. No framework, version, or commit value recorded. Approval and Publication are not proposed or authorized by this change. |
| 0.6 | 2026-10-03 | Returned from Review to Revision under `Documentation_Workflow.md` Section 5, whose Revision stage covers a document "Sent back to the author after review with required changes." The supplemental review pass recorded in `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.3) identified two required changes, both addressed in this version: (1) the Purpose and the Non-Authorization and Reliance Statement still described this document as "Draft" after the Version 0.5 header change to Review, and now use lifecycle-neutral wording ("Until it is Published"); (2) Accepted ADR-001 is added to Related Documents as the source of the governed ERPNext v16 framework choice. No installed version, release label, source repository, or exact commit has been verified or entered into any value row. Schema unchanged. Approval and Publication are not proposed or authorized. |
| 0.7 | 2026-10-03 | Addressed two further required changes during Revision and returned to Review under `Documentation_Workflow.md` Section 5 ("returns to Review once addressed"). Both were identified in the check of committed Version 0.6 recorded in `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.4): (1) Related Documents omitted `docs/Documentation_Workflow.md`, whose Section 5 definitions the Purpose and the Non-Authorization and Reliance Statement rely on, and the review record, which depends on this document, contrary to the Section 9 rule to list documents this one "depends on or is depended on by"; (2) Accepted ADR-002, which establishes `printos_core` as the dedicated custom application whose version identity the Scope covers, was not cited. All three entries are now listed; the ADR-002 reference records no app existence, installed version, source repository, or verified commit. Schema unchanged; no value row exists. Section 7 re-review of this version is pending independent verification. Approval and Publication are not proposed or authorized. |
| 0.8 | 2026-10-03 | Returned from Review to Revision under `Documentation_Workflow.md` Section 5 after AI Review (ChatGPT) of Version 0.7, recorded in `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.5), found a required naming correction: the Proposed Schema labelled its first field "Framework Name" although its examples and the Scope include `printos_core`, the dedicated custom application established by Accepted ADR-002, and the Source Repository / Canonical Origin field used "framework" as an umbrella term. Renamed the field to "Component Name" with a description covering Frappe, ERPNext, and `printos_core`; reworded the Source Repository / Canonical Origin description to cover the official upstream for Frappe and ERPNext and a governed project repository for `printos_core` once one is designated, without selecting or claiming any repository; and replaced the Purpose's "No framework, version, or commit value is recorded here" with a statement that no component identity, installed version, or exact commit has been verified or entered into a manifest value row. The component names in the schema remain examples, not verified evidence. No value row exists. Approval and Publication are not proposed or authorized. |
| 0.9 | 2026-10-03 | Returned from Revision to Review under `Documentation_Workflow.md` Section 5 ("returns to Review once addressed"). The required naming correction recorded as Finding 8 in `docs/reviews/Environment_Version_Manifest_Review.md` (Draft, Version 0.6) was addressed in Version 0.8: the first schema field is labelled "Component Name", the Source Repository / Canonical Origin description no longer treats "framework" as an umbrella term, and the Purpose states that no component identity, installed version, or exact commit has been verified or entered into a manifest value row. In this version the schema and substantive body text are unchanged; only the Status, Version, and Revision History change. Section 7 lens outcomes and Section 10 checklist results for this version are pending independent review. Approval and Publication are not proposed or authorized. |

---
