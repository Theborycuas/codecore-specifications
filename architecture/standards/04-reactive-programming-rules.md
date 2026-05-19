````md id="7m4xqp"
# 04-reactive-programming-rules.md

# 1. Introduction

This document defines the official Reactive Programming Rules of the CodeCore platform.

The purpose of this standard is to establish:

- reactive programming governance
- Reactor usage rules
- non-blocking execution rules
- reactive orchestration standards
- reactive error handling rules
- reactive context propagation
- reactive scalability standards
- reactive anti-pattern prevention

This document follows:

- ADR-001 Reactive-First Architecture
- ADR-002 Event-Driven Architecture
- ADR-003 Multi-Tenant Isolation
- ADR-004 Hexagonal Architecture

---

# 2. Purpose

Reactive programming exists to provide:

```text id="x7m2qp"
non-blocking
scalable
resource-efficient
distributed execution
````

across the entire CodeCore platform.

---

# Critical Principle

```text id="m4v8wr"
reactive programming
is NOT
just async programming
```

---

# 3. Official Reactive Stack

# Mandatory Technologies

| Capability      | Official Standard |
| --------------- | ----------------- |
| Reactive Engine | Project Reactor   |
| HTTP            | Spring WebFlux    |
| Messaging       | Reactor Kafka     |
| Persistence     | R2DBC             |
| Cache           | Reactive Redis    |

---

# Official Reactive Types

| Type    | Purpose                |
| ------- | ---------------------- |
| Mono<T> | Single async result    |
| Flux<T> | Multiple async results |

---

# Forbidden

```text id="u8m1ld"
CompletableFuture
inside core reactive flows
```

unless explicitly justified.

---

# 4. Non-Blocking Rules

All reactive flows MUST remain:

```text id="k5m7qp"
non-blocking
```

---

# Forbidden

```text id="f2m8ld"
Thread.sleep()
```

---

# Forbidden

```text id="r9m4wr"
blocking JDBC drivers
inside reactive flows
```

---

# Forbidden

```text id="u3m1qp"
synchronous HTTP clients
inside WebFlux chains
```

---

# Critical Rule

```text id="m8x4qp"
one blocking call
can compromise
the entire reactive pipeline
```

---

# 5. .block() Rules

# Official Rule

```text id="x1m7wr"
.block()
is forbidden
inside application flows
```

---

# Forbidden Locations

| Location        | Forbidden |
| --------------- | --------- |
| Controllers     | Yes       |
| Services        | Yes       |
| Kafka consumers | Yes       |
| Repositories    | Yes       |

---

# Exception Cases

Allowed ONLY in:

| Scenario          | Allowed    |
| ----------------- | ---------- |
| Testing           | Controlled |
| Bootstrap startup | Controlled |
| Migration tooling | Controlled |

---

# Critical Rule

```text id="v6m2qp"
.block()
inside request flows
breaks reactive scalability
```

---

# 6. Reactive Layer Responsibilities

# Domain Layer

The domain layer MUST remain:

* reactive-compatible
* framework-agnostic
* infrastructure-independent

---

# Forbidden

```text id="u9m4ld"
reactive infrastructure logic
inside aggregates
```

---

# Application Layer

Application services MAY orchestrate:

* Mono pipelines
* Flux pipelines
* async workflows
* reactive composition

---

# Preferred

```text id="q7m4wr"
reactive orchestration
inside application layer
```

---

# Adapters Layer

Adapters MAY contain:

* reactive infrastructure integration
* reactive mapping
* reactive protocol handling

---

# Forbidden

```text id="m9x2qp"
business invariants
inside reactive adapters
```

---

# 7. Reactive Composition Rules

Reactive pipelines SHOULD remain:

| Principle   | Mandatory |
| ----------- | --------- |
| Readable    | Yes       |
| Predictable | Yes       |
| Traceable   | Yes       |
| Composable  | Yes       |

---

# Forbidden

```text id="f2m7wr"
reactive spaghetti pipelines
```

---

# Forbidden

```text id="x5m1ld"
deeply nested flatMaps
without justification
```

---

# Preferred

```text id="u7m8qp"
small composable operators
```

---

# Critical Rule

```text id="m6x7wr"
reactive code
must remain understandable
```

---

# 8. Reactive Error Handling Rules

Errors MUST remain:

* explicit
* observable
* traceable
* recoverable where appropriate

---

# Preferred Operators

| Operator      | Preferred  |
| ------------- | ---------- |
| onErrorResume | Yes        |
| onErrorMap    | Yes        |
| retryWhen     | Controlled |
| timeout       | Yes        |

---

# Forbidden

```text id="u1m4ld"
silent error swallowing
```

---

# Forbidden

```text id="v8m2qp"
global generic exception masking
```

---

# Critical Rule

```text id="q5m8wr"
reactive failures
must remain observable
```

---

# 9. Timeout Rules

Distributed operations MUST support:

```text id="x7m1qp"
explicit timeout handling
```

---

# Mandatory Scenarios

| Scenario              | Mandatory   |
| --------------------- | ----------- |
| External APIs         | Yes         |
| Kafka consumers       | Recommended |
| Database calls        | Recommended |
| Provider integrations | Yes         |

---

# Forbidden

```text id="m2v8ld"
unbounded reactive waits
```

---

# 10. Retry Rules

Retries MUST remain:

* intentional
* observable
* bounded
* idempotent-safe

---

# Forbidden

```text id="u4m7wr"
infinite retries
```

---

# Forbidden

```text id="f8m1ld"
blind retries
without idempotency validation
```

---

# Preferred

```text id="m6x2qp"
exponential backoff
```

---

# Critical Rule

```text id="x1m9wr"
retries
must not amplify failures
```

---

# 11. Reactive Context Propagation Rules

Reactive pipelines MUST propagate:

| Context          | Mandatory |
| ---------------- | --------- |
| tenantId         | Yes       |
| correlationId    | Yes       |
| traceId          | Yes       |
| security context | Yes       |

---

# Forbidden

```text id="p7m4ld"
tenant context loss
inside reactive chains
```

---

# Critical Rule

```text id="v5m8qp"
context propagation
is mandatory
in distributed systems
```

---

# 12. Reactive Security Rules

Security MUST remain reactive.

---

# Mandatory Rules

| Rule                    | Mandatory |
| ----------------------- | --------- |
| Reactive authentication | Yes       |
| Reactive authorization  | Yes       |
| Reactive JWT validation | Yes       |

---

# Forbidden

```text id="q3m1wr"
blocking security filters
```

---

# Forbidden

```text id="k9m7qp"
ThreadLocal-based security state
inside reactive flows
```

---

# 13. Reactive Persistence Rules

Persistence MUST remain:

* non-blocking
* reactive-compatible
* async-safe

---

# Mandatory Technologies

| Technology     | Mandatory |
| -------------- | --------- |
| R2DBC          | Preferred |
| Reactive Redis | Preferred |
| Reactive Mongo | Allowed   |

---

# Forbidden

```text id="u4m7wr"
blocking JPA
inside WebFlux request chains
```

---

# Forbidden

```text id="x8m4qp"
EntityManager
inside reactive services
```

---

# 14. Reactive Messaging Rules

Messaging MUST support:

* replay safety
* async consumption
* backpressure awareness
* tenant propagation

---

# Preferred Technologies

| Capability       | Preferred      |
| ---------------- | -------------- |
| Kafka            | Reactor Kafka  |
| Event Processing | Flux pipelines |

---

# Forbidden

```text id="r6m2ld"
blocking event consumers
```

---

# Critical Rule

```text id="y2m8wr"
event consumers
must remain replay-safe
```

---

# 15. Backpressure Rules

Reactive systems MUST support:

```text id="m1x7qp"
backpressure-aware processing
```

---

# Mandatory Protections

| Protection          | Mandatory   |
| ------------------- | ----------- |
| Rate limiting       | Recommended |
| Timeout handling    | Yes         |
| Buffer control      | Yes         |
| Consumer throttling | Recommended |

---

# Forbidden

```text id="u8m4ld"
unbounded buffering
```

---

# 16. Scheduler Rules

Schedulers MUST remain controlled.

---

# Forbidden

```text id="k3m1wr"
random publishOn usage
```

---

# Forbidden

```text id="x5m8qp"
thread hopping
without architectural reason
```

---

# Preferred

```text id="u4m7wr"
minimal scheduler switching
```

---

# Critical Rule

```text id="m9x7qp"
scheduler changes
must be intentional
```

---

# 17. Observability Rules

All reactive flows MUST remain:

* traceable
* observable
* correlated

---

# Mandatory Support

| Capability              | Mandatory |
| ----------------------- | --------- |
| Distributed tracing     | Yes       |
| Structured logging      | Yes       |
| Correlation propagation | Yes       |
| Metrics                 | Yes       |

---

# Forbidden

```text id="r6m2ld"
untraceable reactive pipelines
```

---

# 18. Reactive Testing Rules

Reactive flows MUST be tested using:

| Technology   | Mandatory |
| ------------ | --------- |
| Reactor Test | Yes       |
| StepVerifier | Yes       |

---

# Forbidden

```text id="x8m4qp"
Thread.sleep()
inside reactive tests
```

---

# Preferred

```text id="f4m1wr"
deterministic reactive testing
```

---

# 19. Reactive Anti-Patterns

# Anti-Pattern 1

```text id="m7x2qp"
Reactive Spaghetti
```

Unreadable chained operators.

---

# Anti-Pattern 2

```text id="u3m8wr"
Fake Reactive
```

Wrapping blocking code in Mono.just().

---

# Anti-Pattern 3

```text id="k5m1ld"
Blocking Leakage
```

JDBC/JPA inside reactive flows.

---

# Anti-Pattern 4

```text id="v2m7qp"
Context Loss
```

tenantId/correlationId disappearing.

---

# Anti-Pattern 5

```text id="x9m4wr"
Infinite Retry Storms
```

Unbounded retry amplification.

---

# 20. Recommended Reactive Design Flow

# Step 1

Design:

```text id="q4m8qp"
business orchestration
```

---

# Step 2

Define:

```text id="u1m7wr"
reactive boundaries
```

---

# Step 3

Validate:

```text id="m6x2qp"
non-blocking infrastructure
```

---

# Step 4

Implement:

```text id="r8m1ld"
context propagation
```

---

# Step 5

Validate:

```text id="x3m7qp"
observability
and replay safety
```

---

# 21. Non-Negotiable Rules

# Rule 1

```text id="y7m1ld"
.block()
inside reactive flows
is forbidden
```

---

# Rule 2

```text id="p4m8qp"
all distributed flows
must remain observable
```

---

# Rule 3

```text id="u7m2ld"
tenant context
must survive
reactive boundaries
```

---

# Rule 4

```text id="m4v8wr"
blocking infrastructure
inside WebFlux chains
is forbidden
```

---

# Rule 5

```text id="x7m2qp"
reactive code
must remain understandable
```

---

# 22. Final Statement

Reactive programming is considered a foundational runtime capability of the CodeCore platform.

All reactive pipelines, event consumers, controllers, repositories, integrations, and distributed workflows MUST preserve:

* non-blocking execution
* replay safety
* reactive observability
* tenant-safe propagation
* backpressure awareness
* async scalability
* readable orchestration
* resilient distributed execution

Reactive correctness is considered foundational to the scalability and resilience of CodeCore.

```
```
