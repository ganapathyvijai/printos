# M00 — Project Setup

**Status:** Draft
**Version:** 0.5
**Owner:** Project Owner
**Category:** Milestones
**Milestone-Completion Status:** Not Assessed

---

## Scope

M00 is a project-setup readiness record. It documents whether specific project-setup and tooling/environment conditions have been defined and, separately, whether evidence exists that each condition has been met. It does not sequence, gate, or authorize any Phase, module, or later milestone.

---

## Explicit Exclusions

- Does not resolve any Architecture Review item or amend the Architecture Review Register.
- Does not author or amend the Development Roadmap or Module Implementation Order.
- Does not assign phases, dates, owners, or estimates beyond the setup conditions it records.
- Does not authorize implementation, coding, or runtime work of any kind.
- Does not modify the status metadata of any other document (each companion synchronization is a separate, explicitly identified action).
- Does not assess or gate any other milestone (M01–M09); does not impose a prerequisite on Phase 1, the Administration module, or any later-milestone start. Any dependency on another milestone must be stated explicitly here, per `docs/implementation/12_Project_Milestones.md`'s Milestone Dependencies section, not assumed.

---

## Completion Criteria and Evidence Requirements

| Criterion | Evidence type | Evidence location |
|---|---|---|
| Repository initialized under governance rules | Exact file path and content check | `docs/` directory structure at the repository root `/home/adharshan/Projects/PrintHub` |
| ERPNext/Frappe framework version pinned and verified | Exact commit/version record | **Undefined and not yet eligible for assessment.** A proposed future evidence location exists — `docs/implementation/Environment_Version_Manifest.md` (Draft, Version 0.2) — but as a Draft document it may not be relied upon (`Documentation_Workflow.md:118`). This criterion remains Undefined until that document reaches Published status and a separately authorized verification act records values. |
| `printos_core` app scaffold created per Clean Architecture rules | File/module tree listing | `platform/` app structure (cited only; not verified by this record) |
| Documentation governance structure in place | Tracked file listing | `docs/Documentation_Workflow.md`, `docs/templates/` (existence and content directly checkable via `git ls-files`) |
| Naming Registry and ADR process operational | Tracked file content | `docs/decisions/00_ADR_Index.md`, `docs/standards/Naming_Registry.md` |
| Development environment reproducibility confirmed | Committed setup script or configuration artifact, plus a verifiable run result | The evidence **format** is now defined: a future committed setup script or configuration artifact, together with a separately recorded, verifiable run result. Per Project Owner Option B and `docs/implementation/01_Phase_1_Roadmap.md:84`, this is deliberately an implementation artifact, not a documentation record. The evidence itself — the artifact's path and its run result — **does not yet exist and is not yet available**; no path is invented here, and no such artifact is created or run by this document. This criterion remains **Undefined and not yet eligible for assessment** until that artifact exists and its run result is recorded. |

**Rule:** Each completion criterion above may be assessed only after that specific criterion's own evidence location and evidence format are defined, **and, where that evidence location is itself a document, only once that document reaches Published status** (`Documentation_Workflow.md:118`). Both criteria remain marked Undefined and not yet eligible for assessment, for different reasons: the ERPNext/Frappe version-identity criterion has a proposed future evidence location (`docs/implementation/Environment_Version_Manifest.md`, currently Draft) that cannot yet be relied upon; the development-environment reproducibility criterion now has a defined evidence **format** (Project Owner Option B: a committed setup script/configuration artifact plus a verifiable run result), but neither the artifact nor its run result yet exists. M00 as a whole remains recorded as **Milestone-Completion Status: Not Assessed** until its criteria are complete enough for an Owner-approved assessment to begin; this documentation task performs no assessment, verification act, or Milestone-Completion Status change. This rule creates no Phase, module, or milestone gate; it governs only when M00's own Milestone-Completion Status may next change.

---

## Dependencies and Blockers (Unresolved)

- The ERPNext/Frappe version-identity criterion has a proposed Draft evidence location (`docs/implementation/Environment_Version_Manifest.md`), which cannot yet be cited as authoritative since it has not reached Published status; no framework, version, or commit value is recorded or verified there.
- The development-environment reproducibility evidence **format** is now defined (Project Owner Option B: committed setup script/configuration artifact plus a verifiable run result); the artifact and its run result do not yet exist and remain undefined in practice.
- No explicit dependency from M00 to any later milestone or vice versa exists; per `docs/implementation/12_Project_Milestones.md`'s Milestone Dependencies section, none is automatic and none is introduced here.

