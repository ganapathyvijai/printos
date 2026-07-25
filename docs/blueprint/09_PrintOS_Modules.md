# PrintOS Modules

Version:
1.0

Status:
Draft

Owner:
PrintHub Architecture Team

Last Updated:
2026-07-18

---

# Purpose

This document describes every business module of PrintOS — its purpose, responsibilities, features, and dependencies — providing a functional catalog that bridges the bounded contexts in [06_Bounded_Contexts.md](06_Bounded_Contexts.md) to concrete business capability.

---

# Scope

This document covers business-level module description: purpose, responsibilities, features, inputs/outputs, dependencies, permissions, reports, dashboards, and future enhancements.

This document does not cover implementation, DocType design, or UI design. Each module here generally corresponds to one bounded context from [06_Bounded_Contexts.md](06_Bounded_Contexts.md), described here at the module/feature level rather than the domain-modeling level.

---

# Background

Bounded contexts define conceptual boundaries; modules translate those boundaries into recognizable business capability that users (print shop staff, in Phase 1) interact with. This module catalog is the primary reference for what PrintOS Phase 1 must deliver.

---

# Main Content

## Module Summary

| Module | Bounded Context | Phase |
|---|---|---|
| CRM | CRM | 1 |
| Sales | Sales | 1 |
| Estimation | Estimation | 1 |
| Artwork | Artwork | 1 |
| Production Planning | Production | 1 |
| Job Cards | Production | 1 |
| Machine Scheduling | Production | 1 |
| Inventory | Inventory | 1 |
| Purchasing | Procurement | 1 |
| Warehouse | Warehouse | 1 |
| Dispatch | Dispatch | 1 |
| Accounts | Accounts | 1 |
| GST | GST | 1 |
| HR | HR | 1 |
| Reports | Reporting | 1 |
| Analytics | Reporting | 1 (basic) / Future (advanced) |
| MachineIQ | MachineIQ | Future |
| Administration | Administration | 1 |
| Marketplace | Marketplace | Future (Phase 5) |

## Module Details

### CRM

- **Purpose:** Capture and manage prospective business relationships.
- **Business Responsibilities:** Lead intake, enquiry tracking, follow-up scheduling.
- **Key Features:** Lead capture, enquiry status tracking, communication log.
- **Inputs:** Prospect contact details, referral/marketing sources.
- **Outputs:** Qualified leads to Sales.
- **Dependencies:** Customer master data.
- **Permissions:** Sales staff (create/edit), Sales management (view all).
- **Reports:** Lead conversion rate, source effectiveness.
- **Dashboards:** Open leads by stage.
- **Future Enhancements:** Marketplace-originated lead ingestion.

### Sales

- **Purpose:** Manage confirmed customer orders.
- **Business Responsibilities:** Sales Order creation, order status tracking.
- **Key Features:** Sales Order entry, order-to-production handoff, customer order history.
- **Inputs:** Approved Quotations, direct reorders.
- **Outputs:** Confirmed Sales Orders to Production.
- **Dependencies:** Quotation, Customer, Price List.
- **Permissions:** Sales staff (create), Sales management (approve/amend).
- **Reports:** Sales by customer, sales by product category.
- **Dashboards:** Open orders, order fulfillment status.
- **Future Enhancements:** Freelancer-brokered and supplier-linked orders.

### Estimation

- **Purpose:** Provide accurate, industry-specific pricing for requested work.
- **Business Responsibilities:** Cost estimation, price proposal generation and revision.
- **Key Features:** Substrate/finishing/machine-time-based costing, multi-version quoting, approval tracking.
- **Inputs:** Enquiry scope, master data (materials, machine rates, finishing costs).
- **Outputs:** Approved Quotation to Sales.
- **Dependencies:** Master Data Model (Product Template, Material, Machine Profile).
- **Permissions:** Estimators (create), Sales management (approve).
- **Reports:** Quotation win/loss rate, average margin.
- **Dashboards:** Pending quotations.
- **Future Enhancements:** MachineIQ-assisted automated estimation.

