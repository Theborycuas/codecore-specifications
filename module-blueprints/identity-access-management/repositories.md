# repositories.md

````md id="x9k2vr"
# Identity & Access Management (IAM)
## Repository Engineering
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official repository model for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- standardize aggregate persistence
- preserve tenant-safe data access
- protect aggregate consistency boundaries
- enforce reactive-safe persistence
- define repository ownership rules
- support scalable authentication persistence
- preserve observability and auditability
- guide AI-assisted implementation

---

# 2. REPOSITORY PHILOSOPHY

IAM repositories exist to:
- persist aggregates
- retrieve aggregate state
- support consistency boundaries
- preserve tenant isolation
- provide reactive-safe persistence access

IAM repositories MUST:
- remain aggregate-oriented
- remain tenant-aware
- remain reactive
- avoid business orchestration
- preserve consistency boundaries

---

# 3. OFFICIAL IAM REPOSITORIES

The IAM bounded context officially defines:

| Repository | Aggregate |
|---|---|
| IdentityRepository | IdentityAggregate |
| SessionRepository | SessionAggregate |
| PasswordResetRepository | PasswordResetAggregate |
| LoginAttemptRepository | LoginAttemptAggregate |

---

# 4. IDENTITY REPOSITORY

---

# 4.1 Purpose

IdentityRepository manages:
- identity persistence
- identity retrieval
- authentication lookup operations
- credential consistency persistence

---

# 4.2 Aggregate Ownership

IdentityRepository persists:
- IdentityAggregate

ONLY.

---

# 4.3 Recommended Operations

Recommended operations:

