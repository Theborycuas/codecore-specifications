# aggregates.md

````md id="rj5tqk"
# Identity & Access Management (IAM)
## Aggregate Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official aggregate model for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- define consistency boundaries
- establish aggregate ownership
- protect authentication invariants
- preserve tenant-safe authentication
- standardize identity lifecycle behavior
- guide reactive-safe implementation
- prevent aggregate misuse
- support scalable security architecture

---

# 2. AGGREGATE PHILOSOPHY

IAM aggregates exist to protect:
- authentication consistency
- credential integrity
- session integrity
- token lifecycle integrity
- authentication security boundaries

IAM aggregates MUST remain:
- cohesive
- security-oriented
- tenant-aware
- reactive-safe
- concurrency-safe

---

# 3. OFFICIAL IAM AGGREGATES

The IAM bounded context officially defines:

| Aggregate | Responsibility |
|---|---|
| IdentityAggregate | Authentication identity lifecycle |
| SessionAggregate | Refresh token and session lifecycle |
| PasswordResetAggregate | Password recovery lifecycle |
| LoginAttemptAggregate | Authentication security protection |

---

# 4. IDENTITY AGGREGATE

---

# 4.1 Purpose

The IdentityAggregate is the primary aggregate of IAM.

It governs:
- identity authentication
- credential ownership
- account status
- authentication eligibility
- credential lifecycle
- authentication state consistency

---

# 4.2 Aggregate Root

