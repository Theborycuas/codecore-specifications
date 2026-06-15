# testing-strategy.md

````md id="u2f8ms"
# Identity & Access Management (IAM)
## Testing Strategy
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official testing strategy for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- preserve authentication integrity
- validate security consistency
- guarantee tenant isolation
- ensure reactive correctness
- validate concurrency safety
- protect token lifecycle consistency
- support scalable delivery
- guide AI-assisted implementation

---

# 2. TESTING PHILOSOPHY

IAM testing exists to:
- validate authentication correctness
- preserve security invariants
- prevent regression vulnerabilities
- validate distributed authentication consistency
- ensure tenant-safe execution

IAM testing MUST:
- remain deterministic
- remain isolated
- remain reactive-safe
- remain security-oriented
- remain reproducible

---

# 3. OFFICIAL TESTING STRATEGY

IAM officially adopts:

| Testing Type | Purpose |
|---|---|
| Unit Testing | Domain validation |
| Integration Testing | Persistence and infrastructure validation |
| Reactive Testing | Reactive flow validation |
| Security Testing | Authentication protection validation |
| Concurrency Testing | Token/session race condition validation |
| Contract Testing | API contract validation |
| Event Testing | Event lifecycle validation |
| Multitenancy Testing | Tenant isolation validation |
| Performance Testing | Authentication scalability validation |

---

# 4. UNIT TESTING RULES

---

# 4.1 Purpose

Unit tests validate:
- aggregates
- entities
- value objects
- domain invariants
- lifecycle transitions

---

# 4.2 Unit Test Principles

Unit tests MUST remain:
- isolated
- deterministic
- fast
- infrastructure-independent

---

# 4.3 Mandatory Unit Test Coverage

The following MUST have unit tests:

- IdentityAggregate
- SessionAggregate
- PasswordResetAggregate
- LoginAttemptAggregate
- Password policies
- Lockout policies
- Token expiration validation
- Authentication state transitions

---

# 4.4 Forbidden Unit Test Patterns

Forbidden:
- database dependency
- external service dependency
- Thread.sleep
- shared mutable state

---

# 5. VALUE OBJECT TESTING RULES

---

# 5.1 Validation Testing

Value Objects MUST validate:
- invalid states
- normalization rules
- immutability
- serialization safety

---

# 5.2 Mandatory Value Object Tests

The following MUST be tested:

- EmailAddress validation
- Username normalization
- PasswordHash protection
- RawPassword restrictions
- Token expiration validation
- JWT structure validation
- TenantIdentifier immutability

---

# 6. INTEGRATION TESTING RULES

---

# 6.1 Purpose

Integration tests validate:
- repository behavior
- persistence integrity
- transactional consistency
- security propagation
- infrastructure integration

---

# 6.2 Official Integration Strategy

Official infrastructure:

