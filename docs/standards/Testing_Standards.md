# Testing Standards

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

Define expectations for automated testing of `printos_core` so business-critical functionality is verifiably correct and regressions are caught before release.

---

# Scope

Covers unit, integration, and workflow-level testing conventions for `printos_core`. Does not cover manual QA process, which belongs to [Release_Process.md](Release_Process.md).

---

# Standards

- Testing is **mandatory** for business-critical features (pricing/estimation logic, workflow transitions, financial calculations), per `PROJECT_RULES.md` Rule 18.
- Domain layer logic is unit-tested independently of Frappe/ERPNext, made possible by the Clean Architecture separation in [Coding_Standards.md](Coding_Standards.md).
- Integration tests verify behavior against a real (non-mocked) database/environment for anything touching persistence or ERPNext-specific behavior.
- Workflow tests verify that only valid state transitions (see [Workflow_Standards.md](Workflow_Standards.md)) succeed and invalid ones are rejected.
- Tests are written before or alongside the feature they cover, not deferred to a later cleanup pass.
- A failing test blocks merge; tests are not skipped or disabled to unblock a release without explicit, documented approval.

---

# Rationale

Business-critical ERP logic (pricing, tax, inventory) has direct financial consequences if wrong. Mandatory testing for these areas, combined with real (not mocked) integration tests, prevents the class of defect where a mocked test passes but real behavior diverges.

---

# Related Documents

- [Coding_Standards.md](Coding_Standards.md)
- [Workflow_Standards.md](Workflow_Standards.md)
- [Release_Process.md](Release_Process.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