---

## Review and Approval Conditions

This document follows the fixed, non-skippable approval sequence defined in `Documentation_Workflow.md` Section 11: Author → AI Review (ChatGPT) → Architecture Review → Business Review → Owner Approval → Published. Per Section 11, "a document cannot skip a stage."

Applicable Section 7 review lenses to be explicitly considered for this document's content: Technical, Consistency, Cross-reference; Business Review confirms the setup conditions reflect real operational need; Architecture Review confirms no contradiction with the Blueprint domain model and no implied ERPNext core modification.

Only a **Published** milestone file may be relied upon by any other document (`Documentation_Workflow.md:118`).

---

## Non-Authorization Statement

This document does not authorize implementation, coding, runtime execution, environment provisioning, or any Docker/service/script/migration activity, regardless of its recorded Milestone-Completion Status. It does not authorize, sequence, or define the scope of Phase 1, the Administration module, M01, or any later milestone.

---

## Related Documents

- `docs/implementation/12_Project_Milestones.md` (governing framework)
- `docs/Documentation_Workflow.md` (Sections 5, 7, 8, 11)

---

## Revision History

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-27 | Initial Draft — records M00 as a project-setup readiness record: defines its completion criteria and evidence requirements (per `docs/implementation/12_Project_Milestones.md`'s governance framework), establishing that each criterion may be assessed only once its own evidence location and format are defined; two criteria (ERPNext/Frappe version-manifest evidence, development-environment reproducibility evidence) are recorded Undefined and therefore not yet assessable. Milestone-Completion Status recorded as Not Assessed, remaining so until M00's criteria are complete enough for an Owner-approved assessment to begin; this creates no Phase, module, or milestone gate. Does not sequence, gate, or authorize Phase 1, the Administration module, or any later milestone. |
| 0.2 | 2026-09-29 | Recorded a proposed future evidence location for the version-identity criterion — `docs/implementation/Environment_Version_Manifest.md` (Draft, Version 0.1) — without changing the criterion's status: it remains Undefined and not yet eligible for assessment, since a Draft document may not be relied upon (`Documentation_Workflow.md:118`); eligibility requires that document to reach Published status and a separately authorized verification act to record values. Corrected the Rule explanatory text to state this Published-status precondition explicitly and to confirm this documentation task performs no assessment, verification act, or Milestone-Completion Status change. Reproducibility criterion evidence-location wording (Project Owner Option B: future committed setup script/configuration artifact plus a verifiable run result) unchanged. Milestone-Completion Status remains Not Assessed. No Phase, module, or milestone gate created; no implementation, runtime, or verification act authorized. |
| 0.3 | 2026-09-30 | Corrected the reproducibility criterion's evidence-location cell, Rule paragraph, and Dependencies entry to actually apply the Project Owner-approved Option B wording (committed setup script/configuration artifact plus a verifiable run result), which the Version 0.2 Revision History row incorrectly claimed was already present and unchanged — a read-only AI Review found the Version 0.2 cell still held the original pre-Option-B placeholder text, byte-identical to Version 0.1. This correction defines the evidence **format** only; the artifact and its run result do not yet exist. The criterion remains Undefined and not yet eligible for assessment. Milestone-Completion Status remains Not Assessed. No Phase, module, or milestone gate created; no implementation, runtime, or verification act authorized. |
| 0.4 | 2026-09-30 | Reference-only synchronization: corrected the version-identity criterion's citation of `docs/implementation/Environment_Version_Manifest.md` from Draft, Version 0.1 to Draft, Version 0.2, reflecting that document's addition of a Source Repository / Canonical Origin schema field and its lifecycle-reliance wording correction. No other change: the criterion remains Undefined and not yet eligible for assessment, since the manifest remains Draft and has not reached Published status. Milestone-Completion Status remains Not Assessed. No Phase, module, or milestone gate created; no implementation, runtime, or verification act authorized. |
| 0.5 | 2026-09-30 | Corrected a stale Dependencies and Blockers entry that said the version-manifest evidence "location and format are undefined," which contradicted this document's own Completion Criteria cell and Rule paragraph — both already stated that a proposed Draft evidence location exists (`docs/implementation/Environment_Version_Manifest.md`). Replaced with wording confirming the location is proposed but not yet authoritative, and that no value is recorded or verified. The ERPNext/Frappe criterion remains Undefined and not yet eligible for assessment. Milestone-Completion Status remains Not Assessed. No Phase, module, or milestone gate created; no implementation, runtime, or verification act authorized. |

---