### Artwork

- **Purpose:** Manage design assets and customer approval prior to production.
- **Business Responsibilities:** Artwork intake, proofing, revision tracking, approval capture.
- **Key Features:** File intake, proof generation, approval workflow, revision history.
- **Inputs:** Sales Order, customer-submitted design files.
- **Outputs:** Approved Artwork to Production.
- **Dependencies:** Sales Order.
- **Permissions:** Design staff (create/revise), Customer-facing staff (submit for approval).
- **Reports:** Approval turnaround time, revision count per job.
- **Dashboards:** Pending approvals.
- **Future Enhancements:** Freelancer-sourced design submissions.

### Production Planning

- **Purpose:** Plan and sequence production work across available capacity.
- **Business Responsibilities:** Capacity planning, job prioritization, schedule coordination.
- **Key Features:** Production calendar, capacity view, job prioritization.
- **Inputs:** Confirmed Sales Orders, Approved Artwork, Machine availability.
- **Outputs:** Scheduled Job Cards.
- **Dependencies:** Job Cards, Machine Scheduling.
- **Permissions:** Production management (plan/schedule).
- **Reports:** Capacity utilization, schedule adherence.
- **Dashboards:** Production calendar view.
- **Future Enhancements:** MachineIQ-driven optimal scheduling.

### Job Cards

- **Purpose:** Track execution of individual units of production work.
- **Business Responsibilities:** Work instruction issuance, progress tracking, quality checkpoints.
- **Key Features:** Job Card creation, status tracking, material allocation link, quality checkpoint recording.
- **Inputs:** Approved Artwork, Production Plan.
- **Outputs:** Finished goods status to Warehouse/Dispatch.
- **Dependencies:** Machine Scheduling, Inventory (material allocation).
- **Permissions:** Production operators (update status), Production management (create/close).
- **Reports:** Job throughput time, rework rate.
- **Dashboards:** Job Cards by status.
- **Future Enhancements:** Real-time shop-floor tracking devices/sensors.

### Machine Scheduling

- **Purpose:** Assign production work to available machines within their capability constraints.
- **Business Responsibilities:** Machine allocation, conflict avoidance, downtime accounting.
- **Key Features:** Machine calendar, capability matching (via Machine Profile), conflict detection.
- **Inputs:** Job Cards, Machine Profiles, Machine availability/downtime.
- **Outputs:** Machine assignments to Job Cards.
- **Dependencies:** Machine Profile master data, Job Cards.
- **Permissions:** Production management (assign/reassign).
- **Reports:** Machine utilization, downtime analysis.
- **Dashboards:** Machine schedule board.
- **Future Enhancements:** MachineIQ-driven predictive maintenance and scheduling optimization.

### Inventory

- **Purpose:** Track availability and consumption of production materials.
- **Business Responsibilities:** Stock level visibility, allocation to Job Cards, replenishment signaling.
- **Key Features:** Stock level tracking, allocation records, low-stock alerts.
- **Inputs:** Procurement receipts, Production consumption.
- **Outputs:** Material availability to Production; replenishment triggers to Purchasing.
- **Dependencies:** Material/Substrate master data, Warehouse.
- **Permissions:** Inventory staff (record movements), Production (request allocation).
- **Reports:** Stock valuation, consumption trends.
- **Dashboards:** Low-stock alerts.
- **Future Enhancements:** Supplier-integrated real-time visibility.

### Purchasing

- **Purpose:** Acquire materials and services required for production.
- **Business Responsibilities:** Purchase Order issuance, supplier communication, receipt confirmation.
- **Key Features:** Purchase Order creation, supplier comparison, receipt tracking.
- **Inputs:** Replenishment triggers from Inventory.
- **Outputs:** Received materials to Warehouse.
- **Dependencies:** Supplier master data, Inventory.
- **Permissions:** Procurement staff (create/receive), management (approve).
- **Reports:** Supplier performance, purchase spend by category.
- **Dashboards:** Open purchase orders.
- **Future Enhancements:** Supplier Portal self-service ordering.