```text
Identity
````

---

# 4.3 Responsibilities

IdentityAggregate owns:

* email/username identity
* password hash
* authentication state
* account activation state
* credential rotation
* lockout state
* authentication eligibility

---

# 4.4 Aggregate Invariants

The following invariants MUST always hold:

---

## Identity Uniqueness

Each identity MUST be globally unique within:

* its tenant scope

---

## Credential Integrity

Credentials MUST:

* always exist for active accounts
* remain hashed
* never be exposed

---

## Authentication Eligibility

Only identities in valid state MAY authenticate.

Valid states MAY include:

```text
ACTIVE
LOCKED
DISABLED
PENDING_VERIFICATION
PASSWORD_RESET_REQUIRED
```

---

## Tenant Ownership Integrity

Identity ownership MUST:

* belong to one tenant
* remain immutable

---

## Lockout Integrity

Locked accounts MUST:

* reject authentication attempts

---

# 4.5 Aggregate Behaviors

IdentityAggregate MAY perform:

* authenticate()
* changePassword()
* lockAccount()
* unlockAccount()
* disable()
* enable()
* requirePasswordReset()
* validateAuthenticationEligibility()

---

# 4.6 Forbidden Responsibilities

IdentityAggregate MUST NOT:

* manage business roles
* manage permissions
* manage user profile data
* manage sessions directly
* orchestrate notifications

---

# 4.7 Aggregate Consistency Boundary

IdentityAggregate protects:

```text
Authentication Identity Consistency
```

NOT:

* authorization consistency
* workflow consistency
* tenant lifecycle consistency

---

# 5. SESSION AGGREGATE

---

# 5.1 Purpose

SessionAggregate governs:

* refresh token lifecycle
* active session lifecycle
* session revocation
* token rotation
* concurrent session consistency

---

# 5.2 Aggregate Root

```text
Session
```

---

# 5.3 Responsibilities

SessionAggregate owns:

* refresh tokens
* session state
* revocation state
* device association
* session expiration
* refresh lifecycle

---

# 5.4 Aggregate Invariants

---

## Revoked Session Integrity

Revoked sessions MUST:

* reject refresh attempts

---

## Token Rotation Integrity

Refresh token rotation MUST:

* invalidate previous refresh token

---

## Session Expiration Integrity

Expired sessions MUST:

* reject access renewal

---

## Tenant Ownership Integrity

Sessions MUST remain:

* tenant-bound
* identity-bound

---

# 5.5 Aggregate Behaviors

SessionAggregate MAY perform:

* refresh()
* revoke()
* expire()
* rotateRefreshToken()
* validateRefreshEligibility()

---

# 5.6 Forbidden Responsibilities

SessionAggregate MUST NOT:

* authenticate credentials
* manage permissions
* manage user profiles
* manage business workflows

---

# 5.7 Aggregate Consistency Boundary

SessionAggregate protects:

```text
Authentication Session Consistency
```

---

# 6. PASSWORD RESET AGGREGATE

---

# 6.1 Purpose

PasswordResetAggregate governs:

* password recovery lifecycle
* reset token validity
* reset request integrity

---

# 6.2 Aggregate Root

```text
PasswordResetRequest
```

---

# 6.3 Responsibilities

PasswordResetAggregate owns:

* reset token generation
* reset expiration
* reset validation
* reset completion lifecycle

---

# 6.4 Aggregate Invariants

---

## Token Expiration Integrity

Expired reset tokens MUST:

* reject password reset execution

---

## One-Time Usage Integrity

Password reset tokens MUST:

* be single-use

---

## Identity Ownership Integrity

Reset requests MUST:

* belong to one identity

---

# 6.5 Aggregate Behaviors

PasswordResetAggregate MAY perform:

* generateResetToken()
* validateResetEligibility()
* expireReset()
* completeReset()

---

# 6.6 Forbidden Responsibilities

PasswordResetAggregate MUST NOT:

* authenticate users
* manage active sessions
* manage permissions

---

# 6.7 Aggregate Consistency Boundary

PasswordResetAggregate protects:

```text
Password Recovery Consistency
```

---

# 7. LOGIN ATTEMPT AGGREGATE

---

# 7.1 Purpose

LoginAttemptAggregate governs:

* brute-force protection
* failed login tracking
* suspicious authentication detection
* temporary lockout protection

---

# 7.2 Aggregate Root

```text
LoginAttemptTracker
```

---

# 7.3 Responsibilities

LoginAttemptAggregate owns:

* failed attempt counters
* attempt windows
* temporary restrictions
* suspicious activity scoring

---

# 7.4 Aggregate Invariants

---

## Attempt Threshold Integrity

Excessive failed attempts MUST:

* trigger protection mechanisms

---

## Window Integrity

Attempt tracking MUST:

* respect configured time windows

---

## Isolation Integrity

Attempt tracking MUST remain:

* identity-scoped
* tenant-scoped

---

# 7.5 Aggregate Behaviors

LoginAttemptAggregate MAY perform:

* registerFailure()
* registerSuccess()
* evaluateRisk()
* triggerLockout()
* clearFailures()

---

# 7.6 Forbidden Responsibilities

LoginAttemptAggregate MUST NOT:

* authenticate identities directly
* manage sessions
* manage permissions

---

# 7.7 Aggregate Consistency Boundary

LoginAttemptAggregate protects:

```text
Authentication Abuse Protection Consistency
```

---

# 8. AGGREGATE RELATIONSHIP RULES

---

# 8.1 Aggregate Isolation Principle

IAM aggregates MUST remain:

* independently consistent

---

# 8.2 Cross Aggregate Mutation Restrictions

Aggregates MUST NOT:

* mutate other aggregate internals directly

---

# 8.3 Coordination Principle

Cross-aggregate workflows SHOULD use:

* application services
* orchestration services
* domain events

---

# 9. TRANSACTIONAL RULES

---

# 9.1 Transaction Scope Principle

Transactions SHOULD remain:

* aggregate-scoped

---

# 9.2 Cross Aggregate Transactions

Cross-aggregate transactions SHOULD be minimized.

Preferred strategy:

* eventual consistency
* event coordination

---

# 9.3 Reactive Transaction Principle

All aggregate persistence MUST remain:

* non-blocking
* Reactor-compatible
* reactive-safe

---

# 10. MULTITENANCY RULES

---

# 10.1 Tenant Ownership Principle

All IAM aggregates MUST contain:

```text
tenant_id
```

---

# 10.2 Cross Tenant Integrity

IAM aggregates MUST NEVER:

* reference another tenant’s identity state

---

# 10.3 Tenant Isolation Principle

All aggregate operations MUST remain:

* tenant-aware
* tenant-safe

---

# 11. CONCURRENCY RULES

---

# 11.1 Optimistic Locking Principle

IAM aggregates SHOULD support:

* optimistic concurrency control

---

# 11.2 Duplicate Authentication Protection

Concurrent authentication flows MUST:

* preserve consistency
* prevent duplicate state corruption

---

# 11.3 Session Concurrency Rules

Concurrent refresh requests MUST:

* preserve refresh token integrity

---

# 12. EVENT RULES

---

# 12.1 Aggregate Event Ownership

Aggregates MAY publish:

| Aggregate              | Example Events         |
| ---------------------- | ---------------------- |
| IdentityAggregate      | UserAuthenticated      |
| SessionAggregate       | SessionRevoked         |
| PasswordResetAggregate | PasswordResetRequested |
| LoginAttemptAggregate  | AccountLocked          |

---

# 12.2 Event Philosophy

Aggregate events MUST:

* represent completed facts
* remain immutable
* remain tenant-aware

---

# 13. SECURITY RULES

---

# 13.1 Credential Protection Principle

Credentials MUST NEVER:

* leave aggregate boundaries in raw form

---

# 13.2 Security Integrity Principle

IAM aggregates MUST:

* fail securely
* deny invalid authentication by default

---

# 13.3 Sensitive Exposure Restrictions

Sensitive internal state MUST NOT:

* leak through APIs
* leak through events
* leak through logs

---

# 14. OBSERVABILITY RULES

---

# 14.1 Aggregate Traceability

Critical aggregate operations SHOULD expose:

* traceability
* correlation IDs
* tenant-aware diagnostics

---

# 14.2 Security Observability

Authentication anomalies SHOULD remain:

* observable
* auditable
* measurable

---

# 15. FORBIDDEN AGGREGATE ANTI-PATTERNS

---

# Forbidden

* God aggregates
* Cross-aggregate direct mutation
* Shared mutable authentication state
* Session orchestration inside IdentityAggregate
* Permission ownership inside IAM
* Tenant-blind aggregates
* Mutable credential exposure
* Blocking persistence flows
* Transactional workflow orchestration inside aggregates

---

# 16. AI IMPLEMENTATION RULES

All AI-generated IAM aggregates MUST:

* preserve aggregate isolation
* preserve tenant ownership
* remain reactive-safe
* avoid oversized consistency boundaries
* preserve credential security
* support optimistic locking
* avoid direct cross-aggregate mutation
* preserve immutable event publication
* avoid authorization leakage
* preserve security-safe invariants

---

# 17. CODECORE IAM AGGREGATE PHILOSOPHY

```text
IAM aggregates exist to protect
authentication consistency,
credential integrity,
session lifecycle integrity
and tenant-safe security boundaries
through isolated reactive consistency models.
```

```
```
