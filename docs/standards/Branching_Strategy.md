# Branching Strategy

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-22

---

# Purpose

Define the branch structure and lifecycle used across the PrintHub repository.

---

# Scope

Covers branch naming and purpose. Does not cover commit/PR conventions (see [Git_Workflow.md](Git_Workflow.md)) or release timing (see [Release_Process.md](Release_Process.md)).

---

# Standards

| Branch | Purpose |
|---|---|
| `main` | Stable, production-ready code |
| `develop` | Integration branch for completed features awaiting release |
| `feature/*` | Active development of a single feature (e.g., `feature/job-card-scheduling`) |
| `bugfix/*` | Fixes for defects found in `develop` |
| `hotfix/*` | Urgent fixes applied directly against production (`main`) |

- Feature branches are created from `develop` and merged back via reviewed Pull Request.
- Hotfix branches are created from `main` and merged back into both `main` and `develop` to keep history consistent.
- Branch names use kebab-case after the type prefix (e.g., `feature/machine-scheduling-conflict-check`).
- Branches are deleted after merge unless there is a specific reason to retain them, and deletion follows the destructive-operation confirmation rule in [Git_Workflow.md](Git_Workflow.md).

---

# Rationale

This structure (per `PROJECT_RULES.md` Rule 20) separates stable production code from in-progress integration work, and gives urgent production fixes (hotfixes) a clear, fast path that still reconciles with ongoing development.

---

# Related Documents

- [Git_Workflow.md](Git_Workflow.md)
- [Release_Process.md](Release_Process.md)
- [Versioning.md](Versioning.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
