# Naming Standards

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

Establish consistent naming conventions across all PrintOS code and artifacts so that any contributor can predict how something is named without needing to ask.

---

# Scope

Covers naming for Python code, JavaScript code, DocTypes, folders, database tables, and API endpoints inside `printos_core`. Does not cover ERPNext core naming, which is inherited as-is.

---

# Standards

| Item | Convention | Example |
|---|---|---|
| Python variables/functions | snake_case | `calculate_job_cost()` |
| Python modules/files | snake_case | `job_card_service.py` |
| Python classes | PascalCase | `class JobCardService` |
| DocTypes | PascalCase | `Job Card`, `Machine Profile` |
| JavaScript variables/functions | camelCase | `calculateJobCost()` |
| JavaScript classes/components | PascalCase | `JobCardForm` |
| Folders | snake_case | `job_card/`, `machine_scheduling/` |
| Database tables (Frappe-managed) | Follows Frappe's `tab<DocType>` convention | `tabJob Card` |
| API endpoints | REST conventions, kebab or resource-based paths | `/api/method/printos_core.job_card.get_status` |
| Constants | UPPER_SNAKE_CASE | `MAX_JOB_PRIORITY` |
| Environment variables | UPPER_SNAKE_CASE | `PRINTOS_DB_HOST` |

---

# Rationale

Consistent naming reduces cognitive load and prevents subtle bugs caused by inconsistent casing across a polyglot stack (Python backend, JavaScript frontend, ERPNext DocTypes). These conventions mirror the ecosystem defaults for each language/framework rather than inventing new ones, minimizing surprise for contributors familiar with Python, JavaScript, or Frappe.

---

# Related Documents

- [Coding_Standards.md](Coding_Standards.md)
- [DocType_Standards.md](DocType_Standards.md)
- [Database_Standards.md](Database_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
