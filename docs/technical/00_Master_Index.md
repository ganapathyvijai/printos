# Technical Documentation — Master Index

Master index for PrintOS technical architecture documentation. This set of documents defines how PrintOS is structured, how modules communicate, and the rules that govern all future development on top of ERPNext.

## Contents

| # | Document | Purpose |
|---|----------|---------|
| 01 | [Architecture Overview](01_Architecture_Overview.md) | High-level view of PrintOS on ERPNext |
| 02 | [Clean Architecture](02_Clean_Architecture.md) | Clean Architecture principles applied to PrintOS |
| 03 | [Layer Architecture](03_Layer_Architecture.md) | Layer definitions and responsibilities |
| 04 | [Dependency Rules](04_Dependency_Rules.md) | Allowed and forbidden dependency directions |
| 05 | [Project Structure](05_Project_Structure.md) | Directory and module layout of `printos_core` |
| 06 | [Module Communication](06_Module_Communication.md) | How modules talk to each other |
| 07 | [Request Lifecycle](07_Request_Lifecycle.md) | End-to-end flow of a request through the system |
| 08 | [Error Handling](08_Error_Handling.md) | Error handling and exception strategy |
| 09 | [Extensibility Model](09_Extensibility_Model.md) | How PrintOS is extended without modifying ERPNext core |
| 10 | [Implementation Guidelines](10_Implementation_Guidelines.md) | Coding rules and conventions for implementation |

## Governing Principles

- ERPNext is the framework. PrintOS is the product.
- ERPNext core is never modified. All customizations live inside `printos_core`.
- Every architectural decision recorded here must be reflected in `docs/blueprint` before implementation begins.
- Development always follows: **Plan → Verify → Execute → Review**.

## Status

This documentation set is a living reference. Update it whenever an architectural decision changes, and cross-link related decisions in `docs/decisions`.
