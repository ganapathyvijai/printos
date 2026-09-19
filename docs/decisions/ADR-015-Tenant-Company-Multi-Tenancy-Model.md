# ADR-015: Tenant and Company Multi-Tenancy Model

Status:
Accepted

Version:
1.1

Date:
2026-07-28

Decision Owner:
Project Owner

---

## 1. Context

[ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md) (Accepted (strategy); Implementation Deferred) established "future multi-tenant SaaS" as a Phase 1 design constraint: architectural choices in `printos_core` must not preclude a future move to multi-tenant operation, even though the concrete topology was deliberately deferred to a reserved future document, `docs/blueprint/25_MultiTenant_Architecture.md` (not yet written at the time ADR-006 was accepted).

Separately, [AR-002](Architecture_Review_Register.md) ("Multi-Tenant Strategy Alignment") was opened because a specific, settled tenancy model (one ERPNext site, one database, one backend instance per tenant; shared app code/Docker image/CI-CD; no shared database) was being asserted in task instructions as though already ratified, while the formal architecture record showed it as strategy-only and implementation-deferred. AR-002 also recorded that "Tenant" itself remained a Pending ADR term against "Company" ([Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 11) — ERPNext's native, Approved scoping anchor.

The Project Owner has formally selected **AR-002 Option A** ("Ratify the one-site-per-tenant, one-database-per-tenant model... via completion of `docs/blueprint/25_MultiTenant_Architecture.md` and the Tenant/Company ADR"), on 2026-07-28.

**This ADR is a companion to ADR-006, not a replacement.** It completes the concrete-model decision ADR-006 deliberately deferred; it does not reopen, reverse, or restate ADR-006's own design-constraint decision.

---

## 2. Decision

- One **Tenant** equals one isolated Frappe site.
- One **Tenant** equals one isolated operational database.
- **Tenant and Company are distinct concepts** (Section 4 and Section 5).
- One Tenant may contain **one or more Companies**.
- **Company** remains the ERPNext business/accounting scope inside a Tenant.
- Unrelated print shops (Tenants) **do not share an operational database**.
- Application code, Docker-image strategy, and CI/CD **may be shared** across Tenants.
- Underlying infrastructure hosts **may be shared or dedicated** by service tier, without weakening site/database isolation.
- Upgrades are **centrally governed** by the PrintHub platform owner.
- **Permanent tenant-specific code forks and permanent tenant-specific version branches are prohibited.**

---

## 3. Tenant Definition

**Tenant** means:

- a print-shop organization;
- a PrintHub subscription unit;
- a Frappe site (the site boundary);
- an isolated operational database (the database boundary);
- the file, secrets, configuration, and backup boundary;
- the upgrade-rollout and operational-management unit.

---

## 4. Company Definition

**Company** means:

- an ERPNext legal and accounting entity that exists **inside** a Tenant;
- the business-scoping anchor for ERPNext transactional and accounting records;
- potentially **one of several** Companies inside a single Tenant;
- **not** a synonym for Tenant;
- **not** the security boundary between unrelated print shops — that boundary is the Tenant's site/database isolation, not Company scoping.

---

## 5. Isolation Invariants

The following must remain isolated per Tenant:

- operational **database**;
- **files** (public and private);
- **secrets and credentials**;
- **configuration** values;
- **backups**;
- **operational access** (support/administrative actions are scoped to a specific Tenant and are not implicitly cross-tenant).

**Sharing a compute host does not permit sharing a tenant operational database.** Host-level resource sharing (Section 9) is an infrastructure-efficiency concern; it must never be implemented in a way that merges or exposes two Tenants' operational data.

---

## 6. Job Card Tier A Consequence

- Job Card Tier A is **scoped by Company** within its Tenant site.
- **Site identity remains implicit** — it is established by which isolated site/database the record lives in, not by a stored field.
- **No Tenant field is included by default** on Job Card Tier A or, by the same reasoning, on other Tenant-site-scoped DocTypes.
- A Tenant field would require a **later Published design** demonstrating a specific, concrete technical need not met by implicit site scoping.
- **Global Customer identity and cross-tenant analytics (Section 11) are not dependencies of Job Card Tier A.**

This ADR does **not** define Job Card fields, status values, permissions, or hooks — those belong to a separate, later Tier A design package.

---

## 7. Upgrade Governance

- One centrally governed application/platform line (ERPNext, Frappe, and PrintHub application versions together).
- Rollout is **platform-owner-controlled**.
- Release rings and temporary rollout staggering **are permitted**.
- **Indefinite version pinning by a Tenant is prohibited.**
- Unsupported versions may be **retired centrally**.
- Rollback and recovery mechanics are **deferred to deployment design** (not decided by this ADR).

This is consistent with, and does not reopen, [ADR-001-ERPNext-Framework](ADR-001-ERPNext-Framework.md)'s already-Resolved (via AR-001) governed-version decision.

---

## 8. Service-Tier Principles

- **Every** service tier (Free, Premium, Enterprise) receives baseline: functional correctness, tenant isolation, security, data integrity, and recoverable automated backup protection.
- Paid tiers **may** receive increased: compute/worker capacity, database/file-storage allowance, backup frequency, retention, monitoring, restore priority, operational limits, and support priority.
- **No plan-specific application fork is permitted** — tier differences must remain configuration-driven.
- **Exact quotas, pricing, RPO, RTO, and SLA commitments remain deferred** — this ADR states principles, not contractual figures.

---

## 9. Platform-Owner Access

- Access to tenant systems/data by the platform owner must be **least-privileged**.
- Access must be **purpose-limited** (tied to a specific support, operational, or security need).
- Access must be **audited**.
- Direct, unrestricted routine database access is **exceptional**, not the default operating or analytics model.
- Implementation mechanisms (access-control tooling, audit logging systems) are **deferred to security and operational design**.

---

## 10. Explicit Exclusions

This ADR explicitly does **not** authorize:

- a global Customer directory;
- automatic cross-tenant Customer matching;
- a centralized transaction store;
- cross-tenant analytics of any kind;
- a data warehouse or data lake;
- cross-shop visibility for any Tenant;
- Tenant Override implementation;
- exact provisioning-automation design;
- exact infrastructure sizing;
- exact backup and SLA values.

**Future central-identity or cross-tenant-analytics work requires a separately approved privacy, security, and data-governance architecture decision.** This ADR does not create, name, or reserve a number for that future decision — no new Architecture Review identifier is established here.

---

## 11. Consequences

**Positive:**

- Strong tenant isolation (site + database boundary, not merely a scoping field).
- Clean, per-Tenant backup/restore boundaries.
- Controlled blast radius — a single Tenant's failure or data issue does not propagate to others.
- Tenant-independent capacity scaling (adding Tenants does not grow one shared database).
- Shared-code maintainability — one governed `printos_core` codebase serves all Tenants.
- A clear, unambiguous Job Card Tier A Company-scoping rule.

**Negative / cost:**

- Greater operational tooling burden — many sites and databases to provision, monitor, upgrade, and restore, rather than one shared instance.
- Automation becomes essential to this model's viability at scale.
- Platform-wide analytics cannot rely on a single shared transactional database and must be designed separately, later, under its own governance (Section 10).

---

## 12. Alternatives Considered

Only the alternatives officially recorded against AR-002 are considered here; no additional option is introduced.

- **Option B — shared instance / multi-Company model** (single ERPNext instance, multiple Companies representing different tenants). Rejected: this would make Company-level scoping the *primary* cross-customer security boundary, which directly contradicts the Owner's requirement that Company is "not the sole security boundary between unrelated print shops." It also would not deliver the Owner's required per-tenant backup/restore boundary or independent database-scaling path without combining tenant operational data in one database.
- **Option C — defer ratification, remain explicitly single-tenant.** Rejected: this does not establish the topology, Tenant/Company relationship, or growth path the Owner has already specified in detail; it would leave Naming Registry §27 item 11 and `25_MultiTenant_Architecture.md` unresolved indefinitely without addressing the Owner's stated preference.

---

## 13. Relationship to ADR-006

- [ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md) **remains Accepted**, unchanged, and unamended by this ADR.
- ADR-015 **completes** the concrete-model decision ADR-006 deliberately deferred to `docs/blueprint/25_MultiTenant_Architecture.md`.
- ADR-015 **does not supersede or reverse** ADR-006 — it fulfills ADR-006's own stated Consequence that "full multi-tenant mechanics... remain an open design area until `docs/blueprint/25_MultiTenant_Architecture.md` is completed."
- A factual cross-reference **may be added to ADR-006's Revision History** once this ADR is Accepted, as a separate, later, non-substantive documentation task. This ADR does not perform that update.

---

## 14. Required Follow-Up Documents

- `docs/blueprint/25_MultiTenant_Architecture.md` — the architecture-design elaboration of this decision, Approval, not Published.
- Naming Registry synchronization — Section 27, item 11 ("Tenant" vs. "Company") is Resolved, citing this Accepted ADR.
- Deployment and operational architecture — provisioning, monitoring, backup tooling, and release-ring mechanics.
- Published Job Card Tier A specifications — DocType, permission, and validation design, informed by Section 6 above.
- A separate scoped implementation-authorization decision, preceded by Published implementation specifications.

---

## 15. Implementation Authorization

**Implementation Authorization:** Not Granted

Acceptance of this ADR does **not** itself authorize coding, schema changes, site provisioning, Docker or CI/CD changes, migrations, or any other implementation activity. A separate, later, scoped implementation-authorization decision — preceded by Published implementation specifications — remains required before any coding may begin. Every roadmap workstream remains Not Authorized by this ADR's Accepted disposition.

---

## 16. References

- [AR-002 — Multi-Tenant Strategy Alignment](Architecture_Review_Register.md)
- [ADR-006-MultiTenant-Strategy](ADR-006-MultiTenant-Strategy.md)
- [ADR-001-ERPNext-Framework](ADR-001-ERPNext-Framework.md) — central version-governance precedent (AR-001, Resolved)
- [../standards/Naming_Registry.md](../standards/Naming_Registry.md) Section 27, item 11
- [../architecture/04_MultiTenant_Architecture.md](../architecture/04_MultiTenant_Architecture.md) (working draft, to be reconciled with the document below)
- `docs/blueprint/25_MultiTenant_Architecture.md` (companion document, Approval, not Published)
- [../implementation/Architecture_Freeze.md](../implementation/Architecture_Freeze.md) — Approval
- [../roadmap/01_Development_Roadmap.md](../roadmap/01_Development_Roadmap.md) — Approval
- [../implementation/Module_Dependency_Matrix.md](../implementation/Module_Dependency_Matrix.md) — Draft

None of the above documents is Published; none is described as Published by this ADR.

---

## 17. Review Record

**Architecture Review:** Completed
**Disposition:** Accepted with non-blocking observations
**Review date:** 2026-07-28
**Critical findings:** 0
**High findings:** 0
**Mandatory corrections before approval:** 0

**Business Review:** Completed
**Disposition:** Accepted with non-blocking observations
**Review date:** 2026-07-28
**Blocking business issues:** 0
**Mandatory corrections before approval:** 0

**Non-blocking observations acknowledged (not applied in this promotion):**

- A dedicated Security Review remains required before production readiness. It is not a prerequisite for this Accepted lifecycle promotion, and no Security Review is claimed to have occurred.
- Minor wording harmonization between "application/platform line" (this ADR, Section 7) and "code line" (the companion Multi-Tenant Architecture document) may occur through a later, separate, non-substantive editorial update. Not applied here.
- This ADR's length is acknowledged as acceptable under current repository precedent (see ADR-014). No structural change made.

None of these observations changes the topology, Tenant/Company definitions, cardinality, isolation invariants, shared-host rule, shared-code rule, upgrade governance, service-tier principles, Job Card Tier A Company-scoping, implicit site identity, no-Tenant-field default, central-customer-identity deferral, cross-tenant-analytics deferral, or implementation-authorization boundary recorded above.

**Project Owner Approval:** Completed
**Decision:** Approved lifecycle promotion to Accepted, Version 1.0
**Approval date:** 2026-07-28

**Approval scope and boundaries:**

- This ADR is approved as the binding companion decision to ADR-006, completing its deferred concrete-tenancy-model gap.
- The Decision content and all architecture conclusions recorded in Sections 2–14 above are unchanged by this promotion.
- **ADR-006 was not amended** — it remains Accepted, unchanged, as recorded in Section 13.
- **AR-002 remains Open** in the Architecture Review Register pending completion of the remaining repository-recording steps (Naming Registry synchronization, ADR Index registration, and formal AR-002 Register closure) — none of which is performed by this promotion.
- **AR-002 is not stated or implied to be Resolved by this document.**
- The Project Owner has separately stated an intention to provide product modifications and suggestions after reviewing the first authorized working ERP slice. This is a **future feedback intent only** and does **not** authorize implementation now; it must not be read as approval to begin coding.

**Subsequent current-state synchronization (2026-09-19):** The AR-002 statements above are preserved as part of the dated 2026-07-28 Project Owner Approval record. After that approval, the remaining repository-recording steps were completed and AR-002 was formally recorded as Resolved in the Architecture Review Register through Project Owner selection of Option A, citing this Accepted ADR as the resolving decision. Naming Registry Section 27 item 11 is Resolved, and Blueprint 25 is at Approval, not Published. This subsequent disposition grants no implementation authorization and changes none of this ADR's architecture decisions.

**Implementation Authorization:** Not Granted. Acceptance of this ADR does not authorize Job Card Tier A coding, DocType creation, schema changes, migrations, hooks, fixtures, site provisioning, tenant creation, Docker changes, CI/CD changes, Deployment Strategy changes, Tenant Override implementation, plugin implementation, central Customer identity design or implementation, or cross-tenant analytics design or implementation. A separate, later, scoped implementation-authorization decision — preceded by Published implementation specifications — remains required.

---

## Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
| 0.1 | 2026-07-28 | Initial Draft | Initial Draft creation following Project Owner selection of AR-002 Option A (2026-07-28). Records the Tenant/Company definitions, isolated site/database-per-Tenant topology, isolation invariants, Job Card Tier A consequence, upgrade-governance and service-tier principles, platform-owner access principle, explicit central-identity and cross-tenant-analytics exclusions, and the implementation-authorization boundary. Companion to ADR-006 (not a supersession or amendment). AR-002 remains Open pending this ADR's and the companion Multi-Tenant Architecture document's review and approval. No implementation authorized. |
| 1.0 | 2026-07-28 | Project Owner Lifecycle Approval | Architecture Review completed — Accepted with non-blocking observations (0 Critical, 0 High findings, 0 mandatory corrections). Business Review completed — Accepted with non-blocking observations (0 blocking business issues, 0 mandatory corrections). Project Owner approved lifecycle promotion from Draft 0.1 to Accepted 1.0. The topology, Tenant/Company decision, and all Section 2–14 content are unchanged. Non-blocking observations (future Security Review before production; optional wording harmonization; ADR length acceptable under precedent) recorded in Section 17 without being applied. ADR-006 was not amended and remains Accepted, unchanged. AR-002 remains Open pending remaining repository-recording steps (Naming Registry synchronization, ADR Index registration, Register closure) and is not stated or implied to be Resolved. The Project Owner's stated intent to provide product feedback after reviewing the first authorized working ERP slice is recorded as future intent only and does not authorize implementation. Implementation Authorization remains Not Granted. |
| 1.1 | 2026-09-19 | Cross-Reference and Disposition Synchronization (MT-R4) | Synchronized stale active references in Sections 14, 15, and 16: Blueprint 25 is Approval, not Published; Naming Registry Section 27 item 11 is Resolved; the "Draft status, and any later Acceptance" implementation-authorization wording was corrected to reflect this ADR's Accepted disposition; and the Architecture_Freeze.md, 01_Development_Roadmap.md, and Module_Dependency_Matrix.md references were converted to versionless ordinary narrative. Added a clearly labeled Subsequent current-state synchronization note to Section 17, recording that AR-002 was subsequently formally recorded as Resolved in the Architecture Review Register, citing this Accepted ADR as the resolving decision. The dated 2026-07-28 Project Owner Approval record, both historical AR-002 statements within it, and Revision History rows 0.1 and 1.0 are preserved verbatim. Sections 2–13 are unchanged. Status remains Accepted; Implementation Authorization remains Not Granted. |

---

## Quality Checklist

- [ ] Context clearly explains the relationship to ADR-006 and AR-002
- [ ] Decision is unambiguous
- [ ] Tenant and Company definitions are precise and mutually exclusive
- [ ] Alternatives considered match the official AR-002 options
- [ ] Consequences (positive and negative) are stated
- [ ] Explicit exclusions (central identity, cross-tenant analytics) are stated without creating a new AR identifier
- [ ] Implementation Authorization boundary is explicit
- [ ] References are complete and no Draft document is described as Published
- [ ] Reviewed by Project Owner
