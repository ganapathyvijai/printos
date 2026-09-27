# Project Milestones — Governance Framework

**Status:** Draft
**Version:** 0.1
**Owner:** Project Owner
**Category:** Implementation

---

## Purpose

This document defines the shared framework, terminology, and governance conditions under which the individual milestone files `docs/milestones/M00_Project_Setup.md` through `M09` — existing, tracked, currently empty files — may be authored, assessed, and marked complete. It does not itself record completion of any milestone, and it does not define what any specific milestone requires. It governs *how* milestone completion is recorded elsewhere.

The following policy choices are recorded in this Version 0.1 as **adopted Project Owner decisions**:
- the two-track status model (Document Lifecycle Status vs. Milestone-Completion Status);
- the four-value Milestone-Completion Status vocabulary;
- contributor-propose / Owner-accept setting authority;
- the framework-authored-first sequencing decision;
- the absence of automatic milestone-to-milestone gating.

---

## Governance Boundary and Explicit Exclusions

Out of scope for this document, and for any milestone file it governs:

- Assigning phases, dates, owners, or estimates to any milestone.
- Authorizing or performing implementation, coding, or runtime work of any kind.
- Resolving any Architecture Review item or amending the Architecture Review Register.
- Authoring or amending the Development Roadmap (`docs/roadmap/01_Development_Roadmap.md`) or the Module Implementation Order (`docs/implementation/02_Module_Implementation_Order.md`).
- Declaring, or automatically implying, that any milestone is complete.
- Creating automatic dependency gating between milestones (see "Milestone Dependencies," below).

---

## Document Lifecycle Status vs. Milestone-Completion Status *(Adopted Project Owner decision)*

Two independent, non-substitutable state tracks apply to every milestone file. Neither may be inferred from the other.

| Track | Governs | Values | Recorded in |
|---|---|---|---|
| **Document Lifecycle Status** | Whether the milestone file's text is trustworthy/citable | Draft → Review → Revision → Approval → Published → Deprecated → Archived (`Documentation_Workflow.md` Section 5) | The milestone file's own `Status:` header |
| **Milestone-Completion Status** | Whether the real-world work the milestone describes has actually happened | Not Assessed, Assessment In Progress, Complete, Blocked | A distinct `Milestone-Completion Status:` field inside each milestone file — never the `Status:` header |

**Rule (adopted):** A milestone file's Document Lifecycle Status and its Milestone-Completion Status are tracked independently and neither constrains the other by default. Whether any minimum Document Lifecycle Status should be required before a non-default Milestone-Completion Status may be recorded is an **open, unresolved question** — see "Owner Decision Points."

---

## Milestone-Completion Status Vocabulary *(Adopted Project Owner decision)*

- **Not Assessed** — default state; no evidence yet gathered or reviewed.
- **Assessment In Progress** — evidence collection or review against the milestone's own criteria is underway.
- **Complete** — the Project Owner has explicitly accepted the evidence against the milestone's own defined criteria. May be set only by the Project Owner.
- **Blocked** — an identified, recorded dependency or condition prevents assessment or completion (see "Milestone Dependencies," below).

**Setting authority (adopted):** Any contributor may *propose* a Milestone-Completion Status (including proposing Complete or Blocked) with supporting evidence. Only the Project Owner may *accept* a proposed status or set a milestone to **Complete**. A proposed-but-unaccepted status has no standing beyond a proposal and does not change the file's recorded status.

---

## Evidence Requirements

Each milestone file must define, within its own text, the specific completion criteria, evidence types, and evidence locations applicable to it. This framework does not define what any milestone specifically requires. Evidence cited by a milestone file must be independently verifiable (a file path, commit hash, log, or other checkable artifact) — not an unverifiable assertion.

No milestone file may have a Milestone-Completion Status other than **Not Assessed** recorded against it until it has its own defined completion criteria in place.

---

## Milestone Dependencies *(Adopted Project Owner decision: no automatic gating)*

