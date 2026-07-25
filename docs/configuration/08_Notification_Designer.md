# 08 — Notification Designer

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

Define how alerts and notifications (email, in-app, SMS/webhook) are configured for PrintOS events without hardcoding message templates or recipient logic.

## Concept

The Notification Designer configures what triggers a notification, who receives it, through which channel, and using which message template.

## Data Model (Illustrative)

| DocType | Purpose |
|---------|---------|
| PrintOS Notification Rule | Trigger event/condition, channel(s), recipient resolution |
| PrintOS Notification Template | Reusable message template with placeholders |

## Recipient Resolution

- Recipients may be resolved by role, by a document field (e.g. document owner, assigned user), or by an Approval Designer chain's current approver.
- Recipient resolution logic lives in the Application layer; the rule configuration only references which resolution strategy to use.

## Channels

- In-app (Frappe notifications), email, and — where integrated — SMS/webhook via [11_Integration_Designer.md](11_Integration_Designer.md).
- Channel availability may be gated by [12_Feature_Flags.md](12_Feature_Flags.md) per installation (e.g. SMS only for installations with that add-on enabled).

## Rules

- Templates never embed unescaped user input directly into HTML email bodies (XSS prevention); templating must escape by default.
- Notification volume/frequency should be configurable (digest vs. immediate) to avoid notification fatigue — this is a UX requirement, not just technical.

## Related Documents

- [07_Automation_Rules.md](07_Automation_Rules.md)
- [11_Integration_Designer.md](11_Integration_Designer.md)
- [12_Feature_Flags.md](12_Feature_Flags.md)

---

# Future Considerations

- WhatsApp Channel (Naming Registry Section 9) should be addable as a Notification Designer channel without redesign, consistent with Integration Designer's adapter model.

---

# Open Questions

- Should notification digest/frequency preferences be a per-user setting, a per-role default, or both?

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