### Warehouse

- **Purpose:** Manage physical storage and movement of goods.
- **Business Responsibilities:** Goods receipt, goods issue, stock location tracking.
- **Key Features:** Location management, stock movement recording.
- **Inputs:** Procurement receipts, Production output.
- **Outputs:** Materials to Production; finished goods to Dispatch.
- **Dependencies:** Warehouse master data, Inventory.
- **Permissions:** Warehouse staff (record movements).
- **Reports:** Stock movement history.
- **Dashboards:** Warehouse occupancy.
- **Future Enhancements:** Multi-warehouse/multi-branch transfer workflows.

### Dispatch

- **Purpose:** Deliver finished goods to customers.
- **Business Responsibilities:** Delivery scheduling, dispatch documentation, delivery confirmation.
- **Key Features:** Dispatch scheduling, delivery method selection, confirmation capture.
- **Inputs:** Finished goods from Production/Warehouse.
- **Outputs:** Delivery confirmation to Accounts.
- **Dependencies:** Delivery Method master data, Sales Order.
- **Permissions:** Dispatch staff (schedule/confirm).
- **Reports:** On-time delivery rate.
- **Dashboards:** Pending dispatches.
- **Future Enhancements:** Marketplace-coordinated delivery.

### Accounts

- **Purpose:** Maintain the financial record of business transactions.
- **Business Responsibilities:** Invoicing, payment tracking, financial statements basis.
- **Key Features:** Invoice generation, payment recording, financial reporting (via ERPNext core).
- **Inputs:** Dispatch confirmation, Purchase Orders.
- **Outputs:** Financial records to Reporting and GST.
- **Dependencies:** ERPNext core accounting capability.
- **Permissions:** Accounts staff (create/record), management (approve/view).
- **Reports:** Receivables aging, payables aging.
- **Dashboards:** Cash position summary.
- **Future Enhancements:** Multi-currency support.

### GST

- **Purpose:** Ensure statutory tax compliance.
- **Business Responsibilities:** Tax computation on transactions, statutory record-keeping.
- **Key Features:** Tax template application, GST-compliant invoice formatting (via ERPNext core).
- **Inputs:** Invoice and Purchase data.
- **Outputs:** Statutory tax records.
- **Dependencies:** ERPNext core tax capability, GST Configuration master data.
- **Permissions:** Accounts staff, statutory compliance owner.
- **Reports:** GST filing summary.
- **Dashboards:** Filing due dates.
- **Future Enhancements:** Multi-jurisdiction tax support.

### HR

- **Purpose:** Manage employee records relevant to operations.
- **Business Responsibilities:** Employee record maintenance, department/role structure.
- **Key Features:** Employee records, department hierarchy (via ERPNext core).
- **Inputs:** Organizational decisions.
- **Outputs:** Employee/role data to Production (operator assignment).
- **Dependencies:** ERPNext core HR capability.
- **Permissions:** HR staff (manage records), management (view).
- **Reports:** Headcount by department.
- **Dashboards:** N/A (Phase 1 basic usage).
- **Future Enhancements:** Service Engineer role and scheduling management.

### Reports

- **Purpose:** Provide standard business reporting across modules.
- **Business Responsibilities:** Standard report generation for operational and management use.
- **Key Features:** Cross-module standard reports.
- **Inputs:** Data from all transactional modules.
- **Outputs:** Reports to business stakeholders.
- **Dependencies:** All transactional modules.
- **Permissions:** Role-based report access.
- **Reports:** (This module is itself the report catalog.)
- **Dashboards:** Standard dashboard set.
- **Future Enhancements:** Custom report builder.

### Analytics

