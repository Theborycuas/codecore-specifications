# codecore-specifications

Official enterprise architecture and module blueprint specifications for **CodeCore**.

---

## Start here

| Document | Purpose |
|----------|---------|
| [MODULE-CATALOG.md](./MODULE-CATALOG.md) | Index of all modules, status, events, dependencies |
| [AUTHENTICATION-CANONICALIZATION.md](./AUTHENTICATION-CANONICALIZATION.md) | IAM as sole authentication BC (module 05 deprecated) |
| [ENTERPRISE-ARCHITECTURE-AUDIT.md](./ENTERPRISE-ARCHITECTURE-AUDIT.md) | Enterprise architecture audit and findings |
| [11-security-context-propagation.md](./11-security-context-propagation.md) | JWT hybrid strategy and security context |

---

## Module blueprints

`module-blueprints/` — one folder per bounded context (see catalog for **ACTIVE** vs **DEPRECATED**).

**Canonical authentication:** `01-identity-access-management` only.  
**Deprecated:** `05-authentication-management` (historical reference).

---

## Cross-cutting rules

Aggregate, reactive, event, multitenancy, auditing, and AI rules live at repository root (`01`–`17` engineering specifications).
