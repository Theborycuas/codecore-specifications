# security-rules.md

````md id="n6v1qy"
# Identity & Access Management (IAM)
## Security Enforcement Rules
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official security enforcement rules for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- enforce authentication security boundaries
- preserve tenant-safe access control
- protect authentication integrity
- standardize security propagation
- prevent credential exposure
- support reactive-safe security execution
- preserve auditability and observability
- guide AI-assisted implementation

---

# 2. SECURITY PHILOSOPHY

IAM is the primary security gateway of CodeCore.

IAM security exists to:
- authenticate identities safely
- protect tenant isolation
- preserve session integrity
- prevent unauthorized access
- propagate secure authentication context
- support scalable stateless security

IAM security MUST:
- fail securely
- remain tenant-aware
- remain reactive-safe
- remain observable
- remain auditable

---

# 3. OFFICIAL SECURITY MODEL

---

# 3.1 Official Authentication Strategy

CodeCore officially adopts:

```text id="officialauthstrategy"
JWT + Refresh Token Authentication
````

---

# 3.2 Official Security Propagation Strategy

CodeCore officially adopts:

```text id="securitypropagation"
Reactive Security Context + Reactor Context
```

---

# 3.3 Stateless Authentication Principle

Authentication SHOULD remain:

* stateless
* distributed-safe
* horizontally scalable

---

# 4. AUTHENTICATION RULES

---

# 4.1 Authentication Integrity Principle

Authentication MUST validate:

* credential integrity
* tenant ownership
* account eligibility
* session validity

before access is granted.

---

# 4.2 Authentication Eligibility Rules

Identities MUST NOT authenticate when:

```text id="invalidauthstates"
LOCKED
DISABLED
EXPIRED
PENDING_VERIFICATION
PASSWORD_RESET_REQUIRED
```

unless explicitly allowed.

---

# 4.3 Secure Failure Principle

Authentication failures MUST:

* deny access safely
* avoid credential leakage
* avoid identity enumeration

---

# 4.4 Timing Attack Protection

Credential validation SHOULD:

* minimize timing attack exposure

---

# 4.5 Authentication Flow (implemented — CodeCore PASO 10.9)

**Scope:** application use case only — no JWT, sessions, or HTTP in this step.

```text
AuthenticationCommand (tenantId, email, rawPassword)
  → validate inputs
  → IdentityRepository.findByTenantAndEmail
  → missing identity → InvalidCredentialsException (generic message)
  → status != ACTIVE → IdentityNotAllowedToAuthenticateException
  → PasswordHasher.matches → false → InvalidCredentialsException
  → AuthenticationResult (identityId, tenantId, email, status)
