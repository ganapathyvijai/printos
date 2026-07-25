# 09 — Extensibility Model

## Purpose

Define how PrintOS extends ERPNext functionality without ever modifying ERPNext core, so upgrades remain safe and customizations remain isolated.

## Core Rule

> ERPNext core is never modified. All customizations belong inside `printos_core`.

## Extension Mechanisms

1. **Custom DocTypes** — New business entities (e.g. `Print Job`, `Estimate Line`) are created as custom doctypes within `printos_core`, not by editing ERPNext's existing doctypes.
2. **Custom Fields** — When ERPNext's existing doctypes need additional data, use Custom Fields/Fixtures defined in `printos_core`, applied via migrations — never by hand-editing ERPNext doctype JSON.
3. **Hooks (`hooks.py`)** — Use Frappe's hook system (`doc_events`, `override_doctype_class`, `scheduler_events`, etc.) to attach PrintOS behavior to ERPNext events, instead of editing ERPNext source.
4. **Server Scripts / Client Scripts** — Used sparingly for lightweight customization; any script with real business logic should call into an Application-layer use case rather than embedding logic inline.
5. **Overrides** — Where ERPNext explicitly supports controller overrides (`override_doctype_class`), PrintOS subclasses rather than forking.

## What Is Never Done

- Editing files inside ERPNext/Frappe app directories.
- Monkey-patching ERPNext internals at runtime.
- Forking ERPNext to add PrintOS features.

## Upgrade Safety

Because all customization lives in `printos_core` and uses supported extension points, ERPNext version upgrades only require re-validating adapters in the Infrastructure layer — Domain and Application layers are unaffected.

## Related Documents

- [01_Architecture_Overview.md](01_Architecture_Overview.md)
- [04_Dependency_Rules.md](04_Dependency_Rules.md)
