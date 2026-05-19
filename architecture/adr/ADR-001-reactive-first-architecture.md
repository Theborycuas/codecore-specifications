````md id="5m8xqp"
# ADR-001 — Reactive-First Architecture

# Status

```text
ACCEPTED
````

---

# Date

```text
2026-05-19
```

---

# Decision Makers

* Platform Architecture Team
* CodeCore Core Engineering

---

# Context

CodeCore is being designed as:

* an enterprise-grade SaaS platform
* a multi-tenant architecture
* an event-driven ecosystem
* a highly scalable platform foundation
* a distributed reactive system

The platform is expected to support:

* high concurrency
* asynchronous orchestration
* distributed communication
* event-driven integrations
* external provider orchestration
* future multi-region scalability
* AI-driven workloads
* real-time observability
* high-volume event processing

Traditional blocking architectures introduce:

* thread exhaustion risks
* inefficient resource usage
* scalability bottlenecks
* fragile synchronous chains
* poor resilience under load
* inefficient I/O handling

The platform requires:

```text id="m4v7wr"
non-blocking
async-first
resource-efficient
fault-tolerant
communication
```

---

# Decision

CodeCore officially adopts:

```text id="x2m8qp"
Reactive-First Architecture
```

as a foundational architectural principle.

All platform capabilities MUST prioritize:

* non-blocking execution
* asynchronous orchestration
* reactive messaging
* backpressure-aware processing
* resource-efficient concurrency
* reactive observability propagation

The platform standardizes on:

| Capability           | Standard                      |
| -------------------- | ----------------------------- |
| Reactive Framework   | Spring WebFlux                |
| Reactive Streams     | Project Reactor               |
| Async Types          | Mono / Flux                   |
| Reactive Messaging   | Kafka Reactive APIs           |
| Reactive Persistence | Reactive repositories/drivers |
| Reactive Security    | Reactive Spring Security      |

---

# Architectural Principles

# 1. Non-Blocking I/O

All I/O operations MUST be non-blocking whenever possible.

---

## Includes

* HTTP communication
* database access
* Kafka messaging
* cache access
* external provider integrations
* file operations

---

## Forbidden

```text id="q7m1ld"
thread-per-request
blocking orchestration
```

---

# 2. Async-First Communication

The platform officially adopts:

```text id="f5v8qp"
async-first communication
```

---

## Preferred Communication

| Style              | Preferred |
| ------------------ | --------- |
| Domain events      | Yes       |
| Async messaging    | Yes       |
| Reactive pipelines | Yes       |

---

## Discouraged

| Style                    | Reason                  |
| ------------------------ | ----------------------- |
| Sync-heavy orchestration | Fragility               |
| Blocking workflows       | Scalability bottlenecks |
| Deep sync chains         | Cascading failures      |

---

# 3. Backpressure Awareness

All reactive pipelines MUST support:

```text id="u4m9wr"
backpressure-aware processing
```

---

## Required Protections

| Protection          | Mandatory |
| ------------------- | --------- |
| Rate limiting       | Yes       |
| Buffer control      | Yes       |
| Consumer throttling | Yes       |
| Timeout handling    | Yes       |

---

# 4. Resource Efficiency

Reactive execution is adopted to improve:

* CPU efficiency
* thread utilization
* memory scalability
* connection scalability

---

## Critical Principle

```text id="k8x2qp"
threads
are expensive resources
```

---

# 5. Reactive Context Propagation

The platform MUST propagate:

| Context               | Mandatory |
| --------------------- | --------- |
| tenantId              | Yes       |
| correlationId         | Yes       |
| traceId               | Yes       |
| authorization context | Yes       |

across reactive boundaries.

---

# Technology Decision

# Official Reactive Stack

| Capability         | Technology               |
| ------------------ | ------------------------ |
| Reactive Engine    | Project Reactor          |
| HTTP Layer         | Spring WebFlux           |
| Reactive Security  | Spring Security Reactive |
| Reactive Messaging | Reactor Kafka            |
| Reactive Database  | R2DBC                    |
| Reactive Cache     | Reactive Redis           |
| Reactive Tracing   | OpenTelemetry            |

---

# Why WebFlux

WebFlux was selected because it provides:

* native reactive support
* Reactor integration
* async HTTP pipelines
* backpressure support
* reactive security integration
* reactive filter chains
* reactive observability support

---

# Why Reactor

Project Reactor was selected because:

* it is the native Spring reactive ecosystem
* it supports backpressure
* it supports async composition
* it integrates with WebFlux
* it provides mature reactive operators

---

# Standard Reactive Types

# Official Types

| Type    | Purpose                |
| ------- | ---------------------- |
| Mono<T> | Single async result    |
| Flux<T> | Multiple async results |

---

## Forbidden

```text id="n5m4ld"
CompletableFuture
inside core reactive flows
```

unless explicitly justified.

---

# Reactive Persistence Strategy

Reactive persistence is mandatory whenever supported.

---

# Preferred Persistence Technologies

| Capability | Preferred      |
| ---------- | -------------- |
| PostgreSQL | R2DBC          |
| Redis      | Reactive Redis |
| MongoDB    | Reactive Mongo |
| Kafka      | Reactor Kafka  |

---

# Forbidden

```text id="d7x1wr"
blocking JPA
inside reactive request chains
```

---

# Reactive Messaging Strategy

Reactive messaging is the preferred integration mechanism.

---

# Preferred Messaging Patterns

| Pattern                 | Preferred |
| ----------------------- | --------- |
| Domain events           | Yes       |
| Async event propagation | Yes       |
| Replay-safe messaging   | Yes       |
| Idempotent consumers    | Yes       |

---

# Critical Rule

```text id="v3m8qp"
events
may be delivered
multiple times
```

---

# Reactive Security Rules

Security flows MUST remain reactive.

---

# Includes

* authentication
* authorization
* JWT validation
* session validation
* security filters

---

# Forbidden

```text id="x9m2wr"
blocking security filters
```

---

# Reactive Observability Rules

Observability MUST support reactive flows.

---

# Required Support

| Capability              | Mandatory |
| ----------------------- | --------- |
| Trace propagation       | Yes       |
| Correlation propagation | Yes       |
| Reactive metrics        | Yes       |
| Reactive logging        | Yes       |

---

# Critical Rule

```text id="m6v4ld"
all reactive flows
must remain traceable
```

---

# Multi-Tenant Reactive Rules

Tenant isolation MUST survive reactive boundaries.

---

# Mandatory Propagation

| Context          | Mandatory |
| ---------------- | --------- |
| tenantId         | Yes       |
| security context | Yes       |
| correlationId    | Yes       |

---

# Forbidden

```text id="u2x8qp"
tenant context loss
inside reactive chains
```

---

# Fault Tolerance Rules

Reactive pipelines MUST support:

| Capability           | Mandatory   |
| -------------------- | ----------- |
| Retries              | Yes         |
| Circuit breaking     | Recommended |
| Timeout handling     | Yes         |
| Fallback strategies  | Recommended |
| Graceful degradation | Recommended |

---

# Critical Rule

```text id="f7m1wr"
failure
must remain isolated
```

---

# Event-Driven Compatibility

Reactive-first architecture complements:

```text id="r4m9ld"
event-driven architecture
```

---

# Strategic Alignment

Reactive architecture was selected to support:

* high-volume events
* distributed messaging
* async orchestration
* eventual consistency
* scalable integrations

---

# Hexagonal Architecture Compatibility

Reactive-first architecture MUST NOT violate:

* DDD boundaries
* Hexagonal Architecture
* bounded context isolation

---

# Critical Rule

```text id="y5m2qp"
reactive architecture
does not replace
good domain design
```

---

# Non-Negotiable Rules

# Rule 1

```text id="g8x1wr"
blocking calls
inside reactive request flows
are forbidden
```

---

# Rule 2

```text id="m3v7ld"
shared mutable state
inside reactive pipelines
is forbidden
```

---

# Rule 3

```text id="u9m4qp"
reactive boundaries
must preserve
tenant isolation
```

---

# Rule 4

```text id="q2x8wr"
sync-heavy orchestration
must be avoided
```

---

# Rule 5

```text id="x7m1ld"
reactive flows
must remain observable
```

---

# Consequences

# Positive Consequences

| Benefit                         | Impact                     |
| ------------------------------- | -------------------------- |
| Better scalability              | Higher concurrency         |
| Better resource utilization     | Lower infrastructure costs |
| Improved async orchestration    | Better resilience          |
| Better event integration        | Cleaner EDA                |
| Improved distributed processing | Enterprise scalability     |

---

# Negative Consequences

| Trade-Off                               | Impact                 |
| --------------------------------------- | ---------------------- |
| Increased complexity                    | Learning curve         |
| Reactive debugging complexity           | Operational complexity |
| Reactive context propagation complexity | Infrastructure effort  |
| Reactive persistence limitations        | Technology constraints |

---

# Risks

| Risk                     | Mitigation                     |
| ------------------------ | ------------------------------ |
| Reactive misuse          | Architecture governance        |
| Blocking leaks           | Static analysis/reviews        |
| Reactive spaghetti flows | Strict layering                |
| Context propagation loss | Shared reactive infrastructure |

---

# Alternatives Considered

# Alternative 1 — Traditional Spring MVC

## Rejected Because

* blocking execution model
* thread scalability limitations
* weaker async orchestration
* less aligned with event-driven architecture

---

# Alternative 2 — Mixed Reactive + Blocking Architecture

## Rejected Because

* inconsistent architecture
* blocking leaks
* unpredictable performance
* operational complexity

---

# Alternative 3 — Fully Synchronous Monolith

## Rejected Because

* poor scalability profile
* weak distributed-system readiness
* high risk of cascading failures

---

# Architectural Constraints

The reactive-first decision is considered:

```text id="p4m8qp"
foundational
and strategic
```

Changing this decision later would require:

* major platform rewrites
* infrastructure redesign
* observability redesign
* messaging redesign
* persistence redesign

---

# Related ADRs

| ADR     | Relationship                  |
| ------- | ----------------------------- |
| ADR-002 | Event-Driven Architecture     |
| ADR-003 | Multi-Tenant Isolation        |
| ADR-004 | Hexagonal Architecture        |
| ADR-018 | Transaction Boundary Strategy |

---

# Final Statement

CodeCore officially adopts:

```text id="n1x7wr"
Reactive-First Architecture
```

as a foundational enterprise architectural principle.

All future modules, APIs, integrations, messaging flows, and infrastructure components MUST prioritize:

* non-blocking execution
* async orchestration
* reactive scalability
* tenant-safe propagation
* reactive observability
* distributed resilience

Reactive architecture is considered a strategic platform capability of CodeCore.

```
```
