````md id="1x8qpm"
# ADR-004 — Hexagonal Architecture Standard

# Status

```text
ACCEPTED
````

---

# Date

```text id="m4v7wr"
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
* a modular bounded-context ecosystem
* a reactive-first distributed architecture
* an event-driven platform
* a provider-agnostic system
* a long-term scalable platform foundation

The platform requires:

* clear business ownership
* infrastructure independence
* maintainable domain logic
* technology replacement flexibility
* testability
* bounded context isolation
* low coupling
* high cohesion

Traditional layered architectures often evolve into:

* framework-centric business logic
* infrastructure leakage
* tightly coupled domains
* untestable application services
* provider-dependent business rules
* fragile integrations

The platform requires:

```text id="u7m1ld"
strict separation
between
business logic
and infrastructure
```

to preserve long-term architectural integrity.

---

# Decision

CodeCore officially adopts:

```text id="x5m8qp"
Hexagonal Architecture
```

as the mandatory architectural standard for all bounded contexts.

All modules MUST separate:

* domain logic
* application orchestration
* infrastructure concerns
* transport concerns
* provider integrations

through explicit architectural boundaries.

The platform standardizes on:

| Layer          | Responsibility                 |
| -------------- | ------------------------------ |
| Domain         | Business rules                 |
| Application    | Use-case orchestration         |
| Ports          | Contracts/interfaces           |
| Adapters       | Infrastructure implementations |
| Infrastructure | External technologies          |

---

# Strategic Principles

# 1. Domain-Centric Architecture

Business rules MUST remain at the center of the architecture.

---

## Critical Rule

```text id="n2x7wr"
business rules
must not depend
on infrastructure
```

---

# 2. Dependency Inversion

Dependencies MUST point inward.

---

# Official Dependency Direction

```text id="f8m4ld"
Infrastructure
    ↓
Adapters
    ↓
Application
    ↓
Domain
```

---

## Forbidden

```text id="m3v9qp"
domain logic
depending on frameworks
```

---

# 3. Technology Independence

The business domain MUST survive:

* framework replacement
* database replacement
* provider replacement
* transport replacement
* infrastructure replacement

---

## Critical Rule

```text id="x1m7wr"
domain logic
must remain
technology agnostic
```

---

# 4. Explicit Boundaries

Every bounded context MUST expose:

* explicit ports
* explicit contracts
* explicit APIs
* explicit event boundaries

---

## Forbidden

```text id="q7m2ld"
hidden infrastructure coupling
```

---

# Official Architectural Layers

# 1. Domain Layer

The Domain Layer contains:

* aggregates
* entities
* value objects
* domain services
* domain events
* business invariants

---

# Domain Layer Responsibilities

| Responsibility        | Allowed |
| --------------------- | ------- |
| Business rules        | Yes     |
| Invariant enforcement | Yes     |
| Domain events         | Yes     |
| Business validation   | Yes     |

---

# Domain Layer MUST NOT contain

| Forbidden             | Reason                 |
| --------------------- | ---------------------- |
| Framework annotations | Infrastructure leakage |
| Database logic        | Persistence coupling   |
| HTTP logic            | Transport coupling     |
| Kafka logic           | Messaging coupling     |
| Provider SDKs         | Vendor lock-in         |

---

## Critical Rule

```text id="v4m8qp"
domain logic
must remain pure
```

---

# 2. Application Layer

The Application Layer orchestrates:

* use cases
* workflows
* transactions
* coordination
* domain interactions

---

# Application Layer Responsibilities

| Responsibility           | Allowed |
| ------------------------ | ------- |
| Use-case orchestration   | Yes     |
| Transaction coordination | Yes     |
| Port usage               | Yes     |
| Security orchestration   | Yes     |

---

# Application Layer MUST NOT contain

| Forbidden           | Reason                 |
| ------------------- | ---------------------- |
| Business invariants | Domain leakage         |
| SQL logic           | Infrastructure leakage |
| Provider SDK logic  | Vendor coupling        |

---

## Critical Rule

```text id="m6x1wr"
application services
coordinate
they do not own
business rules
```

---

# 3. Ports Layer

Ports define:

```text id="u9m4ld"
contracts
between domain/application
and infrastructure
```

---

# Port Types

| Port Type   | Purpose                    |
| ----------- | -------------------------- |
| Input Port  | Use-case entry             |
| Output Port | Infrastructure abstraction |
| Query Port  | Controlled reads           |
| Event Port  | Event publication          |

