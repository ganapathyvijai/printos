# PrintHub Standards

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-22

---

## What This Folder Is

This folder contains the engineering standards that govern how PrintOS is built on top of ERPNext. Where `docs/blueprint` defines *what* PrintOS is and *why* (business architecture), `standards` defines *how* it must be built (engineering conventions and rules) so that contributions from any developer — human or AI — remain consistent, upgrade-safe, and maintainable.

## Purpose

Standards exist to remove ambiguity from day-to-day engineering decisions: naming, code structure, database conventions, API design, testing, git workflow, and more. They translate the principles already stated in `CLAUDE.md` and `PROJECT_RULES.md` (SOLID, Clean Architecture, DDD, upgrade safety) into concrete, checkable rules.

## How to Use These Standards

- Consult the relevant standard before writing code, not after — these are conventions to follow, not a retroactive checklist.
- If a standard conflicts with a specific ERPNext/Frappe constraint, the constraint wins for that instance, but the conflict should be recorded (see [Documentation_Standards.md](Documentation_Standards.md)).
- Standards apply to all code inside `printos_core`. ERPNext core is never modified, so these standards do not apply to ERPNext core itself.

## Index

| Standard | Covers |
|---|---|
| [Naming_Standards.md](Naming_Standards.md) | snake_case / PascalCase / camelCase conventions |
| [Coding_Standards.md](Coding_Standards.md) | PEP8, SOLID, Clean Architecture, DDD application |
| [Database_Standards.md](Database_Standards.md) | Table/field conventions, migrations |
| [DocType_Standards.md](DocType_Standards.md) | ERPNext DocType design conventions |
| [Workflow_Standards.md](Workflow_Standards.md) | Business workflow/state-machine conventions |
| [API_Standards.md](API_Standards.md) | REST API design conventions |
| [Event_Standards.md](Event_Standards.md) | Event naming and event-driven conventions |
| [Logging_Standards.md](Logging_Standards.md) | Logging levels, format, conventions |
| [Error_Handling.md](Error_Handling.md) | Exception handling and error response conventions |
| [Testing_Standards.md](Testing_Standards.md) | Test coverage expectations and test types |
| [Documentation_Standards.md](Documentation_Standards.md) | Code and module documentation conventions |
| [UI_UX_Standards.md](UI_UX_Standards.md) | Frontend/UI consistency conventions |
| [Git_Workflow.md](Git_Workflow.md) | Commit, PR, and repository conventions |
| [Release_Process.md](Release_Process.md) | Release and deployment approval process |
| [Branching_Strategy.md](Branching_Strategy.md) | Branch naming and lifecycle |
| [Versioning.md](Versioning.md) | Version numbering conventions |
| [Performance_Standards.md](Performance_Standards.md) | Performance expectations and practices |
| [Security_Standards.md](Security_Standards.md) | Secure coding conventions |

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
