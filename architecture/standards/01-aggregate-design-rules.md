````md id="6m8xqp"
# 01-aggregate-design-rules.md

# 1. Introduction

This document defines the official Aggregate Design Rules of the CodeCore platform.

The purpose of this standard is to establish:

- aggregate consistency rules
- transaction boundary rules
- invariant protection rules
- aggregate sizing guidelines
- aggregate communication rules
- event emission standards
- aggregate ownership governance

This document follows:

- ADR-005 Domain-Driven Design Strategy
- ADR-004 Hexagonal Architecture
- ADR-002 Event-Driven Architecture
- ADR-003 Multi-Tenant Isolation

---

# 2. Purpose

Aggregates exist to enforce:

```text id="x7m2qp"
business consistency
````

inside explicit transactional boundaries.

---

# Critical Principle

```text id="m4v8wr"
aggregates
protect invariants
not database structure
```

---

# 3. What Is an Aggregate

An aggregate is:

* a transactional consistency boundary
* a cluster of business rules
* an invariant protection mechanism
* a domain ownership boundary

---

# Aggregate Responsibilities

| Responsibility              | Mandatory |
| --------------------------- | --------- |
| Invariant protection        | Yes       |
| Transaction consistency     | Yes       |
| Domain event emission       | Yes       |
| State transition validation | Yes       |

---

# Aggregates Are NOT

| Incorrect Interpretation | Reason                       |
| ------------------------ | ---------------------------- |
| Database tables          | Persistence-centric thinking |
| DTO containers           | Anemic design                |
| CRUD wrappers            | Weak domain modeling         |
| Service replacements     | Ownership confusion          |

---

# 4. Aggregate Root Rules

Every aggregate MUST have:

```text id="u8m1ld"
a single aggregate root
```

---

# Aggregate Root Responsibilities

| Responsibility             | Mandatory |
| -------------------------- | --------- |
| State mutation entry point | Yes       |
| Invariant enforcement      | Yes       |
| Child entity protection    | Yes       |
| Domain event emission      | Yes       |

---

# Forbidden

```text id="k5m7qp"
modifying child entities
outside aggregate root control
```

---

# Example

## Correct

```text id="f2m8ld"
InvoiceAggregate
    └── InvoiceLine
