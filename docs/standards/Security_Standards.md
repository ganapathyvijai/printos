# Security Standards

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

Define secure coding conventions for `printos_core` so PrintOS protects business and customer data by default.

---

# Scope

Covers secure coding practice at the application level. Does not cover infrastructure-level security architecture (reserved for future `docs/blueprint/23_Security_Architecture.md`, per [ADR-010-Blueprint-Numbering-Strategy.md](../decisions/ADR-010-Blueprint-Numbering-Strategy.md)).

---

# Standards

- Never hardcode passwords, secrets, API keys, or database credentials — always use environment variables, per `CLAUDE.md`.
- Never expose secrets in logs, error messages, or API responses (see [Logging_Standards.md](Logging_Standards.md), [Error_Handling.md](Error_Handling.md)).
- All custom API endpoints enforce authentication and role-based permission checks; no endpoint assumes trust based on network location alone.
- User input is validated and sanitized before use in queries, file operations, or rendered output, to prevent injection (SQL injection, XSS) and other OWASP Top 10 risks.
- Sensitive business data (payment details, statutory tax IDs) is handled only through Frappe/ERPNext's supported secure storage mechanisms, never custom plaintext storage.
- Any new dependency is checked for known vulnerabilities before adoption.
- Access to production data and systems follows least-privilege principles.

---

# Rationale

Security failures in an ERP handling financial and customer data are high-impact and often irreversible (data breach, statutory non-compliance). These standards operationalize the security rules already stated in `CLAUDE.md` and `PROJECT_RULES.md` Rule 11 at the code level.

---

# Related Documents

- [Error_Handling.md](Error_Handling.md)
- [Logging_Standards.md](Logging_Standards.md)
- [API_Standards.md](API_Standards.md)
- [Database_Standards.md](Database_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
|1.1|2026-07-22|Documentation Consistency Fix|Updated reserved path from `13_Security_Architecture.md` to `23_Security_Architecture.md` per ADR-010|
