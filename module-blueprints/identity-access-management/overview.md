# overview.md

````md
# Identity & Access Management (IAM)
## Module Blueprint Overview
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

The Identity & Access Management module (IAM) is responsible for:

- authentication
- identity lifecycle management
- credential management
- session management
- token issuance
- token validation
- access entry control
- authentication security boundaries
- multi-factor authentication (MFA)
- identity OAuth / OpenID Connect (user login)
- API key authentication (platform identity-bound access)
- device trust validation
- refresh token lifecycle
- login and logout flows

This module acts as the foundational security gateway of CodeCore.

All platform access MUST originate through IAM-controlled authentication flows.

IAM is one of the most critical bounded contexts in CodeCore because every other module depends directly or indirectly on authenticated identity propagation.

IAM is the **canonical and sole authoritative bounded context** for platform authentication.

`05-authentication-management` is **DEPRECATED** — see `AUTHENTICATION-CANONICALIZATION.md` and `module-blueprints/05-authentication-management/DEPRECATED.md`.

---

# 2. BOUNDED CONTEXT DEFINITION

The IAM bounded context governs:

```text
Who can access the platform,
how identities authenticate,
how sessions are maintained,
and how authentication state propagates
through the system.
````

IAM owns:

* credentials
* authentication flows
* refresh tokens
* session lifecycle
* login attempts
* password policies
* authentication state
* MFA enrollment and verification
* identity OAuth/OIDC login flows
* API keys for platform authentication
* device trust state
* JWT access token issuance (hybrid claims per `11-security-context-propagation.md`)

IAM does NOT own:

* business profiles
* tenant memberships
* authorization permissions
* business roles
* operational user data

Those belong to:

* User Management
* Authorization Management
* Tenant Management

---

# 3. CORE RESPONSIBILITIES

---

# 3.1 Authentication Responsibilities

IAM is responsible for:

* login validation
* password authentication
* credential verification
* token generation
* token refresh
* logout handling
* session revocation

---

# 3.2 Credential Responsibilities

IAM owns:

* password hashes
* password reset lifecycle
* credential expiration
* credential validation
* credential policies

---

# 3.3 Session Responsibilities

IAM governs:

* refresh token lifecycle
* active sessions
* token revocation
* concurrent session control
* session invalidation

---

# 3.4 Identity Security Responsibilities

IAM enforces:

* account lockout
* brute-force protection
* failed login tracking
* security event generation
* suspicious activity detection

---

# 3.5 Authentication Context Responsibilities

IAM propagates:

* authenticated identity
* tenant identity
* authentication metadata
* security context
* traceable access context

through:

* JWT
* Reactor Context
* Reactive Security Context

---

# 4. CORE CAPABILITIES

The IAM module MUST support:

| Capability             | Description                     |
| ---------------------- | ------------------------------- |
| Login                  | Authenticate identities         |
| Logout                 | Revoke sessions                 |
| Refresh Token          | Renew access tokens             |
| Password Reset         | Recover credentials             |
| Session Revocation     | Invalidate compromised sessions |
| Credential Rotation    | Change credentials safely       |
| Failed Login Detection | Detect brute-force behavior     |
| Session Tracking       | Monitor active sessions         |
| Token Validation       | Validate access tokens          |
| MFA                    | Multi-factor authentication     |
| Identity OAuth/OIDC    | Federated user login            |
| API Key Auth           | Service and API identity access |
| Device Trust           | Trusted device policies         |
| Security Auditing      | Produce audit events            |

---

# 5. BUSINESS RULES

---

# 5.1 Identity Ownership Rule

Every authenticated identity MUST:

* belong to exactly one primary identity record

---

# 5.2 Credential Security Rule

Passwords MUST:

* never be stored in plain text
* always be hashed
* remain inaccessible after persistence

---

# 5.3 Authentication Integrity Rule

Successful authentication MUST require:

* valid credentials
* active identity status
* tenant validity
* non-revoked access state

---

# 5.4 Failed Authentication Rule

Repeated failed authentication attempts SHOULD:

* increase security risk score
* trigger lockout policies
* generate audit events

---

# 5.5 Token Integrity Rule

JWT tokens MUST:

* be signed
* expire safely
* remain tenant-aware
* remain traceable

---

# 5.6 Session Consistency Rule

Revoked sessions MUST:

* immediately lose refresh capability

---

# 5.7 Tenant Safety Rule

Authentication MUST preserve:

* tenant isolation
* tenant ownership
* tenant-safe context propagation

---

# 6. OWNERSHIP BOUNDARIES

---

# IAM Owns

* Authentication
* Credentials
* Refresh Tokens
* Sessions
* Login Attempts
* Token Lifecycle
* Password Policies
* MFA
* Identity OAuth/OIDC
* API Keys (platform authentication)
* Device Trust
* Authentication Events

---

# IAM Does NOT Own

* User profile data
* Business roles
* Permission matrices
* Tenant lifecycle
* Business workflows
* Clinical information
* Scheduling ownership

---

# 7. EXTERNAL DEPENDENCIES

IAM depends on:

* Tenant Management
* User Management
* Authorization Management
* Audit Management
* Observability Infrastructure

---

# 8. INTERNAL DEPENDENCIES

IAM internally depends on:

* Reactive Security
* JWT Infrastructure
* Redis
* R2DBC
* Password Hashing
* Reactor Context Propagation

---

# 9. MULTITENANCY STRATEGY

IAM is STRICTLY tenant-aware.

---

# 9.1 Tenant-Aware Authentication

Authentication MUST validate:

* tenant existence
* tenant status
* tenant ownership consistency

---

# 9.2 Cross Tenant Authentication Forbidden

Identities MUST NEVER:

* authenticate across tenants unintentionally

---

# 9.3 Tenant Context Propagation

Authenticated tenant context MUST propagate through:

* JWT claims
* Reactor Context
* Security Context

---

# 10. SECURITY RESPONSIBILITIES

IAM is the primary security gateway of CodeCore.

---

# IAM MUST enforce

* password hashing
* token signing
* session revocation
* brute-force protection
* lockout protection
* credential expiration
* reactive-safe authentication
* auditability
* traceability

---

# IAM MUST NOT

* expose credential internals
* expose sensitive secrets
* bypass authorization boundaries
* trust frontend authentication alone

---

# 11. EVENT RESPONSIBILITIES

IAM publishes authentication-related events.

---

# Example Events

```text
UserAuthenticated
AuthenticationFailed
PasswordChanged
PasswordResetRequested
SessionRevoked
RefreshTokenRotated
AccountLocked
```

---

# Event Philosophy

IAM events MUST:

* represent completed facts
* remain immutable
* remain tenant-aware
* remain security-safe

---

# 12. REACTIVE RESPONSIBILITIES

IAM MUST remain fully reactive.

---

# Mandatory Reactive Rules

* Non-blocking authentication
* Reactor Context propagation
* Reactive Security Context usage
* Reactive token validation
* Reactive Redis usage
* Non-blocking persistence

---

# Forbidden

* ThreadLocal security propagation
* Blocking JDBC
* .block()
* imperative security leakage

---

# 13. SCALABILITY STRATEGY

IAM MUST support:

* horizontal scaling
* distributed deployments
* stateless authentication
* high concurrency
* low latency token validation

---

# Scalability Principles

Preferred strategies:

* JWT stateless access tokens
* Redis-backed refresh token management
* Reactive Redis
* Distributed-safe revocation tracking

---

# 14. OBSERVABILITY RESPONSIBILITIES

IAM MUST provide:

* authentication tracing
* security metrics
* correlation propagation
* failed login monitoring
* suspicious activity visibility

---

# Mandatory Observability Metadata

```text
tenant_id
correlation_id
trace_id
session_id
actor_id
```

---

# 15. AUDITING RESPONSIBILITIES

IAM operations MUST remain auditable.

---

# Mandatory Audit Operations

* Login
* Logout
* Failed Login
* Password Change
* Password Reset
* Session Revocation
* Account Lockout
* Token Refresh

---

# 16. FUTURE EXTENSIBILITY

IAM architecture MUST remain extensible for:

* MFA
* OAuth2
* SSO
* Social Login
* Biometric Authentication
* Device Fingerprinting
* Adaptive Authentication
* Risk-Based Authentication

---

# 17. NON-GOALS

IAM does NOT aim to:

* become a workflow engine
* become an authorization engine
* manage business permissions
* manage business roles
* own business user profiles

---

# 18. MODULE INTEGRATION PHILOSOPHY

IAM integrates through:

* events
* authentication contracts
* security context propagation
* tenant-aware identity propagation

NOT through:

* tight coupling
* direct database access
* shared mutable state

---

# 19. FAILURE PHILOSOPHY

Authentication failures MUST:

* fail safely
* deny access by default
* remain observable
* remain auditable

---

# 20. CODECORE IAM OFFICIAL PHILOSOPHY

```text
Identity & Access Management exists to provide
secure, reactive, tenant-aware and traceable
authentication boundaries for the entire platform
through immutable identity verification,
stateless token propagation
and scalable security enforcement.
```

```
```
