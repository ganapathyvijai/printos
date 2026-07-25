# Release Process

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

Define how a set of completed changes moves from `develop` into a production release of PrintOS.

---

# Scope

Covers the process and approvals required to release. Does not cover deployment infrastructure (reserved for a future `docs/blueprint/24_Deployment_Architecture.md`, per [ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)) or branch structure (see [Branching_Strategy.md](Branching_Strategy.md)).

---

# Standards

- A release is cut from `develop` into `main` only once included features have passed testing per [Testing_Standards.md](Testing_Standards.md).
- Every release updates: Documentation, Roadmap, Architecture, and Change Log, per `PROJECT_RULES.md` Rule 19.
- A release is versioned according to [Versioning.md](Versioning.md) before tagging.
- No release proceeds if it requires an undocumented ERPNext core modification — this is a hard blocker, per `CLAUDE.md`.
- Production releases require explicit human approval; they are never triggered automatically by an AI assistant.
- Post-release, a rollback plan must be identifiable (e.g., prior tagged version) before the release is considered complete.

---

# Rationale

Tying release approval to documentation currency and testing completion enforces the Documentation-First and testing-mandatory principles at the one point where skipping them would have the highest cost — production.

---

# Related Documents

- [Testing_Standards.md](Testing_Standards.md)
- [Versioning.md](Versioning.md)
- [Branching_Strategy.md](Branching_Strategy.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved path from `14_Deployment_Architecture.md` to `24_Deployment_Architecture.md` per ADR-010|
