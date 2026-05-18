# Entity Standards

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Entity Standards for CodeCore.

Its objectives are:

* standardize entity modeling
* prevent entity abuse
* preserve domain consistency
* enforce lifecycle ownership
* establish identity rules
* guide AI-assisted modeling
* maintain modular integrity
* ensure scalability and auditability

This specification is mandatory for:

* domain modeling
* persistence modeling
* aggregate design
* repository implementation
* API contracts
* AI-generated code

---

# 2. ENTITY PHILOSOPHY

---

## 2.1 Official Definition

An Entity is:

```text id="9lfj3d"
A domain object whose identity
remains consistent through time
regardless of attribute changes.
```

---

## 2.2 Core Principle

Entities exist ONLY when:

* identity matters
* lifecycle matters
* state transitions matter
* traceability matters

---

## 2.3 Forbidden Entity Creation

Do NOT create entities for:

* simple data holders
* immutable descriptors
* temporary structures
* simple grouped values

Use Value Objects instead.

---

# 3. ENTITY DESIGN PRINCIPLES

---

# 3.1 Explicit Identity Principle

Every Entity MUST have:

* explicit identity
* stable identity
* immutable identity after creation

---

# 3.2 Lifecycle Principle

Entities MUST own:

* meaningful lifecycle
* meaningful state transitions
* business relevance over time

---

# 3.3 Behavioral Principle

Entities SHOULD contain:

* domain behavior
* validation rules
* transition logic
* invariant enforcement

Entities SHOULD NOT behave as:

* passive data containers
* anemic models
* DTO replacements

---

# 3.4 Encapsulation Principle

Entities MUST protect:

* internal consistency
* lifecycle validity
* invariant integrity

---

# 3.5 Minimal Exposure Principle

Entities SHOULD expose:

* explicit operations
* meaningful behavior

Avoid:

* unrestricted mutation
* public mutable internals
* excessive getters/setters

---

# 4. ENTITY CLASSIFICATION

---

# 4.1 Aggregate Root Entity

Entity that:

* defines aggregate boundary
* controls consistency
* owns lifecycle
* protects invariants

---

# 4.2 Internal Entity

Entity that:

* exists inside aggregate boundary
* depends on aggregate lifecycle
* cannot exist independently

---

# 4.3 Reference Entity

Lightweight entity referenced only by ID.

Used for:

* cross aggregate references
* external ownership references

---

# 5. ENTITY IDENTITY RULES

---

# 5.1 Identity Immutability

Entity identifiers MUST NEVER change after creation.

---

# 5.2 Identity Type Strategy

Preferred strategy:

| Scenario                      | Recommended ID |
| ----------------------------- | -------------- |
| External/Public entities      | UUID           |
| High-volume internal entities | BIGINT         |
| Event identifiers             | UUID           |
| Distributed operations        | UUID           |

---

# 5.3 Public Exposure Rule

Public APIs SHOULD expose:

* UUIDs
* opaque identifiers

Avoid exposing:

* sequential database IDs externally

---

# 5.4 Equality Rules

Entity equality MUST be identity-based.

NOT attribute-based.

---

## Correct

```text id="7k0jlwm"
entityA.id == entityB.id
```

---

## Forbidden

```text id="y8jlwm"
entityA.email == entityB.email
```

unless email itself is the identity.

---

# 6. ENTITY MUTABILITY RULES

---

# 6.1 Controlled Mutation Principle

Entities MUST mutate through:

* explicit methods
* validated transitions
* domain-safe operations

---

## Correct

```text id="9jlwm0"
appointment.confirm()
user.lock()
subscription.activate()
```

---

## Forbidden

```text id="4jlwm1"
entity.setStatus()
entity.setAnything()
```

---

# 6.2 Immutable Identity Rule

Identity fields MUST remain immutable.

---

# 6.3 Immutable Critical Fields

Critical ownership fields SHOULD remain immutable.

Examples:

* tenant_id
* created_at
* created_by

---

# 7. ENTITY STATE RULES

---

# 7.1 Explicit States

Entities with lifecycle complexity MUST define:

* explicit states
* valid transitions
* forbidden transitions

---

# 7.2 State Transition Methods

State changes MUST occur through:

* intention-revealing methods

---

## Correct

```text id="3jlwm2"
appointment.cancel()
appointment.complete()
```

---

## Forbidden

```text id="4jlwm3"
appointment.setStatus(COMPLETED)
```

---

# 7.3 Invalid State Protection

Invalid transitions MUST:

* throw domain exceptions
* preserve consistency

---

# 8. ENTITY TENANT OWNERSHIP RULES

---

# 8.1 Tenant Ownership Principle

Tenant-owned entities MUST contain:

```text id="7jlwm4"
tenant_id
```

---

# 8.2 Tenant Immutability

Tenant ownership MUST NOT change after creation.

---

# 8.3 Cross Tenant Isolation

Entities MUST NEVER:

* reference entities from other tenants
* allow cross-tenant mutation

---

# 8.4 Tenant Validation

Tenant ownership MUST be validated:

* before persistence
* before updates
* before access

---

# 9. AUDITABILITY RULES

---

# 9.1 Mandatory Auditability

All persistent business entities SHOULD contain:

```text id="9jlwm5"
created_at
updated_at
created_by
updated_by
```

