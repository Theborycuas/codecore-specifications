````md id="8m4xqp"
# 02-event-design-rules.md

# 1. Introduction

This document defines the official Event Design Rules of the CodeCore platform.

The purpose of this standard is to establish:

- domain event design rules
- event naming standards
- event ownership rules
- event payload rules
- replay safety rules
- idempotency requirements
- event versioning governance
- tenant-aware event propagation

This document follows:

- ADR-002 Event-Driven Architecture
- ADR-003 Multi-Tenant Isolation
- ADR-005 Domain-Driven Design Strategy

---

# 2. Purpose

Events exist to enable:

```text id="x7m2qp"
loosely-coupled
asynchronous
business communication
````

between bounded contexts.

---

# Critical Principle

```text id="m4v8wr"
events
represent business facts
that already happened
```

---

# 3. What Is a Domain Event

A domain event is:

* an immutable business fact
* a historical occurrence
* a business-significant state transition
* a cross-context communication contract

---

# Examples

## Correct

```text id="u8m1ld"
UserRegistered
InvoiceGenerated
PaymentCaptured
SubscriptionExpired
```

---

## Incorrect

```text id="k5m7qp"
CreateUser
ExecutePayment
SendNotification
```

---

# Critical Rule

```text id="f2m8ld"
events
are NOT commands
```

---

# 4. Event Naming Rules

# Official Convention

```text id="r9m4wr"
<Entity><PastTenseVerb>
```

---

# Examples

| Correct          | Incorrect       |
| ---------------- | --------------- |
| UserRegistered   | RegisterUser    |
| PaymentCaptured  | CapturePayment  |
| InvoiceGenerated | GenerateInvoice |

---

# Forbidden

```text id="u3m1qp"
technical implementation names
inside business events
```

---

# Forbidden

```text id="m8x4qp"
framework-oriented event names
```

---

# 5. Event Ownership Rules

Every event MUST have:

```text id="x1m7wr"
a single bounded context owner
```

---

# Examples

| Event               | Owner        |
| ------------------- | ------------ |
| UserRegistered      | IAM          |
| InvoiceGenerated    | Billing      |
| PaymentCaptured     | Payment      |
| SubscriptionExpired | Subscription |

---

# Forbidden

```text id="v6m2qp"
shared event ownership
```

---

# Critical Rule

```text id="u9m4ld"
the context that owns
the business invariant
owns the event
```

---

# 6. Event Payload Rules

Event payloads MUST remain:

| Characteristic | Mandatory |
| -------------- | --------- |
| Immutable      | Yes       |
| Serializable   | Yes       |
| Stable         | Yes       |
| Tenant-aware   | Yes       |
| Replay-safe    | Yes       |

---

# Event Payloads SHOULD

* contain business meaning
* remain lightweight
* avoid infrastructure leakage
* avoid provider-specific concepts

---

# Forbidden

```text id="q7m4wr"
ORM entities
inside events
```

---

# Forbidden

```text id="m9x2qp"
framework objects
inside event payloads
```

---

# 7. Mandatory Event Metadata

All events MUST contain:

| Field         | Mandatory |
| ------------- | --------- |
| eventId       | Yes       |
| eventType     | Yes       |
| occurredAt    | Yes       |
| tenantId      | Yes       |
| correlationId | Yes       |
| aggregateId   | Yes       |
| schemaVersion | Yes       |

---

# Recommended Metadata

| Field       | Recommended |
| ----------- | ----------- |
| causationId | Yes         |
| traceId     | Yes         |
| actorId     | Recommended |

---

# Critical Rule

```text id="f2m7wr"
all distributed events
must remain traceable
```

---

# 8. Event Immutability Rules

Published events MUST NEVER change.

---

# Forbidden

```text id="x5m1ld"
mutating events
after publication
```

---

# Critical Rule

```text id="u7m8qp"
published events
are historical facts
```

---

# 9. Event Replay Rules

All consumers MUST tolerate:

* duplicate delivery
* retries
* replay execution
* delayed delivery

---

# Critical Rule

```text id="m6x7wr"
events
may be delivered
multiple times
```

---

# Mandatory Consumer Behavior

| Capability          | Mandatory |
| ------------------- | --------- |
| Idempotency         | Yes       |
| Duplicate tolerance | Yes       |
| Retry tolerance     | Yes       |

---

# 10. Idempotency Rules

Critical event consumers MUST support:

```text id="u1m4ld"
idempotent processing
```

---

# Mandatory Domains

| Domain       | Mandatory |
| ------------ | --------- |
| Payments     | Yes       |
| Billing      | Yes       |
| Subscription | Yes       |
| Webhooks     | Yes       |

---

# Forbidden

```text id="v8m2qp"
non-idempotent
financial event processing
```

---

# 11. Event Versioning Rules

Public events MUST support:

```text id="q5m8wr"
schema versioning
```

---

# Mandatory Rules

| Rule                    | Mandatory |
| ----------------------- | --------- |
| Explicit schema version | Yes       |
| Backward compatibility  | Yes       |
| Consumer compatibility  | Yes       |

---

# Examples

```text id="x7m1qp"
UserRegistered:v1
UserRegistered:v2
```

---

# Forbidden

```text id="m2v8ld"
breaking event schemas
without versioning
```

---

# 12. Event Size Rules

Events SHOULD remain:

| Principle   | Recommended |
| ----------- | ----------- |
| Lightweight | Yes         |
| Focused     | Yes         |
| Minimal     | Yes         |

---

# Forbidden

```text id="u4m7wr"
gigantic event payloads
```

---

# Forbidden

```text id="f8m1ld"
entire aggregate serialization
inside events
```

---

# Preferred

```text id="m6x2qp"
business-significant payloads
```

---

# 13. Event Consistency Rules

Events MUST be emitted ONLY after:

```text id="x1m9wr"
successful state transitions
```

---

# Forbidden

```text id="p7m4ld"
publishing events
before transaction success
```

---

# Critical Rule

```text id="v5m8qp"
events
must reflect
committed business state
```

---

# 14. Event Classification Rules

# Official Event Types

| Event Type          | Purpose                     |
| ------------------- | --------------------------- |
| Domain Event        | Business fact               |
| Integration Event   | Cross-context propagation   |
| Security Event      | Security-related occurrence |
| Audit Event         | Compliance trace            |
| Observability Event | Telemetry signal            |

---

# Critical Rule

```text id="q3m1wr"
business events
must remain separated
from technical telemetry
```

---

# 15. Tenant-Aware Rules

All business events MUST remain:

```text id="k9m7qp"
tenant-aware
```

---

# Mandatory Rules

| Rule                      | Mandatory |
| ------------------------- | --------- |
| tenantId propagation      | Yes       |
| Tenant-safe replay        | Yes       |
| Tenant-safe observability | Yes       |

---

# Forbidden

```text id="u4m7wr"
cross-tenant event leakage
```

---

# 16. Reactive Compatibility Rules

Events MUST support:

* reactive processing
* async orchestration
* non-blocking consumers

---

# Preferred Technologies

| Capability | Preferred     |
| ---------- | ------------- |
| Messaging  | Kafka         |
| Consumers  | Reactor Kafka |
| Pipelines  | Mono/Flux     |

---

# Forbidden

```text id="x8m4qp"
blocking event consumers
inside reactive flows
```

---

# 17. Event Security Rules

Sensitive events MUST avoid:

* secret leakage
* credential exposure
* token propagation
* provider secrets

---

# Forbidden

```text id="r6m2ld"
passwords
inside events
```

---

# Forbidden

```text id="y2m8wr"
JWT tokens
inside event payloads
```

---

# Critical Rule

```text id="m1x7qp"
events
must remain security-safe
```

---

# 18. Event Topic Naming Rules

Kafka topics MUST follow:

```text id="u8m4ld"
<context>.<aggregate>.<event>
```

---

# Examples

```text id="k3m1wr"
iam.user.registered
billing.invoice.generated
payment.transaction.captured
```

---

# Forbidden

```text id="x5m8qp"
generic event topics
```

---

# Forbidden

```text id="u4m7wr"
shared multi-purpose topics
```

---

# 19. Event Ordering Rules

Ordering MUST exist ONLY where business consistency requires it.

---

# Ordering Required

| Domain                 | Mandatory |
| ---------------------- | --------- |
| Payments               | Yes       |
| Billing                | Yes       |
| Subscription lifecycle | Yes       |

---

# Ordering Optional

| Domain        | Optional |
| ------------- | -------- |
| Notifications | Yes      |
| Observability | Yes      |
| Telemetry     | Yes      |

---

# Critical Rule

```text id="m9x7qp"
ordering guarantees
must be intentional
```

---

# 20. Event Anti-Patterns

# Anti-Pattern 1

```text id="r6m2ld"
Command Events
```

Events pretending to be commands.

---

# Anti-Pattern 2

```text id="x8m4qp"
Technical Framework Events
```

Framework internals exposed as business contracts.

---

# Anti-Pattern 3

```text id="f4m1wr"
Massive Payload Events
```

Entire aggregates serialized unnecessarily.

---

# Anti-Pattern 4

```text id="m7x2qp"
Shared Event Ownership
```

Multiple contexts controlling one event.

---

# Anti-Pattern 5

```text id="u3m8wr"
Infrastructure-Leaking Events
```

Provider SDK concepts inside event payloads.

---

# 21. Recommended Event Design Flow

# Step 1

Identify:

```text id="k5m1ld"
business fact
```

---

# Step 2

Validate:

```text id="v2m7qp"
bounded context ownership
```

---

# Step 3

Define:

```text id="x9m4wr"
minimal meaningful payload
```

---

# Step 4

Add:

```text id="q4m8qp"
mandatory metadata
```

---

# Step 5

Validate:

```text id="u1m7wr"
replay safety
and idempotency
```

---

# 22. Non-Negotiable Rules

# Rule 1

```text id="m6x2qp"
events
must represent
business facts
```

---

# Rule 2

```text id="r8m1ld"
events
must remain immutable
```

---

# Rule 3

```text id="x3m7qp"
all critical consumers
must be idempotent
```

---

# Rule 4

```text id="y7m1ld"
events
must remain tenant-aware
```

---

# Rule 5

```text id="p4m8qp"
events
must remain replay-safe
```

---

# 23. Final Statement

Events are one of the foundational integration mechanisms of the CodeCore platform.

All events MUST preserve:

* business meaning
* immutability
* replay safety
* tenant isolation
* explicit ownership
* schema stability
* reactive compatibility
* distributed traceability

Event design correctness is considered foundational to the scalability, resilience, and maintainability of CodeCore.

```
```