```

**Eligibility (PASO 10.9):**

| Status | Authentication |
|--------|----------------|
| ACTIVE | Allowed |
| PENDING_VERIFICATION | Rejected |
| LOCKED | Rejected |
| DISABLED | Rejected |
| PASSWORD_RESET_REQUIRED | Rejected |

**Output restrictions:** no password hash, no tokens, no credential fields in `AuthenticationResult`.

---

# 5. PASSWORD SECURITY RULES

---

# 5.1 Password Storage Principle

Passwords MUST:

* never be stored in plain text
* always be hashed

---

# 5.2 Official Hashing Strategy

Recommended algorithm:

```text id="officialhashing"
BCrypt
```

---

# 5.3 Password Complexity Rules

Passwords SHOULD support:

* minimum length
* complexity validation
* entropy validation
* reuse prevention

---

# 5.4 Password Exposure Forbidden

Passwords MUST NEVER:

* appear in logs
* appear in events
* appear in traces
* appear in exceptions

---

# 5.5 Password Rotation Rules

Credential rotation SHOULD:

* invalidate sensitive sessions
* generate audit events

---

# 6. JWT SECURITY RULES

---

# 6.1 JWT Integrity Principle

JWT tokens MUST:

* remain signed
* remain expiration-aware
* remain tamper-resistant

---

# 6.2 Mandatory JWT Claims

Recommended claims:

```text id="jwtclaims"
sub
tenant_id
roles
permissions
iat
exp
jti
```

---

# 6.3 JWT Expiration Rules

JWT access tokens SHOULD:

* remain short-lived

---

# 6.4 JWT Secret Protection

JWT signing secrets MUST:

* remain externalized
* remain encrypted
* remain rotation-capable

---

# 6.5 Token Exposure Forbidden

JWT tokens MUST NOT:

* appear in logs
* appear in traces
* appear in events unintentionally

---

# 7. REFRESH TOKEN SECURITY RULES

---

# 7.1 Refresh Token Integrity Principle

Refresh tokens MUST:

* support revocation
* support expiration
* support rotation

---

# 7.2 Replay Protection Principle

Refresh token reuse MUST:

* trigger compromise protection

---

# 7.3 Secure Storage Principle

Persisted refresh tokens SHOULD:

* remain hashed

---

# 7.4 Rotation Integrity Principle

Refresh token rotation MUST:

* invalidate previous token immediately

---

# 8. SESSION SECURITY RULES

---

# 8.1 Session Integrity Principle

Sessions MUST:

* remain identity-bound
* remain tenant-bound

---

# 8.2 Revocation Principle

Revoked sessions MUST:

* reject future refresh attempts

---

# 8.3 Concurrent Session Policies

Supported strategies MAY include:

```text id="sessionstrategies"
ALLOW_MULTIPLE
LIMIT_BY_DEVICE
LIMIT_BY_SESSION_COUNT
FORCE_SINGLE_SESSION
```

---

# 8.4 Session Traceability Principle

Sessions SHOULD support:

* device traceability
* activity tracking
* anomaly detection

---

# 9. MULTITENANCY SECURITY RULES

---

# 9.1 Tenant Isolation Principle

IAM security MUST preserve:

* strict tenant isolation

---

# 9.2 Cross Tenant Authentication Forbidden

Authentication MUST NEVER:

* authenticate identities across tenants unintentionally

---

# 9.3 Tenant-Aware JWT Principle

JWT tokens MUST propagate:

* tenant ownership metadata

---

# 9.4 Tenant Context Integrity

Tenant context MUST propagate through:

* Reactor Context
* Security Context
* event pipelines

---

# 10. REACTIVE SECURITY RULES

---

# 10.1 Official Reactive Security Standard

IAM security execution MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 10.2 Blocking Security Operations Forbidden

Forbidden:

* JDBC
* Thread.sleep
* blocking token validation
* imperative waiting
* .block()

inside security execution chains.

---

# 10.3 ThreadLocal Security Forbidden

Forbidden:

```text id="threadlocalforbidden"
SecurityContextHolder
ThreadLocal authentication propagation
Static security holders
```

inside reactive execution flows.

---

# 10.4 Context Preservation Principle

Reactive security execution MUST preserve:

* authentication context
* tenant context
* correlation IDs
* trace IDs

---

# 11. ACCOUNT LOCKOUT RULES

---

# 11.1 Brute Force Protection Principle

IAM MUST protect against:

* brute-force authentication attempts
* credential stuffing
* suspicious login behavior

---

# 11.2 Lockout Trigger Rules

Repeated failures SHOULD:

* increase risk score
* trigger temporary lockouts
* generate security events

---

# 11.3 Lockout Visibility Principle

Lockout events MUST remain:

* observable
* auditable
* traceable

---

# 12. AUTHORIZATION BOUNDARY RULES

---

# 12.1 Responsibility Separation Principle

IAM authenticates identities.

Authorization Management decides:

* permissions
* roles
* resource access

---

# 12.2 Forbidden Authorization Ownership

IAM MUST NOT:

* own business permissions
* own business roles
* own business access policies

---

# 12.3 Security Coordination Principle

IAM MAY propagate:

* identity claims
* security claims
* tenant claims

for downstream authorization.

---

# 13. API SECURITY RULES

---

# 13.1 Secure API Principle

IAM APIs MUST:

* require HTTPS
* validate JWT integrity
* validate tenant ownership

---

# 13.2 Authentication Endpoint Protection

Authentication endpoints SHOULD support:

* rate limiting
* abuse protection
* anomaly detection

---

# 13.3 Secure Error Principle

Security errors MUST:

* avoid sensitive information exposure

---

# 14. EVENT SECURITY RULES

---

# 14.1 Event Security Principle

IAM events MUST:

* remain tenant-aware
* avoid credential exposure
* preserve traceability

---

# 14.2 Sensitive Event Restrictions

Events MUST NEVER expose:

* raw passwords
* raw refresh tokens
* internal secrets

---

# 14.3 Replay Safety Principle

Security-sensitive event processing SHOULD support:

* idempotency
* replay protection
* duplicate tolerance

---

# 15. OBSERVABILITY RULES

---

# 15.1 Security Visibility Principle

Critical security workflows MUST remain:

* observable
* traceable
* measurable

---

# 15.2 Mandatory Security Metadata

Recommended metadata:

```text id="securitymetadata"
tenant_id
identity_id
session_id
correlation_id
trace_id
```

---

# 15.3 Suspicious Activity Visibility

Suspicious behavior SHOULD generate:

* security events
* audit records
* operational alerts

---

# 16. AUDITING RULES

---

# 16.1 Mandatory Auditability

Critical security operations MUST remain:

* auditable
* historically traceable

---

# 16.2 Mandatory Audited Operations

The following MUST generate audit records:

* Login
* Logout
* Password Change
* Password Reset
* Session Revocation
* Lockout Trigger
* Refresh Token Rotation

---

# 16.3 Immutable Audit Principle

Security audit history SHOULD remain:

* append-only
* immutable

---

# 17. INFRASTRUCTURE SECURITY RULES

---

# 17.1 Secret Management Principle

Secrets MUST:

* remain externalized
* remain encrypted
* avoid hardcoded persistence

---

# 17.2 Transport Security Principle

Authentication traffic MUST use:

* HTTPS/TLS

---

# 17.3 Secure Persistence Principle

Security-sensitive persistence MUST:

* remain encrypted when appropriate
* remain tenant-aware
* remain auditable

---

# 18. FAILURE HANDLING RULES

---

# 18.1 Fail Secure Principle

Security failures MUST:

* deny access safely
* preserve observability
* preserve auditability

---

# 18.2 Retry Protection Principle

Authentication retries SHOULD:

* avoid amplification attacks
* support bounded retries

---

# 18.3 Security Failure Isolation

Security failures SHOULD remain:

* isolated
* observable
* diagnosable

---

# 19. FUTURE SECURITY EXTENSIBILITY

---

# 19.1 Extensibility Principle

IAM security architecture MUST remain extensible for:

* MFA
* OAuth2
* SSO
* Social Login
* Device Fingerprinting
* Adaptive Authentication
* Risk-Based Authentication
* Hardware Security Keys

---

# 19.2 Identity Federation Readiness

Security architecture SHOULD remain:

* provider-agnostic
* modular
* extensible

---

# 20. FORBIDDEN SECURITY ANTI-PATTERNS

---

# Forbidden

* Plain text password storage
* JWT secret exposure
* Cross-tenant authentication leakage
* ThreadLocal security propagation
* Blocking authentication flows
* Raw token persistence
* SecurityContextHolder usage in reactive flows
* Hardcoded credentials
* Entity exposure through APIs
* Non-traceable security failures
* Shared mutable authentication state

---

# 21. AI IMPLEMENTATION RULES

All AI-generated IAM security logic MUST:

* remain fully reactive
* preserve tenant isolation
* avoid credential exposure
* preserve JWT integrity
* preserve replay protection
* avoid blocking execution
* preserve auditability
* preserve observability
* preserve security-safe context propagation
* fail securely by default

---

# 22. CODECORE IAM SECURITY PHILOSOPHY

```text id="securityphilosophy"
IAM security exists to provide
reactive, tenant-aware and traceable
authentication protection
through stateless identity verification,
secure context propagation
and consistency-preserving security enforcement.
```

```
```

