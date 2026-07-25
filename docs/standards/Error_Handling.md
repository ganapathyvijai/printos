# Error Handling

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

Define consistent conventions for raising, handling, and communicating errors across `printos_core`.

---

# Scope

Covers business-rule violations, validation errors, and unexpected failures within `printos_core` code and APIs. Does not cover ERPNext core error handling.

---

# Standards

- Business-rule violations (e.g., "Job Card cannot start without Approved Artwork") raise explicit, descriptive exceptions — never silently ignored or bypassed.
- User-facing error messages are clear and actionable; internal stack traces are never surfaced to end users or API responses.
- API errors follow a consistent structure (error code, message, optional field-level detail), consistent with [API_Standards.md](API_Standards.md).
- Validation errors are distinguished from system/infrastructure errors, so callers can distinguish "fix your input" from "try again later."
- Every caught exception that represents an unexpected failure is logged per [Logging_Standards.md](Logging_Standards.md) before being surfaced.
- Errors are not used for normal control flow; they represent genuine exceptional conditions.

---

# Rationale

Consistent error handling protects data integrity (invalid transitions are rejected, not silently allowed) and prevents leaking internal implementation details to end users, supporting both usability and security.

---

# Related Documents

- [API_Standards.md](API_Standards.md)
- [Logging_Standards.md](Logging_Standards.md)
- [Workflow_Standards.md](Workflow_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
