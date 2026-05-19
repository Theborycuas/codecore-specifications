````md id="8x4qpm"
# ADR-003 — Multi-Tenant Isolation Strategy

# Status

```text
ACCEPTED
````

---

# Date

```text id="m7v1wr"
2026-05-19
```

---

# Decision Makers

* Platform Architecture Team
* CodeCore Core Engineering

---

# Context

CodeCore is designed as:

* an enterprise-grade SaaS platform
* a shared multi-tenant ecosystem
* a modular bounded-context architecture
* a distributed event-driven system
* a reactive-first platform foundation

The platform is expected to support:

* multiple independent customers
* isolated organizations
* tenant-scoped security
* tenant-scoped billing
* tenant-scoped observability
* tenant-scoped integrations
* future multi-region deployments

The platform MUST guarantee:

```text id="u8m4ld"
strict tenant isolation
```

at all architectural levels.

Multi-tenancy failures are considered:

* critical security failures
* compliance failures
* data isolation violations
* enterprise trust violations

The platform requires a formally governed:

```text id="q2x7qp"
tenant isolation strategy
```

to ensure long-term scalability and security correctness.

---

# Decision

CodeCore officially adopts:

```text id="f5m1wr"
Strict Multi-Tenant Isolation
```

as a foundational architectural principle.

All platform components MUST preserve tenant isolation across:

* persistence
* APIs
* messaging
* observability
* security
* integrations
* caching
* reactive pipelines
* event propagation

The platform standardizes on:

| Capability            | Strategy                                      |
| --------------------- | --------------------------------------------- |
| Tenant Model          | Shared application / isolated logical tenancy |
| Tenant Identification | tenantId                                      |
| Tenant Propagation    | Mandatory                                     |
| Tenant Authorization  | Tenant-scoped                                 |
| Tenant Events         | Tenant-aware                                  |
| Tenant Observability  | Tenant-aware                                  |
| Tenant Integrations   | Tenant-scoped                                 |

---

# Strategic Principles

# 1. Tenant Isolation is Non-Negotiable

The platform MUST behave as if:

```text id="x9m2qp"
every tenant
is an isolated system
```

---

## Critical Rule

```text id="m3v8wr"
Tenant A
must never access
Tenant B data
```

---

# 2. Tenant Context Propagation

All distributed operations MUST propagate:

| Context               | Mandatory   |
| --------------------- | ----------- |
| tenantId              | Yes         |
| correlationId         | Yes         |
| authorization context | Yes         |
| traceId               | Recommended |

---

## Applies To

* HTTP requests
* Kafka events
* reactive pipelines
* cache operations
* database operations
* async workflows

---

## Critical Rule

```text id="r7m1ld"
tenant context
must survive
all async boundaries
```

---

# 3. Tenant-Aware Architecture

All business capabilities MUST remain tenant-aware.

---

# Includes

| Capability    | Tenant-Aware |
| ------------- | ------------ |
| Billing       | Yes          |
| Payments      | Yes          |
| Authorization | Yes          |
| Notifications | Yes          |
| File storage  | Yes          |
| Audit         | Yes          |
| Observability | Yes          |

---

# Forbidden

```text id="u4x8qp"
tenant-agnostic
business persistence
```

---

# 4. Tenant-Scoped Ownership

Business data MUST belong to a tenant.

---

## Examples

| Resource      | Tenant Scoped |
| ------------- | ------------- |
| Users         | Yes           |
| Organizations | Yes           |
| Invoices      | Yes           |
| Subscriptions | Yes           |
| Notifications | Yes           |

---

## Exceptions

Platform-level operational metadata MAY remain global ONLY when explicitly justified.

---

# Tenant Identification Strategy

# Official Tenant Identifier

```text id="n5m4wr"
tenantId
```

---

# Tenant Identifier Characteristics

| Property        | Requirement |
| --------------- | ----------- |
| Globally unique | Yes         |
| Immutable       | Yes         |
| Traceable       | Yes         |
| Serializable    | Yes         |

---

# Tenant Context Sources

Tenant context MAY originate from:

| Source                  | Allowed |
| ----------------------- | ------- |
| JWT claims              | Yes     |
| Session context         | Yes     |
| API Gateway headers     | Yes     |
| Internal event metadata | Yes     |

---

## Forbidden

```text id="x2m8qp"
tenant inference
through implicit assumptions
```

---

# Persistence Isolation Strategy

# Official Strategy

```text id="v7m1ld"
shared infrastructure
+
logical tenant isolation
```

---

# Isolation Rules

All tenant-scoped persistence MUST enforce:

| Rule                 | Mandatory |
| -------------------- | --------- |
| tenantId filtering   | Yes       |
| tenant-aware indexes | Yes       |
| tenant-aware queries | Yes       |
| tenant-aware events  | Yes       |

---

# Forbidden

```text id="k8x4qp"
cross-tenant joins
without explicit governance
```

---

# Database Isolation Rules

Every tenant-scoped table MUST contain:

```text id="f3m7wr"
tenant_id
```

unless explicitly exempted.

---

# Mandatory Query Protection

All queries MUST enforce:

```text id="y1m8ld"
tenant-aware filtering
```

---

# Forbidden

```text id="q6x2qp"
queries
without tenant constraints
```

---

# Messaging Isolation Strategy

All events MUST remain tenant-aware.

---

# Mandatory Event Metadata

| Field         | Mandatory |
| ------------- | --------- |
| tenantId      | Yes       |
| correlationId | Yes       |
| eventId       | Yes       |

---

# Critical Rule

```text id="r4m9wr"
tenant boundaries
must survive
event propagation
```

---

# Kafka Isolation Rules

Kafka topics MAY be shared.

Tenant isolation MUST occur through:

* tenant-aware payloads
* consumer validation
* authorization enforcement

---

# Forbidden

```text id="u8m1qp"
cross-tenant event consumption
```

---

# Reactive Tenant Propagation

Reactive pipelines MUST preserve:

* tenantId
* security context
* correlationId

across all async flows.

---

# Mandatory Support

| Capability                   | Mandatory |
| ---------------------------- | --------- |
| Reactive context propagation | Yes       |
| Async propagation            | Yes       |
| Context preservation         | Yes       |

---

# Forbidden

```text id="m5x7ld"
tenant context loss
inside reactive chains
```

---

# Authorization Isolation Rules

Authorization MUST remain tenant-scoped.

---

# Mandatory Rules

| Rule                      | Mandatory |
| ------------------------- | --------- |
| Tenant-scoped roles       | Yes       |
| Tenant-scoped permissions | Yes       |
| Tenant-scoped policies    | Yes       |
| Tenant-scoped sessions    | Yes       |

---

## Critical Rule

```text id="x9m2wr"
authorization decisions
must respect tenant boundaries
```

---

# JWT Tenant Rules

JWT tokens MUST contain:

| Claim      | Mandatory   |
| ---------- | ----------- |
| sub        | Yes         |
| tenant_id  | Yes         |
| session_id | Recommended |

---

# JWT MUST NOT

```text id="k4m8qp"
become the only source
of authorization truth
```

---

# Cache Isolation Rules

All cache entries MUST remain tenant-aware.

---

# Mandatory Cache Rules

| Rule                      | Mandatory   |
| ------------------------- | ----------- |
| tenant-aware keys         | Yes         |
| tenant-aware invalidation | Yes         |
| tenant-aware TTL policies | Recommended |

---

# Cache Key Pattern

```text id="p2x7wr"
<tenantId>:<resource>:<identifier>
```

---

# Forbidden

```text id="v6m1ld"
global shared cache keys
for tenant business data
```

---

# File Storage Isolation Strategy

File storage MUST remain tenant-aware.

---

# Mandatory Rules

| Rule                         | Mandatory |
| ---------------------------- | --------- |
| Tenant-scoped file ownership | Yes       |
| Tenant-aware storage paths   | Yes       |
| Tenant-aware access control  | Yes       |

---

# Recommended Storage Pattern

```text id="m8x4qp"
/tenants/{tenantId}/...
```

---

# Observability Isolation Rules

Observability MUST support tenant isolation.

---

# Includes

| Capability | Tenant-Aware |
| ---------- | ------------ |
| Metrics    | Yes          |
| Logs       | Yes          |
| Traces     | Yes          |
| Alerts     | Yes          |

---

# Critical Rule

```text id="f1m7wr"
tenant telemetry
must remain isolated
```

---

# Audit Isolation Rules

Audit trails MUST remain tenant-scoped.

---

# Mandatory Rules

| Rule                         | Mandatory |
| ---------------------------- | --------- |
| Tenant-aware audit records   | Yes       |
| Tenant-aware traceability    | Yes       |
| Tenant-aware compliance logs | Yes       |

---

# Forbidden

```text id="x5m2qp"
cross-tenant audit visibility
```

---

# Integration Isolation Rules

External integrations MUST remain tenant-scoped.

---

# Includes

| Integration Type  | Tenant Scoped |
| ----------------- | ------------- |
| Payment providers | Yes           |
| OAuth providers   | Yes           |
| AI providers      | Yes           |
| Email providers   | Yes           |

---

# Critical Rule

```text id="q7m8ld"
tenant integrations
must remain isolated
```

---

# Security Rules

Tenant isolation failures are considered:

```text id="r2x1wr"
critical security incidents
```

---

# Mandatory Protections

| Protection                | Mandatory |
| ------------------------- | --------- |
| Tenant validation         | Yes       |
| Authorization enforcement | Yes       |
| Replay protection         | Yes       |
| Auditability              | Yes       |
| Traceability              | Yes       |

---

# Forbidden

```text id="m4v9qp"
trusting client-provided tenant data
without validation
```

---

# API Gateway Rules

The API Gateway MUST enforce:

* tenant extraction
* tenant validation
* security propagation
* correlation propagation

---

# Critical Rule

```text id="u9m2ld"
tenant context
must enter
through trusted boundaries
```

---

# Event-Driven Compatibility

Multi-tenancy officially complements:

```text id="x3m7wr"
Event-Driven Architecture
```

---

# Strategic Alignment

Tenant-aware EDA supports:

* isolated event propagation
* tenant-safe retries
* tenant-safe observability
* scalable distributed processing

---

# Reactive Architecture Compatibility

Multi-tenancy officially complements:

```text id="f8m1qp"
Reactive-First Architecture
```

---

# Strategic Alignment

Reactive context propagation is mandatory to preserve:

* tenant isolation
* security context
* distributed traceability

---

# Non-Negotiable Rules

# Rule 1

```text id="k5x4wr"
cross-tenant data access
is forbidden
```

---

# Rule 2

```text id="n7m2ld"
all business entities
must be tenant-aware
unless explicitly exempted
```

---

# Rule 3

```text id="m1x8qp"
tenant context
must survive
async boundaries
```

---

# Rule 4

```text id="u6m4wr"
all queries
must enforce
tenant filtering
```

---

# Rule 5

```text id="p3m7ld"
tenant isolation failures
are critical incidents
```

---

# Consequences

# Positive Consequences

| Benefit                      | Impact                |
| ---------------------------- | --------------------- |
| Strong isolation             | Enterprise trust      |
| Better SaaS scalability      | Multi-customer growth |
| Stronger security posture    | Reduced leakage risk  |
| Tenant-aware observability   | Operational clarity   |
| Cleaner ownership boundaries | Better architecture   |

---

# Negative Consequences

| Trade-Off                             | Impact                |
| ------------------------------------- | --------------------- |
| Increased propagation complexity      | Infrastructure effort |
| Additional query constraints          | Development overhead  |
| Tenant-aware observability complexity | Monitoring effort     |
| Cache isolation complexity            | Infrastructure design |

---

# Risks

| Risk                     | Mitigation            |
| ------------------------ | --------------------- |
| Tenant leakage           | Automated validation  |
| Context propagation loss | Shared infrastructure |
| Unsafe queries           | Repository governance |
| Cross-tenant caching     | Cache isolation rules |
| Event leakage            | Event validation      |

---

# Alternatives Considered

# Alternative 1 — Single-Tenant Deployments

## Rejected Because

* operational inefficiency
* poor scalability economics
* infrastructure duplication
* weak SaaS scalability

---

# Alternative 2 — Schema-Per-Tenant

## Rejected Because

* operational complexity
* migration complexity
* scalability concerns
* infrastructure overhead

---

# Alternative 3 — Database-Per-Tenant

## Rejected Because

* excessive infrastructure costs
* operational explosion
* weak elasticity
* operational complexity

---

# Architectural Constraints

The multi-tenant isolation strategy is considered:

```text id="y8m1qp"
foundational
and irreversible
```

Changing this decision later would require:

* persistence redesign
* security redesign
* event redesign
* cache redesign
* observability redesign

---

# Related ADRs

| ADR     | Relationship                |
| ------- | --------------------------- |
| ADR-001 | Reactive-First Architecture |
| ADR-002 | Event-Driven Architecture   |
| ADR-004 | Hexagonal Architecture      |
| ADR-009 | Security Boundary Strategy  |

---

# Final Statement

CodeCore officially adopts:

```text id="t4m7wr"
Strict Multi-Tenant Isolation
```

as a foundational enterprise architectural principle.

All future modules, APIs, persistence models, events, reactive pipelines, integrations, observability flows, and infrastructure components MUST preserve:

* tenant isolation
* tenant-aware propagation
* tenant-scoped authorization
* tenant-safe messaging
* tenant-safe observability
* tenant-safe caching
* tenant-safe integrations

Tenant isolation is considered a non-negotiable enterprise security capability of the CodeCore platform.

```
```
