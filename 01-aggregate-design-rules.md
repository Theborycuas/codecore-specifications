# Aggregate Design Rules

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Aggregate Design Rules for CodeCore.

Its objectives are:

* preserve domain consistency
* define transactional boundaries
* prevent architectural degradation
* establish ownership rules
* standardize aggregate behavior
* guide AI-assisted development
* avoid anemic and over-coupled models

This specification is mandatory for:

* backend development
* module design
* persistence modeling
* transactional design
* event publication
* AI-generated implementations

---

# 2. AGGREGATE PHILOSOPHY

---

## 2.1 Official Definition

An Aggregate is:

```text id="3w8jdy"
A consistency boundary that protects
domain invariants and lifecycle ownership.
```

---

## 2.2 Aggregate Root Definition

An Aggregate Root is:

```text id="mlc5kh"
The only externally accessible entity
inside an aggregate boundary.
```

The Aggregate Root:

* controls consistency
* controls lifecycle
* protects invariants
* owns transactional boundaries
* coordinates internal entities

---

## 2.3 Core Principle

External modules MUST interact ONLY with Aggregate Roots.

Forbidden:

```text id="7hxh1u"
Direct manipulation of internal entities
outside aggregate boundaries.
```

---

# 3. AGGREGATE DESIGN PRINCIPLES

---

# 3.1 Small Aggregates Principle

Aggregates MUST remain small.

Reasoning:

* lower transactional complexity
* better reactive performance
* lower locking contention
* easier scalability
* easier AI consistency

---

## Forbidden

Oversized aggregates containing:

* unrelated responsibilities
* excessive nested collections
* unrelated lifecycle ownership

---

# 3.2 Single Responsibility Principle

Each Aggregate MUST represent:

```text id="9lyy3u"
One operational consistency boundary.
```

---

## Correct

```text id="7g6ifv"
Appointment Aggregate
→ owns scheduling consistency
```

---

## Forbidden

```text id="l39sdb"
Appointment Aggregate
→ scheduling
→ notifications
→ billing
→ analytics
```

---

# 3.3 Aggregate Ownership Principle

Every internal entity MUST belong to ONE Aggregate Root.

Internal entities MUST NOT be shared between aggregates.

---

## Correct

```text id="i6ut6v"
Appointment
 └── AppointmentParticipant
```

---

## Forbidden

```text id="xqjlwm"
Participant shared between multiple aggregates
with mutable ownership.
```

---

# 3.4 Lifecycle Ownership Principle

The Aggregate Root owns:

* creation
* updates
* state transitions
* deletion rules
* invariant enforcement

---

# 3.5 Invariant Protection Principle

Aggregates exist primarily to protect invariants.

---

## Example

```text id="xvc5jz"
An appointment cannot overlap another
appointment for the same actor.
```

The Aggregate Root MUST enforce this rule.

---

# 3.6 Transaction Boundary Principle

Each Aggregate defines:

* transactional consistency scope
* atomic modification boundaries

---

## Important

Transactions SHOULD NOT span multiple aggregates unless absolutely necessary.

---

# 3.7 Reactive Scalability Principle

Aggregates MUST be optimized for:

* reactive flows
* non-blocking operations
* asynchronous event propagation

---

# 4. AGGREGATE ROOT RULES

---

# 4.1 Aggregate Root Responsibilities

Aggregate Roots are responsible for:

* invariant enforcement
* lifecycle control
* internal entity coordination
* event publication
* consistency protection

---

# 4.2 Aggregate Root Restrictions

Aggregate Roots MUST NOT:

* access infrastructure directly
* access HTTP context
* depend on Spring
* orchestrate unrelated workflows
* manipulate external aggregate internals

---

# 4.3 Aggregate Root Identity

Every Aggregate Root MUST have:

* stable identity
* tenant ownership when applicable
* auditability
* lifecycle tracking

---

## Mandatory Fields

Recommended base structure:

```text id="vjlwmm"
id
tenant_id
created_at
updated_at
created_by
updated_by
```

---

# 4.4 Aggregate Root Mutability

Aggregate Roots SHOULD minimize uncontrolled mutability.

Preferred:

* explicit methods
* validated state transitions
* controlled updates

---

## Forbidden

```text id="b9gqns"
public setters for unrestricted mutation
```

---

# 5. INTERNAL ENTITY RULES

---

# 5.1 Internal Entity Definition

Internal entities:

* exist only inside aggregate boundaries
* cannot live independently
* depend on Aggregate Root lifecycle

---

# 5.2 Internal Entity Access

External modules MUST NOT:

* load
* persist
* mutate

internal entities directly.

---

# 5.3 Internal Entity Identity

Internal entities MAY:

* have local identity
* have no global identity

depending on lifecycle requirements.

---

# 5.4 Internal Entity Persistence

Persistence MUST occur through Aggregate Root operations.

---

# 6. VALUE OBJECT VS ENTITY RULES

---

# 6.1 Entity Criteria

Create an Entity ONLY if:

* identity matters
* lifecycle matters
* independent mutation exists
* traceability is required

---

# 6.2 Value Object Criteria

Use Value Objects when:

* identity does not matter
* immutability is desired
* equality is value-based
* concept represents descriptive information

---

## Examples

Correct Value Objects:

```text id="8rpxy9"
EmailAddress
PhoneNumber
Money
DateRange
Address
```

---

# 6.3 Forbidden Entity Abuse

Forbidden:

* creating entities for simple data holders
* unnecessary persistence tables
* artificial identifiers for descriptive concepts

---

# 7. AGGREGATE COMMUNICATION RULES

---

# 7.1 Communication Principle

Aggregates MUST communicate through:

