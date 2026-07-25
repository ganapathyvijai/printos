# 07 — Security Architecture

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

# Purpose

Describe PrintOS's system-wide security architecture — authentication, authorization, data protection, and secure defaults — consolidating and cross-referencing the security-relevant rules already scattered across `CLAUDE.md`, `PROJECT_RULES.md`, and `docs/standards/Security_Standards.md`.

---

# Scope

Covers architectural security posture at the system level. Does not restate detailed engineering rules already defined in `docs/standards/Security_Standards.md`, which remains authoritative for concrete requirements (e.g. password hashing, session handling specifics).

---

# Background

Security in PrintOS is not a bolt-on layer; it is enforced through the same Clean Architecture boundaries and ERPNext extension discipline used everywhere else. This document exists to make that explicit at the architecture level, since security decisions (where permission checks live, how secrets are stored, how tenant/Company isolation is enforced) cut across every layer and every module.

---

# Main Content

## Authentication

- Authentication is handled entirely by Frappe/ERPNext's existing mechanism (session-based auth, API keys/secrets); PrintOS does not implement a parallel authentication system.
- OTP (One-Time Password) is an Approved abbreviation (`Naming_Registry.md` Section 19) reserved for Security/Authentication use where multi-factor flows are introduced.

## Authorization

- Authorization is enforced at the DocType/Role/Permission level (ERPNext native) plus PrintOS-specific extensions configured through [../configuration/09_Role_Permission_Designer.md](../configuration/09_Role_Permission_Designer.md).
- Application-layer use cases must not assume the Interface layer has already checked permissions adequately for the specific business action being performed — sensitive use cases perform their own authorization check rather than relying solely on generic DocType-level permission.

## Data Protection

- Secrets, passwords, and credentials are never hardcoded; they are stored via environment variables or Frappe's encrypted password fields, per `PROJECT_RULES.md` Rule 11 and `CLAUDE.md`'s Security section.
- Integration credentials specifically follow [../configuration/11_Integration_Designer.md](../configuration/11_Integration_Designer.md)'s rule that credentials are referenced, never embedded, in configuration records.
- Tenant/Company-scoped data must not leak across scope boundaries — see [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md) for the current (working-draft) isolation model.

## Secure Defaults

| Concern | Default |
|---|---|
| SQL | Parameterized queries only; no string-interpolated SQL (see [../database/01_Data_Architecture.md](../database/01_Data_Architecture.md)) |
| Notification templates | Escape-by-default to prevent XSS (see [../configuration/08_Notification_Designer.md](../configuration/08_Notification_Designer.md)) |
| Automation condition expressions | Constrained, safely evaluated syntax — no raw `eval` (see [../configuration/07_Automation_Rules.md](../configuration/07_Automation_Rules.md)) |
| Error messages | No stack traces or internal detail exposed to end users at the Interface layer (see [../technical/08_Error_Handling.md](../technical/08_Error_Handling.md)) |

## Auditability

Approval decisions, workflow transitions, and automation runs are all logged and retained (see [../configuration/04_Approval_Designer.md](../configuration/04_Approval_Designer.md), [../database/06_Data_Lifecycle.md](../database/06_Data_Lifecycle.md)) — auditability is treated as a security control, not just an operational nicety, since it is what makes unauthorized or mistaken changes detectable after the fact.

---

# Architecture Notes

This document deliberately does not introduce new security mechanisms beyond what is already required by `Security_Standards.md`, `PROJECT_RULES.md`, and the Configuration Studio documents — its purpose is to show that those individually-stated rules compose into a coherent whole, not to add new obligations.

---

# Future Considerations

- Multi-tenant data isolation guarantees will need formal security review once [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md) is reconciled with the reserved `docs/blueprint/25_MultiTenant_Architecture.md`.
- Marketplace (Phase 5) introduces public-facing, unauthenticated or lightly-authenticated traffic for the first time; its security architecture will need a dedicated review once scoped (per [ADR-009](../decisions/ADR-009-Marketplace.md)).

---

# Open Questions

- Should a dedicated Security Review checklist be added to the Configuration Studio's Documentation Quality Checklist, given how many security-relevant rules (credentials, SQL, XSS, condition evaluation) live there?

---

# Related Documents

- `docs/standards/Security_Standards.md`
- [../configuration/09_Role_Permission_Designer.md](../configuration/09_Role_Permission_Designer.md)
- [../configuration/11_Integration_Designer.md](../configuration/11_Integration_Designer.md)
- [../technical/08_Error_Handling.md](../technical/08_Error_Handling.md)
- [04_MultiTenant_Architecture.md](04_MultiTenant_Architecture.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
