# Workflow Standards

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

Define conventions for implementing business workflows and state transitions (e.g., Job Card lifecycle, Artwork approval) consistently across PrintOS.

---

# Scope

Covers state-machine and approval-workflow conventions for `printos_core` DocTypes. Does not cover the business definition of individual workflows (see `docs/blueprint/10_Business_Workflows.md`).

---

# Standards

- Every workflow implements the states and transitions already defined at the business level in `docs/blueprint/10_Business_Workflows.md`; new states are not introduced in code without a corresponding Blueprint update.
- State fields use a fixed, documented set of values (e.g., Job Card status: Scheduled, In Progress, Quality Check, Rework, Complete) — no free-text status fields.
- Transitions that require approval (e.g., Artwork Approval, Purchase Order approval above threshold) use Frappe's Workflow feature or an equivalent explicit approval mechanism, not implicit status changes.
- Invalid transitions must be rejected with a clear business-rule error message (see [Error_Handling.md](Error_Handling.md)), never silently allowed.
- Every workflow transition that has business significance should be logged (see [Logging_Standards.md](Logging_Standards.md)) for traceability.

---

# Rationale

Keeping code-level workflow states in lockstep with the Blueprint's documented workflows prevents divergence between "what the business thinks happens" and "what the system actually enforces" — a common source of ERP defects.

---

# Related Documents

- [Error_Handling.md](Error_Handling.md)
- [Logging_Standards.md](Logging_Standards.md)
- `docs/blueprint/10_Business_Workflows.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
