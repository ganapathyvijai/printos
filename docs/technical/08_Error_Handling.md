# 08 — Error Handling

## Purpose

Define a consistent error handling and exception strategy across all layers of `printos_core`.

## Principles

- Each layer raises errors meaningful to itself; translation happens only when crossing a layer boundary.
- Domain and Application layers never catch-and-hide errors to "fail silently" — invariant violations must surface.
- The Interface layer is the only place framework-facing errors (HTTP status codes, `frappe.throw`, user-facing messages) are produced.

## Exception Hierarchy

| Layer | Exception type | Example |
|-------|-----------------|---------|
| Domain | Domain-specific exceptions (plain Python, e.g. `InvalidEstimateError`) | Business rule violated |
| Application | Application exceptions wrapping/aggregating Domain exceptions | Use case cannot complete |
| Infrastructure | Adapter exceptions (wraps ERPNext/DB/external errors) | DocType validation failure, network error |
| Interface | Translates all of the above into `frappe.throw` / API error responses | HTTP 400/403/500 |

## Rules

1. Domain exceptions are plain Python exceptions with no `frappe` dependency.
2. Infrastructure adapters catch ERPNext/Frappe-specific exceptions and re-raise as Application-layer exceptions (never let raw `frappe.exceptions.*` leak into Domain/Application).
3. Interface layer catches Application-layer exceptions and converts them to user-facing errors with appropriate messages — no stack traces or internal details exposed to end users.
4. Never use bare `except:` — always catch specific exception types.
5. Never swallow exceptions silently; log with context before handling.

## Logging

- Log at the boundary where an exception is translated, including enough context (module, use case, relevant IDs) to trace the issue.
- Never log secrets, passwords, or sensitive customer data.

## Related Documents

- [07_Request_Lifecycle.md](07_Request_Lifecycle.md)
- [10_Implementation_Guidelines.md](10_Implementation_Guidelines.md)