```

All modifications occur through:

```text id="r9m4wr"
InvoiceAggregate
```

---

## Incorrect

```text id="u3m1qp"
InvoiceLineRepository
```

outside aggregate control.

---

# 5. Aggregate Size Rules

Aggregates MUST remain:

| Principle     | Mandatory |
| ------------- | --------- |
| Small         | Yes       |
| Focused       | Yes       |
| Cohesive      | Yes       |
| Transactional | Yes       |

---

# Forbidden

```text id="m8x4qp"
god aggregates
```

---

# Aggregate Warning Signs

| Smell                  | Risk                 |
| ---------------------- | -------------------- |
| Too many entities      | Complexity           |
| Massive object graphs  | Performance issues   |
| Cross-domain ownership | Coupling             |
| Excessive writes       | Scalability problems |

---

# Critical Rule

```text id="x1m7wr"
large aggregates
become scalability bottlenecks
```

---

# 6. Aggregate Transaction Rules

Aggregates define:

```text id="v6m2qp"
transaction boundaries
```

---

# Mandatory Rule

One transaction SHOULD modify:

| Scope         | Recommended |
| ------------- | ----------- |
| One aggregate | Yes         |

---

# Forbidden

```text id="u9m4ld"
distributed transactions
across aggregates
```

---

# Critical Rule

```text id="q7m4wr"
cross-aggregate consistency
must use eventual consistency
```

---

# 7. Aggregate Communication Rules

Aggregates MUST communicate through:

| Mechanism        | Preferred  |
| ---------------- | ---------- |
| Domain events    | Yes        |
| Explicit queries | Controlled |

---

# Forbidden

```text id="m9x2qp"
direct aggregate mutation
across bounded contexts
```

---

# Forbidden

```text id="f2m7wr"
shared aggregate persistence
```

---

# 8. Aggregate Identity Rules

Every aggregate MUST have:

```text id="x5m1ld"
stable identity
```

---

# Identity Characteristics

| Characteristic | Mandatory |
| -------------- | --------- |
| Immutable      | Yes       |
| Unique         | Yes       |
| Traceable      | Yes       |

---

# Examples

| Aggregate | Identifier |
| --------- | ---------- |
| User      | UserId     |
| Invoice   | InvoiceId  |
| Payment   | PaymentId  |

---

# Forbidden

```text id="u7m8qp"
business logic
depending on database-generated state
```

---

# 9. Aggregate Reference Rules

Aggregates SHOULD reference other aggregates ONLY by ID.

---

# Correct

```text id="m6x7wr"
invoice.customerId
```

---

# Incorrect

```text id="u1m4ld"
invoice.customer
```

when customer belongs to another aggregate boundary.

---

# Critical Rule

```text id="v8m2qp"
aggregate references
must remain lightweight
```

---

# 10. Aggregate Invariant Rules

Aggregates MUST protect:

* business consistency
* state validity
* transactional correctness

---

# Examples

| Aggregate    | Invariant                                      |
| ------------ | ---------------------------------------------- |
| Invoice      | Total must match lines                         |
| Subscription | Expired subscriptions cannot activate features |
| Payment      | Captured payments cannot be recaptured         |

---

# Critical Rule

```text id="q5m8wr"
invalid states
must be impossible
inside aggregates
```

---

# 11. Aggregate Lifecycle Rules

Aggregates MUST control:

| Lifecycle Capability | Mandatory   |
| -------------------- | ----------- |
| Creation             | Yes         |
| Mutation             | Yes         |
| State transitions    | Yes         |
| Deactivation         | Recommended |

---

# Forbidden

```text id="x7m1qp"
external lifecycle mutation
bypassing aggregate rules
```

---

# 12. Aggregate Event Rules

Aggregates MAY emit:

```text id="m2v8ld"
domain events
```

after successful state transitions.

---

# Correct Examples

```text id="u4m7wr"
InvoiceGenerated
PaymentCaptured
SubscriptionExpired
```

---

# Forbidden

```text id="f8m1ld"
technical framework events
inside aggregates
```

---

# Critical Rule

```text id="m6x2qp"
events
must represent
business facts
```

---

# 13. Aggregate Persistence Rules

Persistence MUST remain:

* infrastructure concern
* repository-driven
* aggregate-oriented

---

# Forbidden

```text id="x1m9wr"
ORM annotations
inside pure domain models
```

when avoidable.

---

# Forbidden

```text id="p7m4ld"
cross-context repository access
```

---

# Preferred

```text id="v5m8qp"
repository ports
```

inside the application/domain boundary.

---

# 14. Aggregate Consistency Rules

Aggregates MUST guarantee:

| Consistency Type          | Mandatory |
| ------------------------- | --------- |
| Internal consistency      | Yes       |
| Transactional consistency | Yes       |
| Invariant consistency     | Yes       |

---

# Cross-Aggregate Consistency

Cross-aggregate consistency SHOULD use:

```text id="q3m1wr"
eventual consistency
```

---

# Forbidden

```text id="k9m7qp"
synchronous aggregate orchestration
inside transactions
```

---

# 15. Multi-Tenant Rules

Aggregates MUST remain:

```text id="u4m7wr"
tenant-aware
```

---

# Mandatory Rules

| Rule                     | Mandatory |
| ------------------------ | --------- |
| tenantId awareness       | Yes       |
| tenant-safe events       | Yes       |
| tenant-safe repositories | Yes       |

---

# Forbidden

```text id="x8m4qp"
cross-tenant aggregate access
```

---

# 16. Reactive Rules

Aggregates MUST remain:

* framework-agnostic
* reactive-compatible
* infrastructure-independent

---

# Forbidden

```text id="r6m2ld"
blocking infrastructure logic
inside aggregates
```

---

# Critical Rule

```text id="y2m8wr"
aggregates
must remain pure business models
```

---

# 17. Aggregate Service Rules

Business logic SHOULD live:

| Location            | Preferred         |
| ------------------- | ----------------- |
| Aggregate           | Yes               |
| Domain Service      | Only if necessary |
| Application Service | No invariants     |

---

# Forbidden

```text id="m1x7qp"
anemic aggregates
with all logic in services
```

---

# 18. Aggregate Boundary Rules

Boundaries MUST align with:

* business ownership
* transactional consistency
* invariant protection

---

# Forbidden

```text id="u8m4ld"
aggregate boundaries
defined by database convenience
```

---

# Examples

## Correct

| Aggregate    | Ownership    |
| ------------ | ------------ |
| Invoice      | Billing      |
| Payment      | Payment      |
| Subscription | Subscription |

---

## Incorrect

```text id="k3m1wr"
MegaBusinessAggregate
```

owning unrelated capabilities.

---

# 19. Aggregate Performance Rules

Aggregates SHOULD minimize:

* unnecessary loading
* deep object graphs
* excessive transactional scope

---

# Recommended

| Pattern                   | Recommended |
| ------------------------- | ----------- |
| Lightweight references    | Yes         |
| Event-driven coordination | Yes         |
| Focused invariants        | Yes         |

---

# Forbidden

```text id="x5m8qp"
loading entire business domains
inside one aggregate
```

---

# 20. Aggregate Anti-Patterns

# Anti-Pattern 1

```text id="u4m7wr"
God Aggregate
```

One aggregate owning too much logic.

---

# Anti-Pattern 2

```text id="m9x7qp"
Anemic Aggregate
```

No business rules inside the aggregate.

---

# Anti-Pattern 3

```text id="r6m2ld"
Database-Driven Aggregate
```

Modeled from tables instead of business rules.

---

# Anti-Pattern 4

```text id="x8m4qp"
Cross-Context Aggregate Mutation
```

One context mutating another context's aggregate.

---

# Anti-Pattern 5

```text id="f4m1wr"
Infrastructure-Aware Aggregate
```

Framework or persistence leakage into domain logic.

---

# 21. Non-Negotiable Rules

# Rule 1

```text id="m7x2qp"
aggregates
define transaction boundaries
```

---

# Rule 2

```text id="u3m8wr"
aggregates
must protect invariants
```

---

# Rule 3

```text id="k5m1ld"
cross-aggregate transactions
are forbidden
```

---

# Rule 4

```text id="v2m7qp"
aggregate references
must remain lightweight
```

---

# Rule 5

```text id="x9m4wr"
aggregates
must remain business-centric
```

---

# 22. Recommended Aggregate Design Flow

# Step 1

Identify:

```text id="q4m8qp"
business invariants
```

---

# Step 2

Identify:

```text id="u1m7wr"
transaction boundaries
```

---

# Step 3

Define:

```text id="m6x2qp"
aggregate root
```

---

# Step 4

Define:

```text id="r8m1ld"
domain events
```

---

# Step 5

Validate:

```text id="x3m7qp"
aggregate size
and cohesion
```

---

# 23. Final Statement

Aggregates are one of the most critical tactical patterns of the CodeCore platform.

All aggregates MUST preserve:

* invariant protection
* explicit transaction boundaries
* business ownership
* tenant isolation
* event-driven consistency
* infrastructure independence
* bounded context integrity

Aggregate design correctness is considered foundational to the long-term scalability and maintainability of CodeCore.

```
```