---

# Examples

```text id="k3m8qp"
PaymentProviderPort
EmailSenderPort
UserRepositoryPort
EventPublisherPort
```

---

# Critical Rule

```text id="r5x2wr"
ports
belong to the domain
not infrastructure
```

---

# 4. Adapters Layer

Adapters implement ports.

---

# Adapter Types

| Adapter          | Purpose                 |
| ---------------- | ----------------------- |
| REST Adapter     | HTTP integration        |
| Kafka Adapter    | Messaging integration   |
| Database Adapter | Persistence integration |
| Provider Adapter | External systems        |

---

# Adapter Responsibilities

| Responsibility         | Allowed |
| ---------------------- | ------- |
| Payload transformation | Yes     |
| Protocol handling      | Yes     |
| SDK orchestration      | Yes     |
| Infrastructure mapping | Yes     |

---

# Adapters MUST NOT contain

| Forbidden            | Reason               |
| -------------------- | -------------------- |
| Core business rules  | Domain leakage       |
| Aggregate invariants | Ownership corruption |

---

## Critical Rule

```text id="x8m1ld"
adapters
translate
they do not own
business logic
```

---

# 5. Infrastructure Layer

Infrastructure contains:

* frameworks
* databases
* Kafka
* Redis
* provider SDKs
* observability tooling

---

# Infrastructure Responsibilities

| Responsibility             | Allowed |
| -------------------------- | ------- |
| Persistence implementation | Yes     |
| Messaging implementation   | Yes     |
| Cache implementation       | Yes     |
| Provider integration       | Yes     |

---

# Infrastructure MUST NOT

```text id="p2m7wr"
define business rules
```

---

# Official Package Structure

Each bounded context SHOULD follow:

```text id="m4x8qp"
<context>/
├── domain/
├── application/
├── ports/
├── adapters/
└── infrastructure/
```

---

# Example Structure

```text id="u7m1ld"
billing-management/
├── domain/
│   ├── aggregates/
│   ├── entities/
│   ├── value-objects/
│   └── events/
│
├── application/
│   ├── use-cases/
│   ├── services/
│   └── commands/
│
├── ports/
│   ├── inbound/
│   └── outbound/
│
├── adapters/
│   ├── inbound/
│   └── outbound/
│
└── infrastructure/
    ├── persistence/
    ├── messaging/
    └── configuration/
```

---

# Reactive Architecture Compatibility

Hexagonal Architecture MUST support:

```text id="v9m4qp"
Reactive-First Architecture
```

---

# Mandatory Reactive Support

| Capability             | Mandatory |
| ---------------------- | --------- |
| Reactive ports         | Yes       |
| Reactive repositories  | Yes       |
| Reactive messaging     | Yes       |
| Reactive orchestration | Yes       |

---

# Preferred Types

```text id="x3m8wr"
Mono<T>
Flux<T>
```

---

# Forbidden

```text id="f1m7ld"
blocking infrastructure
inside reactive flows
```

---

# Event-Driven Compatibility

Hexagonal Architecture MUST support:

```text id="r6m2qp"
Event-Driven Architecture
```

---

# Event Boundaries

Events MUST cross contexts through:

* event ports
* messaging adapters
* explicit contracts

---

## Forbidden

```text id="q5x1wr"
direct aggregate access
between contexts
```

---

# Provider-Agnostic Rules

All external providers MUST remain abstracted through ports.

---

# Examples

| Provider Capability | Required Port       |
| ------------------- | ------------------- |
| Payments            | PaymentProviderPort |
| Email               | EmailSenderPort     |
| AI                  | AIProviderPort      |
| Storage             | FileStoragePort     |

---

# Critical Rule

```text id="m8v4ld"
provider SDKs
must not leak
into domain/application layers
```

---

# Persistence Rules

Persistence MUST remain isolated behind ports.

---

# Forbidden

```text id="k2m7qp"
direct ORM leakage
into domain models
```

---

# Forbidden

```text id="u4x1wr"
cross-context repository access
```

---

# Preferred

```text id="p7m8ld"
repository ports
implemented by adapters
```

---

# Security Rules

Security MUST remain layered.

---

# Security Responsibilities

| Layer          | Responsibility               |
| -------------- | ---------------------------- |
| Domain         | Business authorization rules |
| Application    | Security orchestration       |
| Adapters       | Protocol security            |
| Infrastructure | Provider integration         |

---

# Critical Rule