---

# 9.2 Soft Delete Strategy

Preferred deletion strategy:

```text id="1jlwm6"
deleted
deleted_at
deleted_by
```

---

# 9.3 Hard Delete Restrictions

Hard delete SHOULD be avoided unless:

* legally required
* technically necessary
* explicitly justified

---

# 9.4 Lifecycle Traceability

Important lifecycle transitions SHOULD generate:

* audit logs
* domain events

---

# 10. ENTITY RELATIONSHIP RULES

---

# 10.1 Aggregate Boundary Respect

Entities MUST respect aggregate boundaries.

---

# 10.2 Cross Aggregate References

Cross aggregate references SHOULD use:

* IDs
* lightweight references

NOT mutable entity graphs.

---

## Correct

```text id="2jlwm7"
appointment.actor_id
```

---

## Forbidden

```text id="3jlwm8"
appointment.actor.fullObjectReference
```

---

# 10.3 Bidirectional Relationships

Bidirectional relationships SHOULD be minimized.

---

# 10.4 Large Collection Rule

Large collections SHOULD NOT live directly inside entities.

Use:

* projections
* paginated queries
* specialized read models

---

# 11. ENTITY PERSISTENCE RULES

---

# 11.1 Persistence Transparency Principle

Domain entities MUST NOT:

* depend on repositories
* depend on persistence frameworks
* contain infrastructure logic

---

# 11.2 Framework Isolation Principle

Entities MUST remain independent from:

* Spring
* HTTP
* Redis
* Messaging frameworks

---

# 11.3 Reactive Compatibility

Entities MUST remain:

* lightweight
* serialization-safe
* reactive-friendly

---

# 12. ENTITY CONSTRUCTION RULES

---

# 12.1 Explicit Creation

Entity creation MUST occur through:

* constructors
* factory methods
* aggregate operations

---

# 12.2 Invalid Entity Prevention

It MUST be impossible to create invalid entities.

---

# 12.3 Required Data Principle

Required data MUST be enforced:

* at construction time
* before persistence

---

# 13. ENTITY SIZE RULES

---

# 13.1 Small Entity Principle

Entities SHOULD remain:

* cohesive
* focused
* lightweight

---

# 13.2 God Entity Anti-Pattern

Forbidden:

* oversized entities
* unrelated responsibilities
* excessive fields
* excessive nested structures

---

# 13.3 Read Optimization Rule

Heavy read requirements SHOULD use:

* projections
* DTOs
* specialized query models

NOT oversized entities.

---

# 14. ENTITY NAMING RULES

---

# 14.1 Naming Style

Entities MUST use:

* singular nouns
* business language
* ubiquitous language consistency

---

## Correct

```text id="4jlwm9"
User
Appointment
Tenant
Notification
```

---

## Forbidden

```text id="5jlwm0"
UsersEntity
AppointmentData
TenantTable
```

---

# 14.2 Business Agnostic Rule

Core entities MUST remain:

* business-neutral
* reusable
* universal

---

## Forbidden Inside Core

```text id="6jlwm1"
DentalPatient
VeterinaryAppointment
PsychologyDoctor
```

---

# 15. ENTITY EVENTS RULES

---

# 15.1 Event Ownership

Entities MAY generate:

* domain events
* lifecycle events

through Aggregate Root coordination.

---

# 15.2 Event Timing

Events MUST represent:

* completed state changes

NOT commands.

---

# 16. CONCURRENCY RULES

---

# 16.1 Consistency Protection

Entities MUST preserve:

* consistency under concurrent access
* invariant integrity

---

# 16.2 Optimistic Locking

Critical entities SHOULD support:

* optimistic locking
* version tracking

when concurrency risk exists.

---

# 16.3 Reactive Safety

Entity operations MUST remain:

* non-blocking friendly
* asynchronous compatible

---

# 17. FORBIDDEN ENTITY ANTI-PATTERNS

---

# Forbidden

* Anemic entities
* God entities
* Mutable public fields
* Infrastructure-aware entities
* Framework-coupled entities
* Cross aggregate mutable graphs
* Excessive inheritance hierarchies
* Entity-as-DTO design
* Shared mutable entities
* Massive nested collections

---

# 18. AI IMPLEMENTATION RULES

All AI-generated entities MUST:

* preserve aggregate boundaries
* maintain explicit identity
* avoid unnecessary mutability
* avoid infrastructure leakage
* remain business agnostic
* preserve tenant isolation
* preserve auditability
* remain reactive-friendly
* avoid oversized structures

---

# 19. ENTITY DESIGN CHECKLIST

Before implementing an Entity verify:

* Does identity truly matter?
* Does lifecycle truly matter?
* Could this be a Value Object instead?
* Is the entity small and cohesive?
* Are transitions explicit?
* Is tenant ownership enforced?
* Is auditability preserved?
* Is mutability controlled?
* Are aggregate boundaries respected?
* Is infrastructure leakage avoided?
* Is the entity reactive-friendly?
* Is naming consistent with the glossary?
* Is the entity business agnostic?
* Are invalid states impossible?
* Is concurrency considered?

---

# 20. CODECORE OFFICIAL ENTITY PHILOSOPHY

```text id="7jlwm2"
Entities exist to model meaningful identity
and lifecycle consistency, not to mirror
database tables.
```
