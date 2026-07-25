# UI/UX Standards

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-22

---

# Purpose

Define conventions for consistent, usable interfaces across PrintOS, whether delivered through ERPNext's default UI, custom Frappe UI extensions, or future Next.js-based frontends.

---

# Scope

Covers interface consistency principles for `printos_core` frontend work. Does not cover specific screen designs or visual branding assets.

---

# Standards

- Prefer extending ERPNext's existing UI patterns (list views, forms, workflows) over building bespoke UI, unless the business need clearly requires a custom experience.
- Custom UI components use camelCase naming for JavaScript identifiers, per [Naming_Standards.md](Naming_Standards.md).
- Forms and fields use terminology consistent with `docs/blueprint/05_Domain_Model.md` — the same term must mean the same thing in the UI as in the domain model.
- Error and validation messages shown to users follow [Error_Handling.md](Error_Handling.md) — clear and actionable, never raw technical detail.
- Future Next.js-based frontends (for portals in later phases) should share a common design language with the ERP UI to maintain a coherent PrintHub brand experience.
- Accessibility (keyboard navigation, sufficient contrast) is a baseline expectation, not an afterthought.

---

# Rationale

Consistency between ERPNext's native UI and any custom PrintOS interface reduces training overhead for print shop staff (G2) and avoids a fragmented user experience as more portals (Freelancer, Supplier, Marketplace) are introduced in later phases.

---

# Related Documents

- [Naming_Standards.md](Naming_Standards.md)
- [Error_Handling.md](Error_Handling.md)
- `docs/blueprint/09_PrintOS_Modules.md`

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
