# Repository Rules

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Repository Rules for CodeCore.

Its objectives are:

* standardize persistence access
* preserve aggregate boundaries
* prevent persistence abuse
* avoid business logic leakage
* maintain reactive consistency
* enforce modular isolation
* guide AI-assisted development
* ensure scalability and maintainability

This specification is mandatory for:

* repository implementations
* persistence design
* aggregate persistence
* query design
* reactive database access
* AI-generated code

---

# 2. REPOSITORY PHILOSOPHY

---

## 2.1 Official Definition

A Repository is:

```text id="1repo1"
An abstraction responsible for
persisting and retrieving aggregate state.
```

---

## 2.2 Core Principle

Repositories exist to:

* persist aggregates
* retrieve aggregates
* support domain consistency

Repositories DO NOT exist to:

* orchestrate business logic
* coordinate workflows
* implement application behavior

---

## 2.3 Aggregate Ownership Principle

Repositories belong to:

* Aggregate Roots

NOT internal entities.

---

## Correct

```text id="2repo2"
AppointmentRepository
UserRepository
TenantRepository
```

---

## Forbidden

```text id="3repo3"
AppointmentParticipantRepository
AddressRepository
PhoneRepository
```

unless operationally justified.

---

# 3. REPOSITORY DESIGN PRINCIPLES

---

# 3.1 One Repository Per Aggregate Root

Each Aggregate Root SHOULD have:

* one primary repository

---

# 3.2 Aggregate Boundary Respect

Repositories MUST preserve:

* aggregate consistency
* aggregate boundaries
* lifecycle ownership

---

# 3.3 Persistence Abstraction Principle

Repositories abstract:

* persistence implementation
* database technology
* query details

The domain MUST NOT depend directly on:

* SQL
* R2DBC
* PostgreSQL specifics
* Redis specifics

---

# 3.4 Minimal Responsibility Principle

Repositories SHOULD:

* persist
* retrieve
* query

Repositories SHOULD NOT:

* orchestrate workflows
* enforce business policies
* coordinate external systems

---

# 4. REPOSITORY LAYER RULES

---

# 4.1 Repository Placement

Repository contracts belong to:

```text id="4repo4"
domain.repository
```

Implementations belong to:

```text id="5repo5"
infrastructure.persistence
```

---

# 4.2 Dependency Direction

Allowed:

```text id="6repo6"
Application → Repository Interface
Infrastructure → Repository Implementation
```

---

## Forbidden

```text id="7repo7"
Domain → Infrastructure
Repository → Service Layer
```

---

# 4.3 Infrastructure Isolation

Repositories MUST isolate:

* database technology
* query engines
* persistence frameworks

from domain logic.

---

# 5. REPOSITORY METHOD RULES

---

# 5.1 Explicit Intent Principle

Repository methods MUST express:

* clear intent
* clear retrieval purpose

---

## Correct

```text id="8repo8"
findById()
findByTenantId()
existsOverlappingAppointments()
```

---

## Forbidden

```text id="9repo9"
processData()
execute()
handle()
```

---

# 5.2 Query Specificity Principle

Repository methods SHOULD remain:

* explicit
* focused
* predictable

Avoid:

* overly generic repositories
* universal query dumping

---

# 5.3 Minimal Exposure Principle

Repositories SHOULD expose:

* only required operations

Avoid unnecessary CRUD exposure.

---

# 5.4 Query Separation Principle

Heavy read scenarios SHOULD use:

* projections
* specialized queries
* read models

NOT oversized aggregate loading.

---

# 6. REACTIVE REPOSITORY RULES

---

# 6.1 Official Reactive Standard

CodeCore repositories MUST support:

```text id="10repo10"
Reactive Programming
```

using:

* Mono
* Flux
* non-blocking persistence

---

# 6.2 Blocking Operations Forbidden

Repositories MUST NEVER:

* block threads
* use blocking JDBC
* perform Thread.sleep
* use blocking ORM operations

inside reactive flows.

---

# 6.3 Reactive Return Types

Preferred:

```text id="11repo11"
Mono<T>
Flux<T>
```

Forbidden:

```text id="12repo12"
Optional<T>
List<T>
Future<T>
```

inside reactive repository contracts.

---

# 6.4 Reactive Consistency Principle

Reactive repositories MUST:

* preserve Reactor Context
* preserve tenant context
* remain non-blocking

---

# 7. QUERY DESIGN RULES

---

# 7.1 Aggregate Retrieval Principle

Repositories SHOULD retrieve:

* aggregates
* projections
* explicit query models

NOT arbitrary database structures.

---

# 7.2 Query Complexity Rule

Complex reporting queries SHOULD:

* use dedicated query models
* use projections
* use read-oriented optimization

NOT aggregate inflation.

---

# 7.3 Pagination Rule

Large datasets MUST support:

* pagination
* streaming
* chunked retrieval

---

# 7.4 Search Isolation Principle

Search-specific logic MAY be separated into:

* query services
* read repositories
* search adapters

---

# 8. BUSINESS LOGIC RESTRICTIONS

---

# 8.1 Forbidden Responsibilities

Repositories MUST NOT:

* orchestrate workflows
* send notifications
* apply business policies
* perform authorization
* coordinate modules
* manage transactions manually

---

# 8.2 Domain Logic Separation

Business rules belong to:

* aggregates
* domain services
* application services

