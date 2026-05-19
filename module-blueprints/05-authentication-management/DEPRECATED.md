# DEPRECATED — Authentication Management (Module 05)

## Status: **DEPRECATED**

**Effective:** Architectural canonicalization (post enterprise audit)

**Replacement:** [`01-identity-access-management`](../01-identity-access-management/) — **Identity & Access Management (IAM)**

**Decision record:** [`AUTHENTICATION-CANONICALIZATION.md`](../../AUTHENTICATION-CANONICALIZATION.md)

---

## Why this module is deprecated

`05-authentication-management` duplicated the IAM bounded context. Maintaining two authentication models would cause:

- duplicate persistence and APIs
- inconsistent JWT and session behavior
- fragmented security auditing
- invalid AI-assisted implementations

---

## What to do

| Audience | Action |
|----------|--------|
| Implementers | Use **IAM (01)** only. Do not create new services under `authentication-management`. |
| Reviewers | Reject PRs that introduce a second authentication bounded context. |
| Documentation readers | Treat all files in this folder as **historical reference**. Follow IAM blueprints and `AUTHENTICATION-CANONICALIZATION.md` for the migration map. |

---

## Files in this folder

All blueprint files (`overview.md`, `aggregates.md`, `entities.md`, etc.) are **deprecated** and retained intentionally (not deleted) to preserve audit history and support migration mapping.

**Do not copy** these files into other module folders.

---

## Invalid placement history

Some files from this module were incorrectly copied into `07-notification-management` during a documentation error. That folder has been repaired; see `07-notification-management/DOCUMENTATION-REPAIR-NOTES.md`.