This framework introduces no automatic milestone-to-milestone gating. No milestone's Milestone-Completion Status is automatically constrained, inferred, or blocked by another milestone's status.

Any dependency between milestones (e.g., "M01 cannot begin assessment until M00 is Complete") must be explicitly recorded as text inside the *dependent* milestone's own file, citing the specific milestone and condition relied upon. Such a statement is a record, not an enforcement mechanism, and does not itself change any status.

---

## Review and Approval Conditions

This document, and each milestone file it governs, follows the fixed, non-skippable approval sequence defined in `Documentation_Workflow.md` Section 11: Author → AI Review (ChatGPT) → Architecture Review → Business Review → Owner Approval → Published. Per Section 11 (`:234`), "a document cannot skip a stage."

Within the Architecture Review and Business Review stages, the applicable review lenses from Section 7 (Technical, Business, Architecture, Naming, Consistency, Security, Standards, Cross-reference) must be explicitly considered for this document's content — not every lens will necessarily yield a finding, but none may be silently skipped (`Documentation_Workflow.md:162`).

Only a **Published** milestone file may be relied upon by any other document (`Documentation_Workflow.md:118`).

---

## Owner Decision Points Recorded and Unresolved by This Framework

**Adopted in this Version 0.1:**
- Milestone-Completion Status vocabulary: Not Assessed, Assessment In Progress, Complete, Blocked.
- Setting authority: contributors may propose; only the Project Owner may accept or set Complete.
- Authoring order: this framework is authored before any of `docs/milestones/M00_Project_Setup.md` through `M09` is drafted, as a sequencing decision — not a Document Lifecycle Status gate (see "Authoring Order," below).
- No automatic milestone-to-milestone gating; dependencies recorded explicitly per milestone.

**Unresolved — explicitly left open, not addressed by this Version 0.1:**
- Whether any minimum Document Lifecycle Status (e.g., Approval) must be reached by a milestone file before a Milestone-Completion Status other than Not Assessed may be recorded against it.
- Whether any minimum Document Lifecycle Status must be reached by this framework document itself before individual milestone files may begin drafting (see "Authoring Order," below — currently only an authoring-order sequencing decision exists, not a lifecycle gate).

---

## Non-Authorization Statement

This document, and no milestone file governed by it, authorizes implementation, coding, runtime execution, environment provisioning, or any Docker/service/script/migration activity, regardless of any milestone's recorded Milestone-Completion Status. Setting a milestone's status to Complete records that the Project Owner has accepted evidence of completed setup or process activity; it does not itself grant or constitute implementation authorization for any later milestone or module.

---

## Authoring Order for M00–M09 *(Adopted Project Owner decision)*

The Project Owner decision recorded is: this framework document is authored before drafting begins on any of `docs/milestones/M00_Project_Setup.md` through `M09` — existing, tracked, currently empty files. This is a sequencing decision only. It does not impose a Document Lifecycle Status precondition (e.g., this framework reaching Approval or Published) on when milestone drafting may begin; whether such a precondition should also apply is recorded above as unresolved.

---

## Related Documents

- `docs/milestones/M00_Project_Setup.md` through `M09` — existing, tracked, currently empty files governed by this framework.
- `docs/Documentation_Workflow.md` (Sections 5, 7, 8, 11)
- `docs/implementation/00_Implementation_Index.md`
- `docs/implementation/Architecture_Freeze.md` (governance-boundary precedent, Section 3)

---

## Revision History

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-27 | Initial Draft — defines the two-track status model, the Not Assessed / Assessment In Progress / Complete / Blocked vocabulary, contributor-propose / Owner-accept setting authority, explicit (non-automatic) milestone dependency recording, and the framework-authored-first sequencing decision, all adopted Project Owner decisions. Assigns no phase, date, owner, estimate, or completion to any milestone. Records the Document-Lifecycle/Milestone-Completion interaction and the framework-authoring-precondition question as explicitly unresolved. |

---
