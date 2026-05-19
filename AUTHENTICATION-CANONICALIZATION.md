# Authentication Canonicalization

## CodeCore Architectural Decision Record

### Status: **ACCEPTED**

### Resolves: AUD-001, AUD-003 (partial)

---

## 1. Decision

**`01-identity-access-management` (IAM)** is the **single authoritative bounded context** for platform authentication.

**`05-authentication-management`** is **DEPRECATED** as an independent bounded context. Its blueprint files remain in the repository for historical reference only and MUST NOT guide new implementation.

---

## 2. Context

An enterprise audit identified duplicate ownership between IAM and Authentication Management for:

- login flows
- credentials and sessions
- JWT and refresh tokens
- MFA
- identity OAuth/OIDC
- API keys for platform access
- device trust

This duplication violates DDD bounded-context integrity and risks divergent security implementations.

---

## 3. Canonical ownership (IAM)

IAM exclusively owns:

| Capability | Description |
|------------|-------------|
| Authentication | Login, logout, credential verification |
| Identity lifecycle | Activation, lockout, eligibility |
| Credentials | Password hashes, rotation, policies |
| Sessions | Refresh tokens, revocation, concurrent sessions |
| Token issuance | JWT access tokens (hybrid claims — see `11-security-context-propagation.md`) |
| MFA | Enrollment, challenge, enforcement policies |
| Identity OAuth/OIDC | User-facing federated login (Google, Microsoft, SSO, etc.) |
| API keys | Platform/service API authentication where identity-bound |
| Device trust | Trusted device validation for step-up / risk reduction |
| Login security | Brute-force protection, failed attempt tracking |
| Authentication events | Domain and integration events for auth facts |

IAM does **not** own:

- operational user profiles → **User Management (03)**
- permissions, roles, policies → **Authorization Management (04)**
- tenant lifecycle → **Tenant Management (02)**
- integration OAuth for external systems (CRM, ERP) → **Integration Management (14)**
- immutable compliance audit store → **Audit Management (06)**

---

## 4. Deprecation mapping (`05` → `01`)

Use this table when reading deprecated Authentication Management blueprints:

| Deprecated (05) concept | Canonical location (01 IAM) |
|-------------------------|-----------------------------|
| `AuthenticationAggregate` | `IdentityAggregate` + `LoginApplicationService` (orchestration) |
| `SessionAggregate` | `SessionAggregate` (IAM) |
| `RefreshTokenAggregate` | `SessionAggregate` (IAM) |
| `MFAAggregate` | IAM — MFA enrollment/challenge (extend IAM aggregates) |
| `DeviceTrustAggregate` | IAM — device trust policies |
| `APIKeyAggregate` | IAM — API key lifecycle |
| `ServiceIdentityAggregate` | IAM — service-to-service identity |
| `AuthenticationAuditAggregate` | **Removed as aggregate** — emit audit/integration events; **Audit Management** persists |

---

## 5. Cross-module reference rule

All specifications MUST reference:

```text
Identity & Access Management (IAM)
```

or shorthand **`IAM`** after first use.

**Forbidden** in new documentation:

- `Authentication Management` as a live bounded context
- `Identity Management` (ambiguous — use IAM + User Management)

---

## 6. JWT and authorization (AUD-003)

JWT strategy is defined in **`11-security-context-propagation.md`** (hybrid model).

**Authorization Management (04)** remains the **authoritative** runtime validator for permissions and dynamic policies. JWT may carry **coarse** roles/scopes only; sensitive authorization MUST NOT rely solely on token claims.

---

## 7. Consequences

- New code and AI-generated implementations target **IAM only** for authentication.
- Module catalog lists `05` as **DEPRECATED**.
- Notification, audit, and tenant modules consume IAM integration events (e.g. `IdentityAuthenticatedIntegrationEvent`).
- Integration Management retains **integration OAuth** (third-party connectors), not user login OAuth.

---

## 8. Related documents

- `module-blueprints/01-identity-access-management/`
- `module-blueprints/05-authentication-management/DEPRECATED.md`
- `11-security-context-propagation.md`
- `MODULE-CATALOG.md`
- `ENTERPRISE-ARCHITECTURE-AUDIT.md`