```text id="identityrepositoryops"
save()
findById()
findByEmail()
findByUsername()
findByTenantAndEmail()
findByTenantAndUsername()
existsByTenantAndEmail()
existsByTenantAndUsername()
delete()
````

---

# 4.4 Authentication Query Rules

Authentication queries MUST:

* remain tenant-aware
* remain indexed
* preserve identity uniqueness

---

# 4.5 Forbidden Responsibilities

IdentityRepository MUST NOT:

* orchestrate workflows
* generate JWTs
* manage sessions
* perform security decisions
* invoke external APIs

---

# 5. SESSION REPOSITORY

---

# 5.1 Purpose

SessionRepository manages:

* session persistence
* refresh token persistence
* session revocation state
* active session retrieval

---

# 5.2 Aggregate Ownership

SessionRepository persists:

* SessionAggregate

ONLY.

---

# 5.3 Recommended Operations

Recommended operations:

```text id="sessionrepositoryops"
save()
findById()
findBySessionId()
findByRefreshToken()
findActiveSessionsByIdentity()
findRevokedSessions()
revokeSession()
revokeAllSessionsByIdentity()
deleteExpiredSessions()
```

---

# 5.4 Session Integrity Rules

Session queries MUST preserve:

* revocation consistency
* refresh token uniqueness
* session ownership integrity

---

# 5.5 Forbidden Responsibilities

SessionRepository MUST NOT:

* authenticate passwords
* manage permissions
* orchestrate notifications
* generate tokens

---

# 6. PASSWORD RESET REPOSITORY

---

# 6.1 Purpose

PasswordResetRepository manages:

* reset request persistence
* reset token lookup
* reset lifecycle consistency

---

# 6.2 Aggregate Ownership

PasswordResetRepository persists:

* PasswordResetAggregate

ONLY.

---

# 6.3 Recommended Operations

Recommended operations:

```text id="passwordresetrepositoryops"
save()
findById()
findByResetToken()
findPendingRequestsByIdentity()
expirePendingRequests()
markUsed()
deleteExpiredRequests()
```

---

# 6.4 Reset Integrity Rules

Password reset persistence MUST preserve:

* token uniqueness
* expiration consistency
* single-use integrity

---

# 6.5 Forbidden Responsibilities

PasswordResetRepository MUST NOT:

* send notifications
* orchestrate workflows
* authenticate identities

---

# 7. LOGIN ATTEMPT REPOSITORY

---

# 7.1 Purpose

LoginAttemptRepository manages:

* failed authentication tracking
* lockout persistence
* brute-force protection persistence

---

# 7.2 Aggregate Ownership

LoginAttemptRepository persists:

* LoginAttemptAggregate

ONLY.

---

# 7.3 Recommended Operations

Recommended operations:

```text id="loginattemptrepositoryops"
save()
findByIdentity()
findRecentFailures()
incrementFailures()
clearFailures()
findLockedIdentities()
deleteExpiredAttempts()
```

---

# 7.4 Security Rules

Login attempt persistence MUST support:

* abuse detection
* traceability
* auditability

---

# 7.5 Forbidden Responsibilities

LoginAttemptRepository MUST NOT:

* authenticate identities
* manage sessions
* generate risk decisions independently

---

# 8. AGGREGATE BOUNDARY RULES

---

# 8.1 Aggregate Isolation Principle

Repositories MUST persist:

* one aggregate boundary only

---

# 8.2 Cross Aggregate Persistence Forbidden

Repositories MUST NOT:

* mutate multiple aggregate internals directly

---

# 8.3 Aggregate Coordination Principle

Cross-aggregate coordination SHOULD occur through:

* Application Services
* Domain Events
* Orchestration Services

---

# 9. REACTIVE PERSISTENCE RULES

---

# 9.1 Official Reactive Standard

IAM repositories MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 9.2 Official Persistence Strategy

Recommended persistence stack:

```text id="officialpersistencestack"
Spring Data R2DBC
Reactive PostgreSQL
Reactive Redis
```

---

# 9.3 Blocking Persistence Forbidden

Forbidden:

* JDBC
* blocking ORM execution
* imperative waiting
* .block()

inside repository execution chains.

---

# 9.4 Reactive Return Types

Repositories SHOULD return:

* Mono
* Flux

ONLY.

---

# 10. MULTITENANCY RULES

---

# 10.1 Mandatory Tenant Filtering

All tenant-owned queries MUST enforce:

* tenant filtering

---

# Forbidden

```sql id="tenantblindquery"
SELECT * FROM identities;
```

---

# Correct

```sql id="tenantawarequery"
SELECT * FROM identities
WHERE tenant_id = :tenantId;
```

---

# 10.2 Cross Tenant Leakage Forbidden

Repositories MUST NEVER:

* expose another tenant’s authentication data

---

# 10.3 Tenant Ownership Integrity

Repository persistence MUST preserve:

* immutable tenant ownership

---

# 11. CONCURRENCY RULES

---

# 11.1 Optimistic Locking Principle

Critical IAM aggregates SHOULD support:

* optimistic concurrency control

---

# 11.2 Concurrent Refresh Protection

Repositories MUST preserve:

* refresh token consistency
* session revocation consistency

under concurrent access.

---

# 11.3 Duplicate Authentication Protection

Persistence MUST avoid:

* duplicate active identity corruption

---

# 12. SECURITY RULES

---

# 12.1 Credential Protection Principle

Repositories MUST NEVER expose:

* raw passwords
* credential internals
* sensitive secrets

---

# 12.2 Token Security Principle

Refresh tokens SHOULD:

* remain hashed when persisted

---

# 12.3 Secure Persistence Principle

Security-sensitive persistence MUST:

* remain auditable
* remain traceable
* remain tenant-aware

---

# 13. OBSERVABILITY RULES

---

# 13.1 Repository Traceability

Critical repository operations SHOULD expose:

* correlation IDs
* trace IDs
* tenant metadata

---

# 13.2 Security Visibility

Authentication persistence anomalies SHOULD remain:

* observable
* measurable

---

# 13.3 Reactive Visibility Principle

Reactive persistence failures MUST remain:

* traceable
* diagnosable

---

# 14. AUDITING RULES

---

# 14.1 Auditability Principle

Critical repository mutations SHOULD remain:

* auditable
* historically traceable

---

# 14.2 Security Audit Principle

Security-sensitive persistence SHOULD generate:

* audit records
* operational traces

when appropriate.

---

# 15. PERFORMANCE RULES

---

# 15.1 Query Optimization Principle

Authentication queries MUST remain:

* indexed
* low-latency
* scalable

---

# 15.2 Session Cleanup Principle

Expired sessions SHOULD support:

* scheduled cleanup
* reactive-safe deletion

---

# 15.3 Lightweight Persistence Principle

Repositories SHOULD avoid:

* oversized graph loading
* unnecessary joins
* blocking hydration

---

# 16. FAILURE HANDLING RULES

---

# 16.1 Failure Isolation Principle

Repository failures SHOULD remain:

* isolated
* observable
* diagnosable

---

# 16.2 Retry Safety Principle

Retries MUST preserve:

* authentication consistency
* token integrity
* session integrity

---

# 16.3 Persistence Safety Principle

Failed persistence MUST NOT:

* corrupt aggregate state

---

# 17. STORAGE STRATEGY RULES

---

# 17.1 Official Storage Technologies

Recommended storage technologies:

| Technology | Purpose                    |
| ---------- | -------------------------- |
| PostgreSQL | Primary persistence        |
| Redis      | Token/session acceleration |
| Flyway     | Schema migration           |
| R2DBC      | Reactive persistence       |

---

# 17.2 Redis Usage Strategy

Redis MAY support:

* refresh token caching
* session acceleration
* revocation lookup optimization

---

# 17.3 Source of Truth Principle

PostgreSQL remains:

* the primary source of truth

unless explicitly defined otherwise.

---

# 18. FORBIDDEN REPOSITORY ANTI-PATTERNS

---

# Forbidden

* Tenant-blind repositories
* Business orchestration inside repositories
* Blocking JDBC access
* Cross-aggregate mutation
* Shared mutable authentication state
* JWT generation inside repositories
* External API invocation
* Oversized aggregate hydration
* Raw credential persistence
* Permission ownership inside IAM repositories

---

# 19. AI IMPLEMENTATION RULES

All AI-generated IAM repositories MUST:

* remain fully reactive
* preserve tenant isolation
* preserve aggregate boundaries
* avoid business orchestration
* avoid blocking execution
* support optimistic locking
* preserve observability
* preserve auditability
* avoid raw credential exposure
* preserve reactive-safe persistence

---

# 20. CODECORE IAM REPOSITORY PHILOSOPHY

```text id="repositoryphilosophy"
IAM repositories exist to provide
reactive, tenant-aware and consistency-safe
aggregate persistence
through isolated authentication storage boundaries,
non-blocking data access
and security-preserving persistence orchestration.
```

```
```