- **Purpose:** Provide deeper, trend-based insight beyond standard reports.
- **Business Responsibilities:** Trend analysis, performance insight.
- **Key Features:** Basic analytics in Phase 1; advanced analytics future via MachineIQ.
- **Inputs:** Aggregated Reporting data.
- **Outputs:** Insights to management.
- **Dependencies:** Reports module.
- **Permissions:** Management-level access.
- **Reports:** Trend reports.
- **Dashboards:** Executive dashboard.
- **Future Enhancements:** Predictive analytics via MachineIQ.

### MachineIQ (Future)

- **Purpose:** Apply machine intelligence to production and business data.
- **Business Responsibilities:** Pattern recognition, predictive recommendations (future scope).
- **Key Features:** To be defined in a future Blueprint document.
- **Inputs:** Production and Reporting data.
- **Outputs:** Recommendations/insights.
- **Dependencies:** Reports, Production Planning, Machine Scheduling.
- **Permissions:** To be defined.
- **Reports/Dashboards:** To be defined.
- **Future Enhancements:** Core differentiator; full scope pending future phase planning.

### Administration

- **Purpose:** Configure and govern the PrintOS instance.
- **Business Responsibilities:** Company/Branch setup, user and role administration, system configuration.
- **Key Features:** Company/Branch management, user/role management, system settings (via ERPNext core + `printos_core` extensions).
- **Inputs:** Business setup decisions.
- **Outputs:** Configuration consumed by all modules.
- **Dependencies:** ERPNext core administration capability.
- **Permissions:** System administrators only.
- **Reports:** User access audit.
- **Dashboards:** System health (future).
- **Future Enhancements:** Multi-tenant SaaS administration console.

### Marketplace (Future)

- **Purpose:** Connect public buyers (G1) directly with print shops.
- **Business Responsibilities:** Buyer-facing catalog and ordering (future scope).
- **Key Features:** To be defined in a future Blueprint document, at Phase 5 planning.
- **Inputs:** Print shop capacity/catalog (future).
- **Outputs:** Orders into CRM/Sales.
- **Dependencies:** CRM, Sales, Dispatch.
- **Permissions:** To be defined.
- **Reports/Dashboards:** To be defined.
- **Future Enhancements:** Full scope defined at Phase 5.

---

# Architecture Notes

Each module is intended to be implemented within `printos_core` as a cohesive unit aligned to its bounded context, minimizing cross-module coupling per [04_System_Architecture.md](04_System_Architecture.md). Modules built entirely on ERPNext's generic capability (Accounts, GST, HR, Administration) should use Frappe's extension mechanisms rather than reimplementing functionality already provided by the framework.

---

# Future Considerations

MachineIQ and Marketplace modules are intentionally left at a summary level; each will require a dedicated Blueprint document once its phase is formally scoped (see [03_Product_Roadmap.md](03_Product_Roadmap.md)).

---

# Open Questions

- Should Analytics remain a separate module from Reports, or merge once MachineIQ matures?
- What is the minimum viable permission model for Phase 1 versus a full role-based access control design?

---

# Related Documents

- [00_Master_Index.md](00_Master_Index.md)
- [06_Bounded_Contexts.md](06_Bounded_Contexts.md)
- [08_Master_Data_Model.md](08_Master_Data_Model.md)
- [10_Business_Workflows.md](10_Business_Workflows.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-18|Initial|Initial Version|
|1.1|2026-07-22|ADR Synchronization|Renamed the "Quotation" module to "Estimation" (table row and section header) to match its Bounded Context name, per [ADR-012-Estimating-Terminology.md](../decisions/ADR-012-Estimating-Terminology.md). "Quotation" is retained throughout this module's description as the artifact it produces, per [ADR-013-Quotation-Terminology.md](../decisions/ADR-013-Quotation-Terminology.md). No other module, architecture, or content change made.|

---

# Documentation Quality Checklist

- [ ] Technically accurate
- [ ] Business terminology verified
- [ ] Cross-references updated
- [ ] Mermaid diagrams validated
- [ ] No implementation code included
- [ ] Future roadmap considered
- [ ] Reviewed by Project Owner
