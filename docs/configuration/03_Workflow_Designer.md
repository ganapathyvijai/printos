# 03 — Workflow Designer

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

Define how document workflows (state machines) are configured for PrintOS transactional documents (Sales Order, Quotation, Job Card, etc. — per the Approved Entity Naming Registry in `docs/standards/Naming_Registry.md`, Section 13) without hardcoding states/transitions in code.

## Concept

The Workflow Designer lets an administrator define states and transitions for a document type, building on Frappe's native Workflow engine while keeping PrintOS-specific transition rules in the Application layer.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Workflow | Declares the target document type and overall workflow |
| PrintOS Workflow State | States within a workflow (e.g. Draft, Estimated, Approved, In Production, Completed) |
| PrintOS Workflow Transition | Allowed transitions between states, with guard conditions and required roles |

## Rules

- Guard conditions (business rules deciding whether a transition is allowed) are evaluated by Application-layer use cases, not embedded as ad-hoc scripts inside the workflow record.
- State names and transitions must be reviewed against [database/06_Data_Lifecycle.md](../database/06_Data_Lifecycle.md) so lifecycle stages remain consistent.
- Every workflow change affecting a production tenant is tested in a non-production environment first (see [15_Deployment_Model.md](15_Deployment_Model.md)).

## Relationship to Approval Designer

Transitions that require sign-off delegate to the Approval Designer (see [04_Approval_Designer.md](04_Approval_Designer.md)) rather than duplicating approval logic within the workflow definition.

## Related Documents

- [04_Approval_Designer.md](04_Approval_Designer.md)
- [07_Automation_Rules.md](07_Automation_Rules.md)
- [../technical/07_Request_Lifecycle.md](../technical/07_Request_Lifecycle.md)
- [../blueprint/10_Business_Workflows.md](../blueprint/10_Business_Workflows.md)
- [../database/06_Data_Lifecycle.md](../database/06_Data_Lifecycle.md)

---

# Future Considerations

- Workflow definitions authored here must remain traceable to the business-level workflows in `Blueprint 10_Business_Workflows.md`; a configured workflow state machine should never introduce a business transition that document does not describe.

---

# Open Questions

- Should workflow state names be validated at configuration time against `docs/standards/Workflow_Standards.md`'s approved status vocabulary, or is that left to the Form Designer's status field?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Replaced non-Approved example document names ("Print Job, Estimate, Production Order") with Approved Entity Naming Registry terms ("Sales Order, Quotation, Job Card") — "Estimate" is Deprecated (see ADR-013) and "Production Order" is Rejected (see ADR-014). Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
