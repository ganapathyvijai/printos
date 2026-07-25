# 15 — Deployment Model

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

Define how configuration (designers' output, feature flags, templates, tenant overrides) moves safely across environments — development, staging, production — without ad hoc manual edits in production.

## Environments

```
Development  ──▶  Staging  ──▶  Production
```

Configuration changes are authored and tested in Development, validated in Staging, and promoted to Production only after review — mirroring the project's **Plan → Verify → Execute → Review** workflow.

## Promotion Mechanism

- Configuration DocTypes are exported as Frappe **fixtures** and version-controlled alongside `printos_core`, so configuration changes are reviewable as diffs, not invisible database edits.
- Environment-specific values (credentials, endpoints) are never baked into fixtures — they are supplied via environment variables or per-environment credential records (see [11_Integration_Designer.md](11_Integration_Designer.md)).

## Rules

- No configuration change is made by editing production data directly when it should be introduced through the Development → Staging → Production path; direct production edits are limited to genuine emergency fixes, and are backfilled into fixtures/documentation immediately after.
- Every promoted configuration change affecting workflows, approvals, or permissions must be verified in Staging with representative data before reaching Production.
- Rollback plan: because configuration is versioned fixture data, reverting a bad configuration change means reapplying the previous fixture version, not manual undo.

## Related Documents

- [01_Configuration_Architecture.md](01_Configuration_Architecture.md)
- [13_Tenant_Customization.md](13_Tenant_Customization.md)
- [14_Template_Library.md](14_Template_Library.md)
- [../technical/10_Implementation_Guidelines.md](../technical/10_Implementation_Guidelines.md)
- `docs/standards/Branching_Strategy.md`
- `docs/standards/Release_Process.md`

---

# Future Considerations

- This deployment model must extend to tenant provisioning, tenant backup, and configuration export/import once `docs/blueprint/25_MultiTenant_Architecture.md` formally defines multi-tenant operation — the fixture-based promotion mechanism described here is intended to remain the underlying mechanism for that future capability, not be replaced by it.

---

# Open Questions

- Should configuration version compatibility (which configuration fixture versions are valid against which PrintOS/ERPNext release) be tracked centrally, following `docs/standards/Versioning.md`, or per configuration category?

---

# Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-07-23 | Initial | Initial Version |
| 1.1 | 2026-07-23 | Configuration Studio Review | Added header metadata, cross-references to `Branching_Strategy.md`/`Release_Process.md`, Future Considerations, Open Questions, Revision History/Quality Checklist. No architectural content changed. |

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
