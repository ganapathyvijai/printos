# Documentation Standards

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

Define how PrintOS documentation — both the Blueprint and inline code documentation — is written and kept current.

---

# Scope

Covers documentation conventions for `docs/blueprint`, the `standards` folder itself, and code-level documentation in `printos_core`. Does not define the content of any specific Blueprint document.

---

# Standards

- Every architectural decision is documented in `docs/blueprint` **before** implementation, per `CLAUDE.md`'s Documentation-First rule.
- Blueprint documents follow the established template (Purpose, Scope, Background, Main Content, Architecture Notes, Future Considerations, Open Questions, Related Documents, Revision History, Quality Checklist).
- Standards documents (this folder) follow a lighter template (Purpose, Scope, Standards, Rationale, Related Documents, Revision History) since they are prescriptive rules rather than architecture narratives.
- Code comments explain WHY, never WHAT — matching `PROJECT_RULES.md` Rule 16.
- Every module should document its Purpose, Owner, Dependencies, and Future Roadmap, per `PROJECT_RULES.md` Rule 12.
- Every completed feature updates: Documentation, Roadmap, Architecture, and Change Log, per `PROJECT_RULES.md` Rule 19.
- Revision History tables are updated on every substantive change, not just version-number bumps.

---

# Rationale

Documentation-first is a core operating principle of this project (`CLAUDE.md`, `CHATGPT.md`). Standardizing the documentation format itself ensures that this principle is followed consistently rather than left to individual judgment.

---

# Related Documents

- `docs/blueprint/00_Master_Index.md`
- [Coding_Standards.md](Coding_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
