# M00 — Project Setup

**Status:** Draft
**Version:** 0.1
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
| ERPNext/Frappe framework version pinned and verified | Exact commit/version record | **Undefined** — no current file names a version-manifest location. |
| `printos_core` app scaffold created per Clean Architecture rules | File/module tree listing | `platform/` app structure (cited only; not verified by this record) |
| Documentation governance structure in place | Tracked file listing | `docs/Documentation_Workflow.md`, `docs/templates/` (existence and content directly checkable via `git ls-files`) |
| Naming Registry and ADR process operational | Tracked file content | `docs/decisions/00_ADR_Index.md`, `docs/standards/Naming_Registry.md` |
| Development environment reproducibility confirmed | Setup/build log or checklist | **Undefined** — no current file names a location or format for this evidence. |

**Rule:** Each completion criterion above may be assessed only after that specific criterion's own evidence location and evidence format are defined. The two criteria currently marked Undefined — the ERPNext/Frappe version-manifest evidence and the development-environment reproducibility evidence — cannot yet be assessed, since neither has a defined evidence location or format. M00 as a whole remains recorded as **Milestone-Completion Status: Not Assessed** until its criteria are complete enough for an Owner-approved assessment to begin. This rule creates no Phase, module, or milestone gate; it governs only when M00's own Milestone-Completion Status may next change.

---

## Dependencies and Blockers (Unresolved)

- The ERPNext/Frappe version-manifest evidence location and format are undefined.
- The development-environment reproducibility evidence location and format are undefined.
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

---
