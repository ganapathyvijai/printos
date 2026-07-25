# 14 — Template Library

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-23

---

## Purpose

Define how reusable configuration templates (default workflows, form layouts, dashboard sets, automation packs) are maintained and applied, so new tenants can be onboarded quickly with proven defaults instead of configuring everything from scratch.

## Concept

A template is a named, versioned bundle of configuration records for one or more designers (e.g. a "Standard Print Shop" template bundling a default Workflow, Approval chain, and Dashboard set). Templates are the middle tier in the configuration resolution order defined in [13_Tenant_Customization.md](13_Tenant_Customization.md).

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Configuration Template | Named template, version, target designer(s) |
| PrintOS Template Item | Individual configuration record included in the template |

## Rules

- Templates are versioned; applying a new template version to an existing tenant is an explicit, reviewed action — never a silent overwrite of a tenant's customized configuration.
- A tenant that has customized a value the template provides keeps its override; template updates only affect values the tenant has not overridden.
- New templates and significant template changes are recorded in `docs/blueprint`.

## Related Documents

- [13_Tenant_Customization.md](13_Tenant_Customization.md)
- [15_Deployment_Model.md](15_Deployment_Model.md)

---

# Future Considerations

- This is the intended home for future Industry Templates and Configuration Packages (bundles targeting a specific print-industry vertical, e.g. signage vs. packaging) — the Template/Template Item data model is designed to support that without redesign.

---

# Open Questions

- Should templates be versioned independently per included designer (Workflow, Form, Dashboard, etc.), or as a single bundled version across the whole template?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist to match the Blueprint documentation template. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
