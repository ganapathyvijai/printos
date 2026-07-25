# API Standards

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

Define conventions for designing and exposing APIs from `printos_core`, whether consumed by the PrintOS frontend, future mobile apps, or future integrations.

---

# Scope

Covers REST API conventions for endpoints added by `printos_core`. Does not cover ERPNext's own built-in REST API, and does not define specific endpoint implementations.

---

# Standards

- APIs follow REST conventions: resource-oriented paths, appropriate HTTP verbs (GET/POST/PUT/DELETE), and standard status codes.
- All custom endpoints are namespaced under `printos_core` (e.g., `/api/method/printos_core.<module>.<action>`).
- Request/response payloads use consistent field naming (snake_case, matching [Naming_Standards.md](Naming_Standards.md)).
- Every endpoint enforces authentication and role-based permission checks consistent with [Security_Standards.md](Security_Standards.md).
- Errors are returned in a consistent structure (see [Error_Handling.md](Error_Handling.md)), never as raw stack traces.
- Breaking changes to an existing endpoint require a version increment (see [Versioning.md](Versioning.md)), not silent modification of the existing contract.
- APIs intended for future Mobile App or Marketplace consumption must be designed statelessly, without assuming a specific frontend.

---

# Rationale

Consistent, well-namespaced APIs isolate `printos_core` functionality from ERPNext internals and prepare the platform for future consumers (mobile, marketplace, integrations) without requiring redesign, consistent with the API-first principle in `CHATGPT.md`.

---

# Related Documents

- [Error_Handling.md](Error_Handling.md)
- [Security_Standards.md](Security_Standards.md)
- [Versioning.md](Versioning.md)
- [Event_Standards.md](Event_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
