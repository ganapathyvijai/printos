# 07 — Request Lifecycle

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-26

---

## Purpose

Trace the end-to-end path of a request through PrintOS, from entry point to response, so contributors know exactly where each concern belongs.

## Lifecycle Stages

1. **Entry (Interface layer)** — A request arrives via a whitelisted API method, a Desk client script action, or a portal page. The Interface layer parses/validates input shape only (not business rules) and calls the relevant Application use case.
2. **Use Case Execution (Application layer)** — The use case orchestrates the operation: it loads data through ports, applies Domain rules, and requests persistence through ports. It has no knowledge of HTTP, Frappe requests, or the UI.
3. **Domain Rule Evaluation (Domain layer)** — Business rules, invariants, and calculations execute here. Pure logic, no I/O.
4. **Persistence / Side Effects (Infrastructure layer)** — Repository/adapter implementations translate Application-layer calls into ERPNext doctype operations, database writes, background jobs, or external API calls.
5. **Response (back through Interface layer)** — The use case returns a result/DTO; the Interface layer formats it into the API response, UI update, or redirect.

## Diagram

```
Client ──▶ Interface ──▶ Application ──▶ Domain
                              │
                              ▼
                       Infrastructure ──▶ ERPNext/Frappe/DB
                              │
Client ◀── Interface ◀── Application (result)
```

## Error Propagation

Errors raised in Domain/Application layers are translated into user-facing errors only at the Interface layer. See [08_Error_Handling.md](08_Error_Handling.md).

## Related Documents

- [03_Layer_Architecture.md](03_Layer_Architecture.md)
- [08_Error_Handling.md](08_Error_Handling.md)

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-26 | Metadata Normalization | Added document-control metadata (Version, Status, Owner, Last Updated, Revision History). No architectural or technical content changed. |
