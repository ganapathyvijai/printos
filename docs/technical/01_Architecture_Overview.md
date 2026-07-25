# 01 — Architecture Overview

## Purpose

PrintOS is a commercial-grade ERP product built on top of ERPNext. ERPNext supplies the underlying framework (doctypes, workflow engine, permissions, UI scaffolding, REST/RPC layer). PrintOS supplies the business product: printing-industry-specific processes, data models, and workflows.

## Position in the Stack

```
┌──────────────────────────────┐
│           PrintOS            │  ← business product (this layer)
│   (printos_core app/module)  │
├──────────────────────────────┤
│           ERPNext             │  ← framework (never modified)
├──────────────────────────────┤
│           Frappe               │  ← platform runtime
└──────────────────────────────┘
```

## Core Principles

1. **ERPNext core is immutable.** No file inside ERPNext core apps is ever edited directly.
2. **All customization is additive.** PrintOS extends ERPNext through its own app, `printos_core`, using standard Frappe extension points (custom doctypes, hooks, overrides, server scripts, client scripts).
3. **Clean Architecture applies inside `printos_core`.** Business logic is isolated from framework/UI concerns (see [02_Clean_Architecture.md](02_Clean_Architecture.md)).
4. **Documentation precedes implementation.** Every architectural decision is recorded in `docs/blueprint` before code is written.

## High-Level Components

- **Domain** — printing-industry business rules (jobs, orders, estimating, production, materials).
- **Application** — use cases/services orchestrating domain logic.
- **Infrastructure** — ERPNext/Frappe integration (doctypes, ORM, background jobs, APIs).
- **Interface** — UI (desk/portal/web), REST endpoints, client scripts.

See [03_Layer_Architecture.md](03_Layer_Architecture.md) for details on each layer.

## Related Documents

- [02_Clean_Architecture.md](02_Clean_Architecture.md)
- [05_Project_Structure.md](05_Project_Structure.md)
- [09_Extensibility_Model.md](09_Extensibility_Model.md)
