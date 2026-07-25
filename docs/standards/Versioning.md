# Versioning

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

Define how version numbers are assigned to PrintOS releases, APIs, and Blueprint/Standards documents.

---

# Scope

Covers version numbering conventions across code releases, API contracts, and documentation. Does not cover the release approval process itself (see [Release_Process.md](Release_Process.md)).

---

# Standards

- PrintOS software releases follow **Semantic Versioning** (`MAJOR.MINOR.PATCH`):
  - MAJOR — breaking changes to business behavior or API contracts.
  - MINOR — backward-compatible feature additions.
  - PATCH — backward-compatible bug fixes.
- API versioning follows [API_Standards.md](API_Standards.md); breaking API changes require a MAJOR increment and are never applied silently to an existing version.
- Blueprint and Standards documents use a simple `MAJOR.MINOR` version in their header, incremented whenever content materially changes; the Revision History table records what changed.
- Version numbers are never reused or decremented.

---

# Rationale

A single, predictable versioning scheme across code, APIs, and documentation lets any contributor reason about compatibility and change impact without needing separate rules per artifact type.

---

# Related Documents

- [API_Standards.md](API_Standards.md)
- [Release_Process.md](Release_Process.md)
- [Documentation_Standards.md](Documentation_Standards.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
