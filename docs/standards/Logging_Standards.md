# Logging Standards

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

Define consistent logging practices across `printos_core` so operational issues can be diagnosed without exposing sensitive data.

---

# Scope

Covers log levels, format, and content conventions for application logging. Does not cover monitoring/alerting infrastructure (see future `docs/blueprint/24_Deployment_Architecture.md`, per [ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)).

---

# Standards

- Use standard log levels: DEBUG, INFO, WARNING, ERROR, CRITICAL — applied consistently (e.g., business-rule violations log as WARNING, unexpected failures as ERROR).
- Log messages are structured and include context (entity type, entity ID, action) without embedding full sensitive payloads.
- Never log secrets, passwords, API keys, or full customer payment details, per `CLAUDE.md` security rules.
- Business-significant workflow transitions (see [Workflow_Standards.md](Workflow_Standards.md)) should be logged at INFO level for traceability.
- Errors are logged with enough context to reproduce the issue, but stack traces are never returned to end users (see [Error_Handling.md](Error_Handling.md)).

---

# Rationale

Consistent, safe logging supports debugging and future audit requirements (see [Security_Standards.md](Security_Standards.md)) without risking exposure of sensitive data, in line with the "Never expose secrets" rule in `CLAUDE.md`.

---

# Related Documents

- [Error_Handling.md](Error_Handling.md)
- [Security_Standards.md](Security_Standards.md)
- [Workflow_Standards.md](Workflow_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved path from `14_Deployment_Architecture.md` to `24_Deployment_Architecture.md` per ADR-010|
