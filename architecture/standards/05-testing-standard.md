````md id="9m4xqp"
# 05-testing-standard.md

# 1. Introduction

This document defines the official Testing Standard of the CodeCore platform.

The purpose of this standard is to establish:

- testing philosophy
- testing architecture rules
- testing scope definitions
- testing isolation standards
- reactive testing rules
- event-driven testing standards
- integration testing governance
- test reliability rules

This document follows:

- ADR-001 Reactive-First Architecture
- ADR-002 Event-Driven Architecture
- ADR-003 Multi-Tenant Isolation
- ADR-004 Hexagonal Architecture
- ADR-005 Domain-Driven Design Strategy

---

# 2. Purpose

Testing exists to validate:

```text id="x7m2qp"
architectural correctness
+
business correctness
+
distributed-system correctness
````

---

# Critical Principle

```text id="m4v8wr"
tests
are architectural validation
```

---

# 3. Testing Philosophy

CodeCore testing strategy prioritizes:

| Priority | Focus                      |
| -------- | -------------------------- |
| Highest  | Business correctness       |
| High     | Architectural boundaries   |
| High     | Distributed behavior       |
| Medium   | Infrastructure integration |
| Lower    | UI details                 |

---

# Critical Rule

```text id="u8m1ld"
tests
must validate
behavior
not implementation details
```

---

# 4. Official Testing Pyramid

# Recommended Distribution

| Test Type         | Priority   |
| ----------------- | ---------- |
| Domain Tests      | Highest    |
| Application Tests | High       |
| Integration Tests | High       |
| Contract Tests    | Medium     |
| End-to-End Tests  | Controlled |

---

# Forbidden

```text id="k5m7qp"
overreliance
on end-to-end tests
```

---

# 5. Official Testing Technologies

| Capability          | Official Standard |
| ------------------- | ----------------- |
| Unit Testing        | JUnit 5           |
| Mocking             | Mockito           |
| Reactive Testing    | Reactor Test      |
| Integration Testing | TestContainers    |
| Assertions          | AssertJ           |
| API Testing         | WebTestClient     |

---

# Mandatory Rule

```text id="f2m8ld"
all modules
must support
automated testing
```

---

# 6. Domain Testing Rules

# Priority

```text id="r9m4wr"
highest priority
```

---

# Domain Tests MUST validate

| Capability            | Mandatory |
| --------------------- | --------- |
| Business invariants   | Yes       |
| Aggregate consistency | Yes       |
| State transitions     | Yes       |
| Domain events         | Yes       |
| Business policies     | Yes       |

---

# Forbidden

```text id="u3m1qp"
database dependencies
inside domain tests
```

---

# Forbidden

```text id="m8x4qp"
Spring Boot startup
for aggregate testing
```

---

# Critical Rule

```text id="x1m7wr"
domain tests
must execute
without infrastructure
```

---

# 7. Aggregate Testing Rules

Aggregates MUST be tested for:

* invariant protection
* state transitions
* event emission
* invalid state prevention

---

# Mandatory Tests

| Scenario              | Mandatory |
| --------------------- | --------- |
| Valid transitions     | Yes       |
| Invalid transitions   | Yes       |
| Event emission        | Yes       |
| Tenant-aware behavior | Yes       |

---

# Example

## Correct

```text id="v6m2qp"
Payment cannot be captured twice
```

---

## Incorrect

```text id="u9m4ld"
testing repository implementation
inside aggregate tests
```

---

# 8. Application Layer Testing Rules

Application tests MUST validate:

* orchestration correctness
* port interaction
* workflow coordination
* security propagation
* tenant propagation

---

# Forbidden

```text id="q7m4wr"
testing framework internals
```

---

# Preferred

```text id="m9x2qp"
testing use-case behavior
```

---

# Critical Rule

```text id="f2m7wr"
application tests
validate orchestration
not infrastructure details
```

---

# 9. Reactive Testing Rules

Reactive flows MUST use:

```text id="x5m1ld"
Reactor Test
```

---

# Mandatory Tools

| Tool                 | Mandatory   |
| -------------------- | ----------- |
| StepVerifier         | Yes         |
| VirtualTimeScheduler | Recommended |

---

# Forbidden

```text id="u7m8qp"
Thread.sleep()
inside reactive tests
```

---

# Forbidden

```text id="m6x7wr"
blocking assertions
for reactive pipelines
```

---

# Preferred

```text id="u1m4ld"
deterministic reactive validation
```

---

# Critical Rule

```text id="v8m2qp"
reactive tests
must remain non-blocking
```

---

# 10. Integration Testing Rules

Integration tests MUST validate:

* infrastructure integration
* database integration
* Kafka integration
* Redis integration
* security propagation
* tenant propagation

---

# Official Standard

```text id="q5m8wr"
TestContainers
```

---

# Mandatory Infrastructure Tests

| Capability | Mandatory   |
| ---------- | ----------- |
| PostgreSQL | Yes         |
| Kafka      | Yes         |
| Redis      | Recommended |

---

# Forbidden

```text id="x7m1qp"
shared external testing environments
for automated integration tests
```

---

# Critical Rule

```text id="m2v8ld"
integration tests
must remain reproducible
```

---

# 11. Event-Driven Testing Rules

EDA tests MUST validate:

* event publication
* event consumption
* replay safety
* idempotency
* tenant propagation

---

# Mandatory Validations

| Validation                | Mandatory |
| ------------------------- | --------- |
| Event payload correctness | Yes       |
| Replay tolerance          | Yes       |
| Duplicate handling        | Yes       |
| Correlation propagation   | Yes       |

---

# Forbidden

```text id="u4m7wr"
testing Kafka internals
instead of business behavior
```

---

# Critical Rule

```text id="f8m1ld"
event consumers
must be replay-safe
```

---

# 12. Multi-Tenant Testing Rules

All critical flows MUST validate:

```text id="m6x2qp"
tenant isolation
```

---

# Mandatory Tests

| Capability                 | Mandatory |
| -------------------------- | --------- |
| Tenant-aware queries       | Yes       |
| Tenant-aware events        | Yes       |
| Tenant-aware authorization | Yes       |
| Cross-tenant isolation     | Yes       |

---

# Forbidden

```text id="x1m9wr"
tenant-agnostic integration testing
```

---

# Critical Rule

```text id="p7m4ld"
cross-tenant leakage
must be tested explicitly
```

---

# 13. Security Testing Rules

Security tests MUST validate:

* JWT validation
* authorization boundaries
* tenant authorization
* role enforcement
* permission enforcement

---

# Forbidden

```text id="v5m8qp"
security assumptions
without automated validation
```

---

# Critical Rule

```text id="q3m1wr"
security-sensitive flows
must always be tested
```

---

# 14. Observability Testing Rules

Distributed flows MUST validate:

* correlation propagation
* trace propagation
* structured logging
* observability continuity

---

# Mandatory Validations

| Capability                | Mandatory   |
| ------------------------- | ----------- |
| correlationId propagation | Yes         |
| tenantId propagation      | Yes         |
| traceId propagation       | Recommended |

---

# Forbidden

```text id="k9m7qp"
unobservable distributed workflows
```

---

# 15. API Testing Rules

HTTP APIs MUST use:

```text id="u4m7wr"
WebTestClient
```

---

# Mandatory API Validations

| Validation          | Mandatory |
| ------------------- | --------- |
| Status codes        | Yes       |
| Payload validation  | Yes       |
| Security validation | Yes       |
| Tenant propagation  | Yes       |

---

# Forbidden

```text id="x8m4qp"
manual-only API testing
```

---

# 16. Mocking Rules

Mocks SHOULD remain:

* controlled
* minimal
* behavior-focused

---

# Allowed

| Scenario                | Allowed |
| ----------------------- | ------- |
| External providers      | Yes     |
| Infrastructure adapters | Yes     |
| Messaging ports         | Yes     |

---

# Forbidden

```text id="r6m2ld"
mocking
core business logic
```

---

# Forbidden

```text id="y2m8wr"
over-mocking
distributed workflows
```

---

# Critical Rule

```text id="m1x7qp"
mock infrastructure
not business correctness
```

---

# 17. Test Naming Rules

Tests MUST use:

```text id="u8m4ld"
behavior-oriented naming
```

---

# Correct Examples

```text id="k3m1wr"
shouldRejectExpiredSubscription()
shouldPublishUserRegisteredEvent()
shouldPreventCrossTenantAccess()
```

---

# Incorrect Examples

```text id="x5m8qp"
test1()
validateMethod()
billingServiceTest()
```

---

# 18. Test Structure Rules

Tests SHOULD follow:

```text id="u4m7wr"
Arrange
Act
Assert
```

---

# Preferred Structure

```text id="m9x7qp"
given
when
then
```

---

# Forbidden

```text id="r6m2ld"
massive unreadable tests
```

---

# 19. Testing Anti-Patterns

# Anti-Pattern 1

```text id="x8m4qp"
Slow Everything Tests
```

All validation through E2E tests.

---

# Anti-Pattern 2

```text id="f4m1wr"
Framework-Centric Tests
```

Testing Spring instead of business behavior.

---

# Anti-Pattern 3

```text id="m7x2qp"
Mock Explosion
```

Excessive mocking everywhere.

---

# Anti-Pattern 4

```text id="u3m8wr"
Reactive Blocking Tests
```

Using .block() recklessly.

---

# Anti-Pattern 5

```text id="k5m1ld"
Tenant-Blind Testing
```

Ignoring multi-tenant isolation.

---

# 20. CI/CD Testing Rules

CI pipelines MUST validate:

| Validation               | Mandatory   |
| ------------------------ | ----------- |
| Unit tests               | Yes         |
| Integration tests        | Yes         |
| Architecture consistency | Recommended |
| Security validation      | Recommended |

---

# Critical Rule

```text id="v2m7qp"
broken tests
must block deployments
```

---

# 21. Non-Negotiable Rules

# Rule 1

```text id="x9m4wr"
business rules
must be tested
without infrastructure
```

---

# Rule 2

```text id="q4m8qp"
reactive flows
must be tested
reactively
```

---

# Rule 3

```text id="u1m7wr"
tenant isolation
must be tested explicitly
```

---

# Rule 4

```text id="m6x2qp"
distributed workflows
must remain observable
during tests
```

---

# Rule 5

```text id="r8m1ld"
tests
must validate behavior
not framework internals
```

---

# 22. Final Statement

Testing is considered an architectural governance mechanism of the CodeCore platform.

All tests, pipelines, integration suites, and validation flows MUST preserve:

* business correctness
* architectural correctness
* reactive correctness
* tenant isolation
* replay safety
* distributed observability
* deterministic execution
* scalable maintainability

Testing correctness is considered foundational to the long-term reliability and scalability of CodeCore.

```
```