* identifiers
* events
* contracts
* application services

NOT through direct internal references.

---

# 7.2 Cross Aggregate References

Aggregates SHOULD reference other aggregates ONLY by ID.

---

## Correct

```text id="a3s80y"
appointment.actor_id
```

---

## Forbidden

```text id="a1esjz"
appointment.actor.fullMutableObject
```

---

# 7.3 Aggregate Coordination

Cross-aggregate workflows MUST occur in:

* Application Services
* Orchestration Services

NOT inside Aggregate Roots.

---

# 8. AGGREGATE TRANSACTION RULES

---

# 8.1 Transaction Scope

Transactions SHOULD affect:

* one aggregate at a time

Preferred.

---

# 8.2 Distributed Consistency

Cross-aggregate consistency SHOULD use:

* domain events
* eventual consistency
* workflow coordination

---

# 8.3 Long Transactions

Long-running transactions are forbidden.

---

# 8.4 Reactive Transaction Rules

Reactive transactions MUST:

* remain non-blocking
* avoid imperative blocking APIs
* preserve Reactor Context

---

# 9. EVENT PUBLICATION RULES

---

# 9.1 Aggregate Event Ownership

Aggregate Roots own event publication.

---

# 9.2 Event Timing

Events MUST represent:

* completed state changes

NOT intentions.

---

## Correct

```text id="cjlwmr"
AppointmentScheduled
```

---

## Forbidden

```text id="vk6mxl"
ScheduleAppointment
```

---

# 9.3 Event Immutability

All domain events MUST be immutable.

---

# 9.4 Event Minimalism

Events MUST contain:

* required identifiers
* required contextual metadata

Avoid:

* full entity graphs
* internal implementation leakage

---

# 10. AGGREGATE SIZE RULES

---

# 10.1 Maximum Complexity Principle

Aggregates MUST avoid:

* excessive nested collections
* deep object graphs
* unrelated responsibilities

---

# 10.2 Large Collections Rule

Large collections SHOULD be externalized.

---

## Example

Correct:

```text id="g8smj5"
Appointment
 └── lightweight participants
```

Externalized:

```text id="y7px0v"
AppointmentAuditHistory
AppointmentAnalytics
```

---

# 10.3 Read Model Separation

Heavy read scenarios SHOULD use:

* projections
* read models
* specialized queries

NOT oversized aggregates.

---

# 11. AGGREGATE LIFECYCLE RULES

---

# 11.1 Explicit State Transitions

State transitions MUST be explicit.

---

## Correct

```text id="jqjlwm"
appointment.confirm()
appointment.cancel()
appointment.complete()
```

---

## Forbidden

```text id="e5z18m"
appointment.setStatus(COMPLETED)
```

---

# 11.2 Valid Lifecycle States

Each Aggregate MUST define:

* valid states
* valid transitions
* forbidden transitions

---

# 11.3 Lifecycle Protection

Invalid transitions MUST throw domain exceptions.

---

# 12. MULTITENANCY RULES

---

# 12.1 Tenant Ownership

Tenant-owned aggregates MUST contain:

```text id="zjlwm4"
tenant_id
```

---

# 12.2 Cross Tenant Access

Cross-tenant aggregate access is forbidden.

---

# 12.3 Tenant Isolation Enforcement

Aggregate access MUST validate:

* tenant ownership
* authorization
* visibility rules

---

# 13. AUDITABILITY RULES

---

# 13.1 Aggregate Auditability

Aggregate lifecycle changes MUST be auditable.

---

# 13.2 Audit Metadata

Recommended fields:

```text id="wjlwmx"
created_at
updated_at
created_by
updated_by
```

---

# 13.3 Soft Delete Standard

Preferred deletion strategy:

```text id="8rjlwm"
deleted
deleted_at
deleted_by
```

---

# 14. REACTIVE DESIGN RULES

---

# 14.1 Non Blocking Principle

Aggregates MUST remain compatible with:

* non-blocking execution
* Reactor pipelines
* asynchronous flows

---

# 14.2 Blocking APIs

Blocking APIs inside aggregate workflows are forbidden.

---

# 14.3 Reactive Friendly Design

Aggregate operations SHOULD:

* remain lightweight
* avoid unnecessary loading
* avoid heavy graph traversal

---

# 15. FORBIDDEN AGGREGATE ANTI-PATTERNS

---

# Forbidden

* God Aggregates
* Shared mutable internals
* Bidirectional aggregate ownership
* Aggregate orchestration
* Infrastructure leakage
* HTTP awareness
* Framework coupling
* Mutable public internals
* Cross aggregate transactions by default
* Anemic lifecycle control

---

# 16. CODECORE OFFICIAL AGGREGATE PHILOSOPHY

```text id="4xjlwm"
Aggregates exist to protect consistency,
not to model entire business worlds.
```

---

# 17. AI IMPLEMENTATION RULES

All AI-generated aggregates MUST:

* enforce invariants
* maintain small boundaries
* avoid oversized graphs
* avoid direct aggregate coupling
* preserve lifecycle ownership
* use explicit transitions
* remain reactive-friendly
* avoid framework leakage

---

# 18. IMPLEMENTATION CHECKLIST

Before implementing an Aggregate verify:

* Does it protect a real invariant?
* Is the boundary explicit?
* Is the lifecycle owned correctly?
* Is the aggregate small enough?
* Are transitions explicit?
* Are internal entities protected?
* Is cross aggregate communication safe?
* Is multitenancy enforced?
* Is auditability preserved?
* Is the design reactive-friendly?
* Is orchestration outside the aggregate?
* Is event publication explicit?
* Are responsibilities cohesive?
