# 10 — Implementation Guidelines

## Purpose

Concrete coding rules for anyone implementing PrintOS features, consistent with the architecture defined in documents 01–09.

## Development Workflow

Always follow: **Plan → Verify → Execute → Review**. Never skip verification (`pwd`, `git status`, `git branch`, confirm workspace) before creating files.

## Coding Standards

- Follow PEP8 for all Python code.
- Apply SOLID principles, Clean Architecture, and Domain-Driven Design.
- Favor modular design: small, single-responsibility modules over large multi-purpose files.

## Naming Conventions

- Python: `snake_case`.
- DocTypes: `PascalCase`.
- JavaScript: `camelCase`.

## Layer Discipline

- Before adding code, identify which layer it belongs to (see [03_Layer_Architecture.md](03_Layer_Architecture.md)) and confirm the dependency direction is legal (see [04_Dependency_Rules.md](04_Dependency_Rules.md)).
- Domain and Application code must not import `frappe` or `erpnext`.
- New use cases go in `application/<module>/use_cases/`; new ports in `application/<module>/ports/`; new adapters in `infrastructure/<module>/`.

## Documentation Requirement

- Every architectural decision must be recorded in `docs/blueprint` before implementation.
- Significant decisions also get an entry in `docs/decisions`.

## Testing

- Domain logic: pure unit tests, no Frappe site required.
- Application use cases: unit tests with fake/mock port implementations.
- Infrastructure adapters: integration tests against a real (test) Frappe site.

## Security

- Never hardcode secrets or passwords; use environment variables.
- Never expose secrets in logs, error messages, or client-side code.

## Version Control

- Never commit or push automatically — always ask first.
- Never perform destructive git operations without explicit confirmation.

## Related Documents

- [00_Master_Index.md](00_Master_Index.md)
- [08_Error_Handling.md](08_Error_Handling.md)