```text id="y1m4qp"
security concerns
must not bypass
architectural boundaries
```

---

# Observability Rules

Observability MUST integrate without contaminating domain logic.

---

# Allowed

| Capability         | Allowed |
| ------------------ | ------- |
| Trace propagation  | Yes     |
| Metrics            | Yes     |
| Structured logging | Yes     |

---

# Forbidden

```text id="x6m7wr"
observability logic
inside domain models
```

---

# Multi-Tenant Compatibility

Hexagonal Architecture MUST preserve:

```text id="f9m2ld"
tenant isolation
```

---

# Mandatory Rules

| Rule                      | Mandatory |
| ------------------------- | --------- |
| Tenant-aware ports        | Yes       |
| Tenant-aware repositories | Yes       |
| Tenant-aware events       | Yes       |

---

# Critical Rule

```text id="m3x8qp"
tenant context
must survive
all architectural layers
```

---

# Testing Strategy

Hexagonal Architecture was selected to improve:

* unit testing
* integration testing
* adapter isolation
* domain testing
* provider mocking

---

# Testing Rules

| Test Type        | Priority   |
| ---------------- | ---------- |
| Domain tests     | Highest    |
| Use-case tests   | High       |
| Adapter tests    | High       |
| End-to-end tests | Controlled |

---

# Critical Rule

```text id="r8m1wr"
business rules
must be testable
without infrastructure
```

---

# Non-Negotiable Rules

# Rule 1

```text id="q4m7ld"
domain logic
must not depend
on frameworks
```

---

# Rule 2

```text id="u2x8qp"
provider SDKs
must remain isolated
inside adapters
```

---

# Rule 3

```text id="v5m1wr"
cross-context repository access
is forbidden
```

---

# Rule 4

```text id="m7x4ld"
business rules
must remain
inside the domain layer
```

---

# Rule 5

```text id="k9m2qp"
adapters
must not own
business decisions
```

---

# Consequences

# Positive Consequences

| Benefit                     | Impact                |
| --------------------------- | --------------------- |
| Better modularity           | Independent evolution |
| Better testability          | Faster development    |
| Better provider abstraction | Vendor flexibility    |
| Better maintainability      | Cleaner architecture  |
| Better scalability          | Enterprise readiness  |

---

# Negative Consequences

| Trade-Off                  | Impact                       |
| -------------------------- | ---------------------------- |
| More architectural layers  | Increased complexity         |
| More interfaces/ports      | Boilerplate                  |
| More structure enforcement | Learning curve               |
| More abstraction           | Initial development overhead |

---

# Risks

| Risk                   | Mitigation           |
| ---------------------- | -------------------- |
| Overengineering        | Governance           |
| Excessive abstraction  | Pragmatic boundaries |
| Adapter sprawl         | Package standards    |
| Infrastructure leakage | Architecture reviews |

---

# Alternatives Considered

# Alternative 1 — Traditional Layered Architecture

## Rejected Because

* infrastructure leakage
* weak domain protection
* framework-centric business logic
* poor provider abstraction

---

# Alternative 2 — Framework-Centric Architecture

## Rejected Because

* framework lock-in
* weak domain ownership
* poor long-term maintainability

---

# Alternative 3 — Anemic Domain Model

## Rejected Because

* weak invariant enforcement
* procedural business logic
* poor domain encapsulation

---

# Architectural Constraints

Hexagonal Architecture is considered:

```text id="x1m8wr"
a foundational
architectural standard
```

Changing this decision later would require:

* repository redesign
* module redesign
* infrastructure redesign
* testing redesign
* dependency redesign

---

# Related ADRs

| ADR     | Relationship                  |
| ------- | ----------------------------- |
| ADR-001 | Reactive-First Architecture   |
| ADR-002 | Event-Driven Architecture     |
| ADR-003 | Multi-Tenant Isolation        |
| ADR-005 | Domain-Driven Design Strategy |

---

# Final Statement

CodeCore officially adopts:

```text id="p4m7ld"
Hexagonal Architecture
```

as the mandatory architectural standard for all bounded contexts.

All future modules, APIs, integrations, messaging systems, repositories, and infrastructure components MUST preserve:

* domain-centric architecture
* explicit boundaries
* dependency inversion
* provider abstraction
* infrastructure isolation
* tenant-safe layering
* reactive compatibility
* event-driven integration

Hexagonal Architecture is considered a foundational maintainability and scalability capability of the CodeCore platform.

```
```
