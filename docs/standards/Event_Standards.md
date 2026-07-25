# Event Standards

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

Define conventions for naming and structuring domain events raised within `printos_core`, in anticipation of event-driven integration needs (e.g., MachineIQ, future Marketplace).

---

# Scope

Covers event naming, payload conventions, and emission conventions. Does not cover the messaging infrastructure/broker choice, which is a future architecture decision.

---

# Standards

- Event names use the pattern `<entity>.<action>` in snake_case (e.g., `job_card.completed`, `artwork.approved`, `sales_order.confirmed`).
- Event names reflect business facts already defined in `docs/blueprint/10_Business_Workflows.md`, not implementation-level occurrences.
- Event payloads carry the minimum data needed for a subscriber to act, referencing entity IDs rather than duplicating full entity state.
- Events are emitted only for state transitions with genuine business significance (avoid noisy, low-value events).
- Event producers do not assume knowledge of consumers — an event is a fact, not a command directed at a specific module.

---

# Rationale

Consistent event naming and payload discipline now makes it straightforward to introduce a formal event-driven integration layer later (e.g., for MachineIQ or Marketplace) without renaming or restructuring existing events, per the future-first mindset in `PROJECT_RULES.md` Rule 24.

---

# Related Documents

- [API_Standards.md](API_Standards.md)
- [Logging_Standards.md](Logging_Standards.md)
- `docs/blueprint/10_Business_Workflows.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
