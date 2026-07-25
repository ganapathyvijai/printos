# 05 — Project Structure

## Purpose

Define the directory layout of `printos_core` and where each architectural layer lives on disk, so structure is consistent as the codebase grows.

## Top-Level Layout

```
printos_core/
├── domain/            # Domain layer: entities, value objects, business rules
│   └── <module>/
├── application/        # Application layer: use cases, ports (interfaces), DTOs
│   └── <module>/
│       ├── use_cases/
│       └── ports/
├── infrastructure/     # Infrastructure layer: ERPNext adapters, repositories
│   └── <module>/
│       ├── doctype/
│       └── repositories/
├── interface/           # Interface layer: API, client scripts, portal pages
│   └── <module>/
│       ├── api/
│       └── client_scripts/
├── hooks.py             # Frappe app hooks (wiring only, no business logic)
├── config/
└── tests/
    ├── domain/
    ├── application/
    └── infrastructure/
```

## Module Organization

Each business module (e.g. `estimating`, `production`, `materials`) is vertically sliced across all four layers using the same module name, so related code is easy to locate:

```
domain/estimating/
application/estimating/
infrastructure/estimating/
interface/estimating/
```

## Naming

- Python: `snake_case` for files, functions, variables.
- DocTypes: `PascalCase`.
- JavaScript (client scripts): `camelCase`.

## Related Documents

- [03_Layer_Architecture.md](03_Layer_Architecture.md)
- [10_Implementation_Guidelines.md](10_Implementation_Guidelines.md)