```text id="integrationstrategy"
TestContainers + PostgreSQL + Redis
````

---

# 6.3 Mandatory Integration Coverage

The following MUST have integration tests:

* IdentityRepository
* SessionRepository
* PasswordResetRepository
* LoginAttemptRepository
* JWT validation
* Refresh token persistence
* Session revocation
* Tenant filtering

---

# 6.4 Persistence Integrity Rules

Integration tests MUST validate:

* optimistic locking
* tenant filtering
* token uniqueness
* revocation consistency

---

# 6.5 Implemented Integration Tests (CodeCore backend — PASO 10.7)

**Infrastructure:** Testcontainers `postgres:16-alpine` only (no local Docker DB, no H2).

**Base class:** `AbstractPostgresIntegrationTest` — shared container, Flyway `classpath:db/migration`, dynamic R2DBC properties.

**Current scope:**

| Test class | Validates |
|---|---|
| `RegisterIdentityUseCaseIT` | `RegisterIdentityUseCase` → `IdentityRepository` → `R2dbcIdentityRepository` → PostgreSQL |
| `R2dbcIdentityRepositoryIT` | Repository adapter, tenant email uniqueness, round-trip |

**Persistence rules verified (10.7):**

* Flyway: schema `iam`, table `iam.iam_user`, `flyway_schema_history`
* Tenant-scoped email uniqueness (`tenant_id` + `normalized_email`)
* Same email allowed across different tenants
* Registration status `PENDING_VERIFICATION` and `email_verified` projection consistency
* Full round-trip save/load via use case (no repository mocks)

**Not yet implemented (future steps):** SessionRepository IT, JWT, Redis, LoginAttempt, optimistic-lock concurrency IT.

**Reactive standard:** `StepVerifier` — no `.block()` in assertions.

---

# 6.6 HTTP Integration Tests (PASO 10.8)

**Tool:** `WebTestClient` + `SpringBootTest` (random port) + shared `AbstractPostgresIntegrationTest`.

| Test class | Validates |
|---|---|
| `RegisterIdentityControllerIT` | `POST /api/v1/identities` → use case → PostgreSQL row |

**HTTP cases verified:**

* `201 Created` + response body fields
* `409 Conflict` duplicate tenant + email
* `400 Bad Request` missing/blank required fields
* JDBC row exists after successful POST

No repository mocks. No WireMock.

---

# 6.7 Authentication Integration Tests (PASO 10.9)

**Infrastructure:** Testcontainers PostgreSQL + real BCrypt hashes (no repository mocks).

| Test class | Validates |
|---|---|
| `AuthenticateIdentityUseCaseIT` | `AuthenticateIdentityUseCase` → `IdentityRepository` → PostgreSQL |

**Cases verified:**

* ACTIVE identity + correct password → success (`AuthenticationResult`)
* ACTIVE + wrong password → `InvalidCredentialsException`
* Unknown tenant/email → `InvalidCredentialsException`
* `PENDING_VERIFICATION` (via registration) → `IdentityNotAllowedToAuthenticateException`
* `LOCKED`, `DISABLED`, `PASSWORD_RESET_REQUIRED` (persisted rows) → `IdentityNotAllowedToAuthenticateException`

**Unit tests:** `AuthenticateIdentityUseCaseTest` — mocked ports, seven minimum scenarios.

**Not yet:** login HTTP IT, JWT, session store, lockout counter IT.

---

# 7. REACTIVE TESTING RULES

---

# 7.1 Official Reactive Testing Standard

Reactive flows MUST use:

```text id="reactivetesting"
StepVerifier
```

---

# 7.2 Reactive Validation Rules

Reactive tests MUST validate:

* completion signals
* error propagation
* backpressure compatibility
* context propagation

---

# 7.3 Forbidden Reactive Testing Patterns

Forbidden:

* .block()
* imperative waiting
* Thread.sleep
* blocking assertions

---

# 7.4 Context Propagation Testing

Reactive tests MUST validate:

* tenant context propagation
* security context propagation
* correlation ID propagation
* trace ID propagation

---

# 8. SECURITY TESTING RULES

---

# 8.1 Purpose

Security tests validate:

* authentication protection
* credential integrity
* JWT integrity
* replay protection
* brute-force protection

---

# 8.2 Mandatory Security Test Coverage

The following MUST be tested:

* invalid credentials
* locked accounts
* disabled accounts
* expired JWT tokens
* revoked sessions
* replayed refresh tokens
* password policy enforcement
* brute-force lockout triggers

---

# 8.3 Credential Protection Rules

Security tests MUST verify:

* passwords are never exposed
* raw tokens are not logged
* credential serialization is safe

---

# 8.4 Replay Protection Testing

Refresh token reuse MUST trigger:

* security rejection
* session invalidation when required

---

# 9. CONCURRENCY TESTING RULES

---

# 9.1 Purpose

Concurrency tests validate:

* race condition protection
* session consistency
* refresh token integrity
* optimistic locking

---

# 9.2 Mandatory Concurrency Test Coverage

The following MUST be tested:

* concurrent refresh requests
* simultaneous session revocation
* concurrent password changes
* concurrent failed login attempts
* concurrent authentication flows

---

# 9.3 Concurrency Safety Rules

Concurrency tests MUST validate:

* no duplicate active refresh tokens
* no session corruption
* no invalid authentication state

---

# 10. API CONTRACT TESTING RULES

---

# 10.1 Purpose

Contract tests validate:

* API consistency
* serialization correctness
* error contract consistency
* authentication response integrity

---

# 10.2 Mandatory API Contract Coverage

The following MUST be tested:

* login contracts
* refresh contracts
* logout contracts
* password reset contracts
* error response contracts
* validation failure contracts

---

# 10.3 Serialization Safety Rules

Contract tests MUST verify:

* sensitive fields are hidden
* payloads remain minimal
* contract versions remain stable

---

# 11. EVENT TESTING RULES

---

# 11.1 Purpose

Event tests validate:

* immutable event publication
* replay safety
* event ordering
* distributed consistency

---

# 11.2 Mandatory Event Coverage

The following MUST be tested:

* IdentityAuthenticated events
* SessionRevoked events
* PasswordChanged events
* AccountLockoutTriggered events
* PasswordResetRequested events

---

# 11.3 Event Integrity Rules

Event tests MUST validate:

* immutability
* tenant metadata propagation
* traceability metadata
* duplicate tolerance

---

# 12. MULTITENANCY TESTING RULES

---

# 12.1 Purpose

Multitenancy tests validate:

* tenant isolation
* tenant-safe propagation
* tenant-aware persistence

---

# 12.2 Mandatory Multitenancy Coverage

The following MUST be tested:

* cross-tenant authentication rejection
* tenant filtering enforcement
* tenant-aware JWT claims
* tenant-safe event propagation
* tenant-aware session retrieval

---

# 12.3 Isolation Principle

Tests MUST guarantee:

* no cross-tenant leakage
* no shared authentication state

---

# 13. PERFORMANCE TESTING RULES

---

# 13.1 Purpose

Performance tests validate:

* authentication scalability
* token validation latency
* session throughput
* reactive scalability

---

# 13.2 Mandatory Performance Coverage

The following SHOULD be tested:

* login throughput
* JWT validation latency
* refresh token throughput
* session revocation throughput
* concurrent authentication load

---

# 13.3 Scalability Principles

Performance tests SHOULD validate:

* horizontal scalability
* low-latency authentication
* Redis acceleration effectiveness

---

# 14. OBSERVABILITY TESTING RULES

---

# 14.1 Purpose

Observability tests validate:

* traceability
* correlation propagation
* diagnostic integrity

---

# 14.2 Mandatory Observability Coverage

The following MUST be tested:

* correlation ID propagation
* trace ID propagation
* tenant metadata propagation
* audit event generation
* security event generation

---

# 14.3 Diagnostic Integrity Principle

Tests SHOULD ensure:

* authentication failures remain diagnosable
* reactive failures remain traceable

---

# 15. AUDITING TESTING RULES

---

# 15.1 Mandatory Auditability

Critical IAM workflows MUST generate:

* audit records
* security traces
* immutable operational history

---

# 15.2 Mandatory Audit Test Coverage

The following MUST be audited:

* login
* logout
* password changes
* password reset
* failed login
* lockout triggers
* session revocation

---

# 16. FAILURE TESTING RULES

---

# 16.1 Failure Isolation Principle

Failure tests SHOULD validate:

* graceful degradation
* secure failure handling
* retry safety

---

# 16.2 Mandatory Failure Coverage

The following SHOULD be tested:

* Redis unavailability
* PostgreSQL unavailability
* invalid JWT signatures
* token replay attempts
* concurrent revocation failures

---

# 16.3 Secure Failure Principle

Failures MUST:

* deny access safely
* preserve observability
* preserve auditability

---

# 17. TEST DATA RULES

---

# 17.1 Sensitive Data Principle

Test data MUST:

* avoid real credentials
* avoid production secrets
* remain isolated

---

# 17.2 Deterministic Test Data

Test data SHOULD remain:

* reproducible
* immutable where possible

---

# 17.3 Tenant-Aware Test Data

Tenant-aware tests MUST:

* isolate tenant fixtures
* avoid shared tenant state

---

# 18. CI/CD TESTING RULES

---

# 18.1 Mandatory CI Validation

CI pipelines MUST execute:

* unit tests
* integration tests
* reactive tests
* security tests
* contract tests

before deployment.

---

# 18.2 Build Failure Principle

Critical IAM test failures MUST:

* block deployment

---

# 18.3 Parallel Execution Principle

Tests SHOULD support:

* isolated parallel execution

---

# 19. FORBIDDEN TESTING ANTI-PATTERNS

---

# Forbidden

* Blocking reactive tests
* Shared mutable fixtures
* Tenant-blind testing
* Raw credential logging
* Thread.sleep synchronization
* Real production credentials
* Hidden test dependencies
* Non-deterministic authentication tests
* External infrastructure dependency
* Authentication state leakage

---

# 20. AI IMPLEMENTATION RULES

All AI-generated IAM tests MUST:

* remain deterministic
* preserve tenant isolation
* preserve authentication integrity
* validate reactive execution
* avoid blocking execution
* validate concurrency safety
* preserve observability
* preserve auditability
* validate replay protection
* validate secure failure handling

---

# 21. CODECORE IAM TESTING PHILOSOPHY

```text id="testingphilosophy"
IAM testing exists to preserve
authentication integrity,
tenant-safe security consistency
and reactive reliability
through deterministic validation,
distributed traceability
and security-oriented verification.
```

```
```
