# Git Workflow

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

Define how git is used day-to-day across the PrintHub repository so history stays clean, reviewable, and safe.

---

# Scope

Covers commit practices, pull request expectations, and general git hygiene. Branch naming and lifecycle are covered in [Branching_Strategy.md](Branching_Strategy.md); release timing is covered in [Release_Process.md](Release_Process.md).

---

# Standards

- Commits are never made automatically by an AI assistant or automation tool without explicit human approval, per `CLAUDE.md`.
- Commits are never pushed automatically; pushing requires explicit approval.
- Destructive operations (force-push, `reset --hard`, `clean -f`, branch deletion) always require explicit confirmation before execution.
- Every Pull Request includes: Purpose, Files Changed, Risk Assessment, Testing Performed, Documentation Updated — per `PROJECT_RULES.md` Rule 21.
- Commit messages describe *why* a change was made, not just *what* changed.
- Hooks (pre-commit, pre-push) are never bypassed (`--no-verify`) without explicit user instruction.

---

# Rationale

Git history is shared, hard-to-reverse state. Requiring explicit approval before commit/push and never skipping hooks protects against irreversible mistakes and keeps the audit trail (who changed what, and why) intact.

---

# Related Documents

- [Branching_Strategy.md](Branching_Strategy.md)
- [Release_Process.md](Release_Process.md)
- [Versioning.md](Versioning.md)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------|--------|---------|
|1.0|2026-07-22|Initial|Initial Version|
