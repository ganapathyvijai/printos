# ADR-003: Documentation-First Development Workflow

Status:
Accepted

Date:
2026-07-18

---

## Context

PrintHub is developed collaboratively across a human project owner, ChatGPT (acting as architect/reviewer), and Claude (acting as implementer), over a multi-year horizon. Without a shared, durable record of architectural intent, decisions risk becoming implicit in code, inconsistent across contributors, or lost entirely when context is not carried between sessions or tools.

## Decision

Every architectural decision must be documented in `docs/blueprint` (and recorded as an ADR in `Decisions/` where it represents a discrete choice) **before** it is implemented in code. The Blueprint is the single source of truth for PrintHub/PrintOS architecture.

## Reasons

- Ensures architectural intent survives independently of any single contributor's memory or a specific AI session's context.
- Gives ChatGPT (architect/reviewer) and Claude (implementer) a shared reference to work from, reducing drift between design intent and implementation.
- Supports the Plan → Verify → Execute → Review workflow mandated in `CLAUDE.md`, where "Plan" is meaningless without durable documentation to plan against.
- Makes it possible to onboard new contributors (human or AI) without reverse-engineering intent from code alone.

## Consequences

- Feature work is blocked until its architectural basis is documented, which may slow initial delivery in exchange for long-term clarity.
- Documentation (Blueprint + Standards + Decisions) must be actively maintained; stale documentation is treated as a defect, not a low-priority cleanup item.
- Any code change that contradicts the Blueprint is a signal that either the code or the documentation is wrong, and the discrepancy must be resolved before proceeding.

## Related Documents

- `docs/blueprint/00_Master_Index.md`
- `docs/blueprint/README.md`
- `standards/Documentation_Standards.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
