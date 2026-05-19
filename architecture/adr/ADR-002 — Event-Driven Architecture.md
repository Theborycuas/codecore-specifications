````md id="4x8qpm"
# ADR-002 — Event-Driven Architecture

# Status

```text
ACCEPTED
````

---

# Date

```text id="n5m2wr"
2026-05-19
```

---

# Decision Makers

* Platform Architecture Team
* CodeCore Core Engineering

---

# Context

CodeCore is being designed as:

* a distributed enterprise SaaS platform
* a multi-tenant ecosystem
* a modular bounded-context architecture
* a reactive-first platform
* a highly scalable integration-oriented system

The platform requires:

* low coupling
* independent module evolution
* asynchronous orchestration
* scalable communication
* provider integration flexibility
* distributed observability
* fault-tolerant workflows
* future multi-region scalability

Traditional tightly-coupled synchronous architectures introduce:

* cascading failures
* fragile dependency chains
* scalability bottlenecks
* high operational coupling
* poor modularity
* deployment rigidity

The platform requires:

```text id="v7m1ld"
asynchronous
loosely-coupled
event-oriented
communication
```

between bounded contexts.

---

# Decision

CodeCore officially adopts:

```text id="x3m8qp"
Event-Driven Architecture (EDA)
```

as a foundational architectural principle.

Bounded contexts MUST communicate primarily through:

* domain events
* integration events
* asynchronous messaging
* replay-safe event propagation

The platform standardizes on:

| Capability          | Standard                |
| ------------------- | ----------------------- |
| Messaging Platform  | Kafka                   |
| Event Model         | Immutable domain events |
| Async Integration   | Event-driven            |
| Event Processing    | Reactive consumers      |
| Event Serialization | Versioned schemas       |
| Event Correlation   | Correlation IDs         |

---

# Strategic Principles

# 1. Loose Coupling

Bounded contexts MUST remain loosely coupled.

---

## Preferred Communication

| Style              | Preferred |
| ------------------ | --------- |
| Domain events      | Yes       |
| Async messaging    | Yes       |
| Explicit contracts | Yes       |

---

## Discouraged

| Style                   | Reason               |
| ----------------------- | -------------------- |
| Deep synchronous chains | Fragility            |
| Shared persistence      | Tight coupling       |
| Cross-context mutations | Ownership corruption |

---

## Critical Rule

```text id="f8m4wr"
bounded contexts
communicate through contracts
not implementations
```

---

# 2. Events Represent Business Facts

Events MUST represent:

```text id="m2x7qp"
facts
that already happened
```

---

## Correct Examples

```text id="q5m1ld"
UserRegistered
PaymentCaptured
InvoiceGenerated
SubscriptionExpired
```

---

## Forbidden

```text id="y9m8qp"
CreateUser
ExecutePayment
SendNotification
```

---

## Critical Rule

```text id="u4x2wr"
events
are not commands
```

---

# 3. Async-First Communication

The platform officially adopts:

```text id="r7m1ld"
async-first orchestration
```

---

## Preferred Integration Style

| Pattern              | Preferred |
| -------------------- | --------- |
| Event propagation    | Yes       |
| Event consumption    | Yes       |
| Eventual consistency | Yes       |

---

## Restricted

| Pattern                     | Reason           |
| --------------------------- | ---------------- |
| Sync-heavy orchestration    | Fragility        |
| Distributed transactions    | Complexity       |
| Blocking integration chains | Scalability risk |

---

# 4. Eventual Consistency

Cross-context consistency MUST rely on:

```text id="x8m4qp"
eventual consistency
```

---

## Forbidden

```text id="f3x7wr"
distributed ACID transactions
between bounded contexts
```

---

## Critical Rule

```text id="m5v1ld"
aggregates
define transaction boundaries
```

---

# 5. Independent Evolution

EDA is adopted to support:

* independent deployments
* bounded context autonomy
* module scalability
* isolated evolution
* organizational scalability

---

# Messaging Technology Decision

# Official Messaging Platform

```text id="n7m8qp"
Apache Kafka
```

---

# Why Kafka

Kafka was selected because it provides:

* high throughput
* replay support
* partition scalability
* durable event storage
* event ordering support
* distributed scalability
* ecosystem maturity

---

# Strategic Kafka Usage

Kafka will support:

* domain event propagation
* integration events
* observability events
* audit propagation
* retry workflows
* dead-letter handling

---

# Event Classification

# Official Event Types

| Event Type          | Description               |
| ------------------- | ------------------------- |
| Domain Event        | Business fact             |
| Integration Event   | Cross-context propagation |
| Security Event      | Security-related event    |
| Audit Event         | Compliance trace          |
| Observability Event | Telemetry event           |

---

# Domain Events

## Characteristics

| Characteristic      | Mandatory |
| ------------------- | --------- |
| Immutable           | Yes       |
| Replay-safe         | Yes       |
| Tenant-aware        | Yes       |
| Correlation-aware   | Yes       |
| Business meaningful | Yes       |

---

## Examples

```text id="k2x1wr"
UserRegistered
InvoiceGenerated
PaymentCaptured
```

---

# Integration Events

## Purpose

Integration events enable:

* cross-context synchronization
* eventual consistency
* external orchestration

---

## Critical Rule

```text id="u9m4ld"
integration events
must remain stable contracts
```

---

# Event Schema Rules

All events MUST contain:

| Field         | Mandatory |
| ------------- | --------- |
| eventId       | Yes       |
| eventType     | Yes       |
| occurredAt    | Yes       |
| correlationId | Yes       |
| tenantId      | Yes       |
| aggregateId   | Yes       |
| schemaVersion | Yes       |

---

# Event Naming Convention

# Mandatory Convention

```text id="v4x8qp"
<Entity><PastTenseVerb>
```

---

## Examples

```text id="m1x7wr"
UserRegistered
TenantCreated
PaymentFailed
RefundProcessed
```

---

## Forbidden

```text id="f7m2ld"
CreateTenant
ProcessPayment
RunBilling
```

---

# Event Immutability Rules

Events MUST be immutable after publication.

---

## Forbidden

```text id="y5m8qp"
event mutation
after publishing
```

---

## Critical Rule

```text id="q3x1wr"
published events
are historical facts
```

---

# Replay Safety Rules

Consumers MUST tolerate:

* duplicate delivery
* retries
* replay execution
* out-of-order delivery where applicable

---

## Critical Rule

```text id="u6m4ld"
events
may be delivered
multiple times
```

---

# Idempotency Rules

All critical consumers MUST support:

```text id="p9m8qp"
idempotent processing
```

---

## Includes

* payment consumers
* billing consumers
* subscription consumers
* webhook processors

---

# Forbidden

```text id="k4x2wr"
non-idempotent
financial event processing
```

---

# Dead Letter Queue (DLQ) Strategy

Critical event flows MUST support:

| Capability             | Mandatory |
| ---------------------- | --------- |
| DLQ routing            | Yes       |
| Retry orchestration    | Yes       |
| Poison event isolation | Yes       |
| Replay support         | Yes       |

---

# Critical Rule

```text id="x7m1ld"
failed events
must remain observable
```

---

# Event Ordering Rules

Event ordering is REQUIRED only where business consistency depends on ordering.

---

## Ordering Required

| Domain                 | Required |
| ---------------------- | -------- |
| Payments               | Yes      |
| Billing                | Yes      |
| Subscription lifecycle | Yes      |

---

## Ordering Not Strictly Required

| Domain        | Optional |
| ------------- | -------- |
| Notifications | Yes      |
| Observability | Yes      |
| Telemetry     | Yes      |

---

# Topic Governance Rules

Kafka topics MUST follow:

```text id="v8m4qp"
bounded context ownership
```

---

# Topic Naming Convention

```text id="q2x7wr"
<context>.<aggregate>.<event>
```

---

## Examples

```text id="n5m1ld"
iam.user.registered
billing.invoice.generated
payment.transaction.captured
```

---

# Topic Ownership Rules

Each topic MUST have:

| Requirement        | Mandatory |
| ------------------ | --------- |
| Single owner       | Yes       |
| Schema governance  | Yes       |
| Version governance | Yes       |
| Tenant-awareness   | Yes       |

---

# Forbidden

```text id="m4x8qp"
shared ownership
of event topics
```

---

# Event Versioning Strategy

All public events MUST support schema versioning.

---

# Mandatory Rules

| Rule                   | Mandatory |
| ---------------------- | --------- |
| Backward compatibility | Yes       |
| Explicit versioning    | Yes       |
| Consumer compatibility | Yes       |

---

## Examples

```text id="x1m7wr"
UserRegistered:v1
UserRegistered:v2
```

---

# Security Rules

Security-sensitive events MUST support:

* correlation tracking
* auditability
* replay protection
* tenant isolation
* traceability

---

# Forbidden

```text id="k8m2ld"
sensitive payload leakage
inside events
```

---

# Multi-Tenant Rules

All events MUST remain tenant-aware.

---

# Mandatory Rules

| Rule                       | Mandatory |
| -------------------------- | --------- |
| tenantId propagation       | Yes       |
| Cross-tenant isolation     | Yes       |
| Tenant-aware replay        | Yes       |
| Tenant-aware observability | Yes       |

---

## Critical Rule

```text id="y7m4qp"
tenant boundaries
must survive
event propagation
```

---

# Reactive Architecture Compatibility

EDA officially complements:

```text id="r5x1wr"
Reactive-First Architecture
```

---

# Strategic Alignment

EDA supports:

* reactive pipelines
* async orchestration
* scalable messaging
* resilient integrations
* distributed systems evolution

---

# Hexagonal Architecture Compatibility

Event-driven architecture MUST preserve:

* bounded context isolation
* explicit ownership
* infrastructure abstraction
* domain integrity

---

# Critical Rule

```text id="u3m8ld"
events
must not bypass
domain boundaries
```

---

# Non-Negotiable Rules

# Rule 1

```text id="f9m2qp"
shared mutable databases
between contexts
are forbidden
```

---

# Rule 2

```text id="n1x7wr"
cross-context aggregate mutation
is forbidden
```

---

# Rule 3

```text id="m6v4ld"
events
must remain immutable
```

---

# Rule 4

```text id="x2m8qp"
all critical consumers
must be idempotent
```

---

# Rule 5

```text id="q7m1wr"
all distributed event flows
must remain observable
```

---

# Consequences

# Positive Consequences

| Benefit                | Impact                     |
| ---------------------- | -------------------------- |
| Loose coupling         | Independent evolution      |
| Better scalability     | Distributed growth         |
| Async orchestration    | Higher resilience          |
| Better fault isolation | Reduced cascading failures |
| Replay support         | Operational recovery       |

---

# Negative Consequences

| Trade-Off                        | Impact                    |
| -------------------------------- | ------------------------- |
| Increased operational complexity | Kafka operations          |
| Eventual consistency complexity  | Async workflows           |
| Replay complexity                | Consumer design           |
| Debugging complexity             | Distributed tracing needs |

---

# Risks

| Risk               | Mitigation               |
| ------------------ | ------------------------ |
| Event chaos        | Event Catalog governance |
| Schema drift       | Version governance       |
| Consumer fragility | Idempotency standards    |
| Hidden coupling    | Dependency governance    |
| Event storms       | Topic governance         |

---

# Alternatives Considered

# Alternative 1 — Synchronous REST-Centric Architecture

## Rejected Because

* tight coupling
* cascading failures
* scalability bottlenecks
* poor async orchestration

---

# Alternative 2 — Shared Database Integration

## Rejected Because

* ownership corruption
* DDD violations
* hidden dependencies
* poor scalability

---

# Alternative 3 — Hybrid Sync-Heavy Architecture

## Rejected Because

* inconsistent orchestration
* operational fragility
* unpredictable scalability

---

# Architectural Constraints

EDA is considered:

```text id="v4m9qp"
a foundational
architectural decision
```

Changing this decision later would require:

* messaging redesign
* integration redesign
* observability redesign
* workflow redesign
* module redesign

---

# Related ADRs

| ADR     | Relationship                  |
| ------- | ----------------------------- |
| ADR-001 | Reactive-First Architecture   |
| ADR-003 | Multi-Tenant Isolation        |
| ADR-004 | Hexagonal Architecture        |
| ADR-018 | Transaction Boundary Strategy |

---

# Final Statement

CodeCore officially adopts:

```text id="p6x1wr"
Event-Driven Architecture
```

as a foundational enterprise architectural principle.

All future modules, integrations, workflows, messaging systems, and distributed operations MUST prioritize:

* asynchronous communication
* immutable events
* bounded context isolation
* replay safety
* idempotent processing
* observable distributed flows
* tenant-aware propagation

Event-Driven Architecture is considered a strategic scalability capability of the CodeCore platform.

```
```
