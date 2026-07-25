# 11 — Integration Designer

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

Define how external system integrations (payment gateways, shipping carriers, design/prepress tools, accounting exports, webhooks) are configured without hardcoding integration-specific logic throughout the codebase.

## Concept

The Integration Designer configures connections to external systems as data: endpoint, authentication reference, mapping rules, and trigger conditions — with the actual integration call implemented once, in the Infrastructure layer, as an adapter.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Integration | Named integration: type, target system, enabled status |
| PrintOS Integration Credential | Reference to securely stored credentials (never stored in plain configuration) |
| PrintOS Field Mapping | Maps PrintOS fields to external system fields |

## Rules

- Credentials/secrets are never stored as plain configuration field values — they are stored via Frappe's encrypted password fields or environment variables, referenced (not embedded) by the Integration record.
- Each integration type has exactly one Infrastructure-layer adapter implementation; the Integration Designer configures instances of that adapter, it does not generate new adapter code per installation.
- Outbound calls to external systems must handle failure gracefully (retries/backoff where appropriate) and log failures per [../technical/08_Error_Handling.md](../technical/08_Error_Handling.md).
- New integration types require a decision recorded in `docs/decisions` before implementation.

## Related Documents

- [08_Notification_Designer.md](08_Notification_Designer.md)
- [12_Feature_Flags.md](12_Feature_Flags.md)
- [../technical/09_Extensibility_Model.md](../technical/09_Extensibility_Model.md)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md)

---

# Future Considerations

- MachineIQ Service and Marketplace Service (Naming Registry Section 9) are expected to be configured as Integration Designer instances of a dedicated adapter, consistent with every other integration type — not as a special case.

---

# Open Questions

- Should integration retry/backoff policy be configurable per integration instance, or standardized centrally per integration type?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Reworded "per tenant" to "per installation" pending resolution of the Tenant/Company naming conflict. Added header metadata, Future Considerations, Open Questions, Revision History/Quality Checklist. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