NOT repositories.

---

# 8.3 Validation Restrictions

Repositories SHOULD NOT:

* validate business invariants
* own lifecycle rules

except persistence consistency validations.

---

# 9. MULTITENANCY RULES

---

# 9.1 Tenant Enforcement Principle

Repositories MUST enforce:

* tenant isolation
* tenant filtering
* tenant ownership validation

---

# 9.2 Mandatory Tenant Filtering

Tenant-owned entities MUST NEVER be queried without:

* tenant constraints

---

## Forbidden

```sql
SELECT * FROM appointments;
```

---

## Correct

```sql
SELECT * FROM appointments
WHERE tenant_id = :tenantId;
```

---

# 9.3 Cross Tenant Access

Cross-tenant repository access is forbidden unless:

* explicitly authorized
* platform-level operation

---

# 10. TRANSACTION RULES

---

# 10.1 Transaction Ownership

Repositories SHOULD NOT:

* control business transactions

Transaction orchestration belongs to:

* application services
* orchestration layers

---

# 10.2 Aggregate Transaction Scope

Repositories SHOULD preserve:

* aggregate transactional boundaries

---

# 10.3 Reactive Transactions

Reactive transaction management MUST:

* remain non-blocking
* preserve Reactor Context
* avoid imperative transaction leakage

---

# 11. EVENT RULES

---

# 11.1 Repository Event Restrictions

Repositories MUST NOT:

* publish domain events directly

Event publication belongs to:

* aggregates
* application services
* orchestration layers

---

# 11.2 Persistence Events

Infrastructure persistence events MAY exist internally.

But MUST NOT leak into domain design.

---

# 12. CACHING RULES

---

# 12.1 Repository Cache Awareness

Repositories MAY support:

* caching strategies
* reactive caching
* distributed caching

through infrastructure abstraction.

---

# 12.2 Cache Transparency Principle

Business logic MUST NOT depend directly on:

* cache existence
* cache technology

---

# 12.3 Cache Consistency

Cache invalidation MUST preserve:

* aggregate consistency
* tenant isolation

---

# 13. ERROR HANDLING RULES

---

# 13.1 Persistence Error Isolation

Persistence-specific exceptions SHOULD be translated into:

* domain-safe exceptions
* infrastructure-safe exceptions

---

# 13.2 Technology Leakage Forbidden

Database-specific exceptions MUST NOT leak into:

* application layer
* domain layer

---

# 13.3 Reactive Error Propagation

Reactive repository errors MUST:

* preserve reactive flows
* avoid swallowed exceptions

---

# 14. PERFORMANCE RULES

---

# 14.1 Overfetching Prevention

Repositories SHOULD avoid:

* unnecessary joins
* oversized object graphs
* excessive eager loading

---

# 14.2 Read Optimization Principle

Read-heavy scenarios SHOULD use:

* projections
* query optimization
* dedicated read models

---

# 14.3 Query Predictability

Repository queries MUST remain:

* explicit
* predictable
* observable

---

# 15. OBSERVABILITY RULES

---

# 15.1 Repository Observability

Repository operations SHOULD support:

* tracing
* metrics
* correlation IDs
* tenant tracing

---

# 15.2 Sensitive Data Protection

Repository logs MUST NOT expose:

* passwords
* tokens
* sensitive information

---

# 16. REPOSITORY NAMING RULES

---

# 16.1 Naming Convention

Repositories MUST use:

```text id="13repo13"
<EntityName>Repository
```

---

## Correct

```text id="14repo14"
AppointmentRepository
TenantRepository
UserRepository
```

---

## Forbidden

```text id="15repo15"
AppointmentDAO
UserManager
TenantPersistenceHelper
```

---

# 16.2 Query Method Naming

Query methods SHOULD remain:

* descriptive
* explicit
* intention-revealing

---

# 17. FORBIDDEN REPOSITORY ANTI-PATTERNS

---

# Forbidden

* God repositories
* Business orchestration in repositories
* Generic universal repositories
* Infrastructure leakage
* Cross aggregate mutation
* Unbounded queries
* Blocking persistence in reactive flows
* Query dumping repositories
* Repository-to-repository orchestration
* Tenant-unaware queries

---

# 18. AI IMPLEMENTATION RULES

All AI-generated repositories MUST:

* preserve aggregate boundaries
* avoid business orchestration
* enforce tenant isolation
* remain reactive-friendly
* avoid blocking operations
* avoid oversized queries
* avoid infrastructure leakage
* preserve modular boundaries
* use explicit method naming
* avoid generic repository abuse

---

# 19. REPOSITORY DESIGN CHECKLIST

Before implementing a Repository verify:

* Does it belong to an Aggregate Root?
* Is aggregate consistency preserved?
* Is tenant isolation enforced?
* Is the repository reactive-friendly?
* Are blocking operations avoided?
* Is business logic excluded?
* Are queries explicit and predictable?
* Is overfetching avoided?
* Are projections considered?
* Is infrastructure leakage avoided?
* Are transactions handled outside repositories?
* Is event publication excluded?
* Is naming consistent?
* Is observability supported?
* Are exceptions properly isolated?

---

# 20. CODECORE OFFICIAL REPOSITORY PHILOSOPHY

```text id="16repo16"
Repositories exist to persist and retrieve
aggregate state, not to become business engines
or orchestration layers.
```
