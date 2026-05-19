# Documentation Repair Notes — Module 07

## Status: **REPAIRED**

This folder previously contained **invalid duplicated content** identified in the enterprise architecture audit (AUD-002).

---

## Invalid content (removed / replaced)

| File | Previous (invalid) content | Correct ownership |
|------|---------------------------|-------------------|
| `overview.md` | User Management overview (`07-user-management`) | `03-user-management` |
| `events.md` | User Management events | `03-user-management` |
| `aggregates.md` | Authentication Management aggregates | `01-identity-access-management` (IAM) |
| `entities.md` | Authentication Management entities | IAM |
| `value-objects.md` | Authentication Management value objects | IAM |
| `workflows.md` | Authentication Management workflows | IAM |
| `repositories.md` | Authentication Management repositories | IAM |
| `api-contracts.md` | Authentication Management APIs | IAM |
| `security-rules.md` | Authentication Management security | IAM |
| `testing-strategy.md` | Authentication Management testing | IAM |

The duplicate Authentication Management copies matched `05-authentication-management` (also **DEPRECATED**).

---

## Current state

All blueprint files in this folder now describe **Notification Management** only.

Do not restore User Management or Authentication content into module `07`.

---

## Related documents

- `AUTHENTICATION-CANONICALIZATION.md`
- `module-blueprints/05-authentication-management/DEPRECATED.md`
- `ENTERPRISE-ARCHITECTURE-AUDIT.md`
