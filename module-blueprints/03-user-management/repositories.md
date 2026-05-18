# repositories.md

````md id="userrepositories01"
# User Management
## Repository Engineering
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official repository model for the User Management bounded context.

Its objectives are:

- standardize operational user persistence
- preserve tenant-safe organizational consistency
- enforce ownership traceability
- support scalable organizational structures
- preserve aggregate boundaries
- support reactive-safe persistence
- preserve observability and auditability
- guide AI-assisted implementation

---

# 2. REPOSITORY PHILOSOPHY

User Management repositories exist to:
- persist operational user aggregates
- retrieve organizational participation state
- preserve ownership consistency
- support scalable tenant-safe execution
- provide reactive organizational persistence

User repositories MUST:
- remain aggregate-oriented
- remain tenant-safe
- remain reactive
- avoid business orchestration
- preserve isolation boundaries

---

# 3. OFFICIAL USER MANAGEMENT REPOSITORIES

The User Management bounded context officially defines:

| Repository | Aggregate |
|---|---|
| UserProfileRepository | UserProfileAggregate |
| MembershipRepository | MembershipAggregate |
| ActorRepository | ActorAggregate |
| OrganizationUnitRepository | OrganizationUnitAggregate |
| OwnershipRepository | OwnershipAggregate |

---

# 4. USER PROFILE REPOSITORY

---

# 4.1 Purpose

UserProfileRepository manages:
- operational profile persistence
- profile lifecycle retrieval
- user contextual visibility
- operational user lookup

---

# 4.2 Aggregate Ownership

UserProfileRepository persists:
- UserProfileAggregate

ONLY.

---

# 4.3 Recommended Operations

Recommended operations:

```text id="userprofilerepositoryops"
save()
findById()
findByIdentityId()
findByActorId()
findByDisplayName()
findByStatus()
findByTenantId()
existsByIdentityId()
searchProfiles()
delete()
````

---

# 4.4 Profile Integrity Rules

Profile queries MUST preserve:

* tenant ownership
* operational visibility restrictions
* lifecycle consistency

---

# 4.5 Forbidden Responsibilities

UserProfileRepository MUST NOT:

* authenticate identities
* manage permissions
* orchestrate workflows
* invoke external services

---

# 5. MEMBERSHIP REPOSITORY

---

# 5.1 Purpose

MembershipRepository manages:

* tenant participation persistence
* branch participation retrieval
* membership lifecycle state
* organizational eligibility lookup

---

# 5.2 Aggregate Ownership

MembershipRepository persists:

* MembershipAggregate

ONLY.

---

# 5.3 Recommended Operations

Recommended operations:

```text id="membershiprepositoryops"
save()
findById()
findByActorId()
findByTenantId()
findByOrganizationUnitId()
findByStatus()
findActiveMemberships()
assignBranch()
removeBranch()
archiveMembership()
delete()
```

---

# 5.4 Membership Integrity Rules

Membership persistence MUST preserve:

* organizational consistency
* branch consistency
* operational eligibility

---

# 5.5 Forbidden Responsibilities

MembershipRepository MUST NOT:

* authenticate credentials
* manage authorization policies
* orchestrate workflows

---

# 6. ACTOR REPOSITORY

---

# 6.1 Purpose

ActorRepository manages:

* operational actor persistence
* actor classification retrieval
* operational participation lookup
* contextual operational visibility

---

# 6.2 Aggregate Ownership

ActorRepository persists:

* ActorAggregate

ONLY.

---

# 6.3 Recommended Operations

Recommended operations:

```text id="actorrepositoryops"
save()
findById()
findByActorType()
findByOperationalCode()
findByTenantId()
findByStatus()
findProfessionals()
findPatients()
assignClassification()
delete()
```

---

# 6.4 Actor Integrity Rules

Actor persistence MUST preserve:

* contextual operational consistency
* tenant ownership integrity

---

# 6.5 Forbidden Responsibilities

ActorRepository MUST NOT:

* authenticate identities
* manage permissions
* orchestrate workflows

---

# 7. ORGANIZATION UNIT REPOSITORY

---

# 7.1 Purpose

OrganizationUnitRepository manages:

* organizational hierarchy persistence
* branch structure retrieval
* hierarchy visibility
* organizational subdivision consistency

---

# 7.2 Aggregate Ownership

OrganizationUnitRepository persists:

* OrganizationUnitAggregate

ONLY.

---

# 7.3 Recommended Operations

Recommended operations:

```text id="organizationunitrepositoryops"
save()
findById()
findByTenantId()
findByParentUnitId()
findByUnitType()
findHierarchy()
findBranches()
archiveUnit()
validateHierarchy()
delete()
```

---

# 7.4 Organizational Integrity Rules

Organizational persistence MUST preserve:

* acyclic hierarchy rules
* tenant-scoped visibility
* structural consistency

---

# 7.5 Forbidden Responsibilities

OrganizationUnitRepository MUST NOT:

* orchestrate workflows
* manage permissions
* authenticate identities

---

# 8. OWNERSHIP REPOSITORY

---

# 8.1 Purpose

OwnershipRepository manages:

* operational ownership persistence
* ownership transfer retrieval
* ownership lifecycle history
* ownership traceability visibility

---

# 8.2 Aggregate Ownership

OwnershipRepository persists:

* OwnershipAggregate

ONLY.

---

# 8.3 Recommended Operations

Recommended operations:

```text id="ownershiprepositoryops"
save()
findById()
findByResourceId()
findByOwnerActorId()
findByOwnershipType()
findOwnershipHistory()
transferOwnership()
revokeOwnership()
archiveOwnership()
delete()
```

---

# 8.4 Ownership Integrity Rules

Ownership persistence MUST preserve:

* historical traceability
* ownership consistency
* tenant-safe propagation

---

# 8.5 Forbidden Responsibilities

OwnershipRepository MUST NOT:

* authenticate users
* manage permissions
* orchestrate workflows

---

# 9. AGGREGATE BOUNDARY RULES

---

# 9.1 Aggregate Isolation Principle

Repositories MUST persist:

* one aggregate boundary only

---

# 9.2 Cross Aggregate Persistence Forbidden

Repositories MUST NOT:

* mutate external aggregate internals directly

---

# 9.3 Coordination Principle

Cross-aggregate coordination SHOULD occur through:

* application services
* orchestration services
* domain events

---

# 10. REACTIVE PERSISTENCE RULES

---

# 10.1 Official Reactive Standard

User repositories MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 10.2 Official Persistence Strategy

Recommended persistence stack:

```text id="userpersistencestack"
Spring Data R2DBC
Reactive PostgreSQL
Reactive Redis
```

---

# 10.3 Blocking Persistence Forbidden

Forbidden:

* JDBC
* blocking ORM execution
* imperative waiting
* .block()

inside repository execution chains.

---

# 10.4 Reactive Return Types

Repositories SHOULD return:

* Mono
* Flux

ONLY.

---

# 11. MULTITENANCY RULES

---

# 11.1 Mandatory Tenant Filtering

All tenant-owned queries MUST enforce:

* tenant filtering

---

# Forbidden

```sql id="tenantblinduserquery"
SELECT * FROM memberships;
```

---

# Correct

```sql id="tenantawareuserquery"
SELECT * FROM memberships
WHERE tenant_id = :tenantId;
```

---

# 11.2 Cross Tenant Leakage Forbidden

Repositories MUST NEVER:

* expose another tenant’s organizational state
* expose another tenant’s ownership relationships

---

# 11.3 Tenant Ownership Integrity

Repository persistence MUST preserve:

* immutable tenant ownership

---

# 12. CONCURRENCY RULES

---

# 12.1 Optimistic Locking Principle

Critical operational aggregates SHOULD support:

* optimistic concurrency control

---

# 12.2 Membership Concurrency Rules

Concurrent membership mutations MUST:

* preserve organizational consistency

---

# 12.3 Ownership Concurrency Rules

Concurrent ownership transfers MUST:

* preserve traceability consistency

---

# 12.4 Organizational Concurrency Rules

Concurrent hierarchy modifications MUST:

* preserve acyclic organizational structures

---

# 13. SECURITY RULES

---

# 13.1 Isolation Protection Principle

Repositories MUST preserve:

* strict tenant isolation

---

# 13.2 Sensitive Exposure Restrictions

Sensitive operational metadata SHOULD:

* remain protected
* remain access-controlled

---

# 13.3 Secure Persistence Principle

Critical operational persistence MUST:

* remain auditable
* remain traceable
* remain tenant-aware

---

# 14. OBSERVABILITY RULES

---

# 14.1 Repository Traceability

Critical repository operations SHOULD expose:

* correlation IDs
* trace IDs
* tenant-aware diagnostics
* actor metadata

---

# 14.2 Organizational Visibility

Membership and hierarchy persistence SHOULD remain:

* observable
* diagnosable

---

# 14.3 Reactive Visibility Principle

Reactive persistence failures MUST remain:

* traceable
* diagnosable

---

# 15. AUDITING RULES

---

# 15.1 Auditability Principle

Critical repository mutations SHOULD remain:

* auditable
* historically traceable

---

# 15.2 Mandatory Audit Operations

The following SHOULD generate audit records:

* User registration
* Membership creation
* Membership suspension
* Ownership assignment
* Ownership transfer
* Branch assignment
* Organizational restructuring

---

# 16. PERFORMANCE RULES

---

# 16.1 Query Optimization Principle

Organizational queries MUST remain:

* indexed
* scalable
* low-latency

---

# 16.2 Lightweight Persistence Principle

Repositories SHOULD avoid:

* oversized graph loading
* unnecessary joins
* blocking hydration

---

# 16.3 Ownership Lookup Principle

Ownership queries SHOULD support:

* low-latency resolution
* scalable distributed lookup

---

# 17. FAILURE HANDLING RULES

---

# 17.1 Failure Isolation Principle

Repository failures SHOULD remain:

* isolated
* observable
* diagnosable

---

# 17.2 Retry Safety Principle

Retries MUST preserve:

* organizational consistency
* ownership consistency
* membership integrity

---

# 17.3 Persistence Safety Principle

Failed persistence MUST NOT:

* corrupt organizational structures
* corrupt ownership history

---

# 18. STORAGE STRATEGY RULES

---

# 18.1 Official Storage Technologies

Recommended storage technologies:

| Technology | Purpose                  |
| ---------- | ------------------------ |
| PostgreSQL | Primary persistence      |
| Redis      | Operational acceleration |
| Flyway     | Schema migration         |
| R2DBC      | Reactive persistence     |

---

# 18.2 Redis Usage Strategy

Redis MAY support:

* membership lookup acceleration
* ownership resolution acceleration
* hierarchy visibility caching
* operational eligibility caching

---

# 18.3 Source of Truth Principle

PostgreSQL remains:

* the primary source of truth

unless explicitly defined otherwise.

---

# 19. FORBIDDEN REPOSITORY ANTI-PATTERNS

---

# Forbidden

* Tenant-blind repositories
* Cross-tenant ownership leakage
* Business workflow orchestration
* Blocking JDBC access
* Cross-aggregate mutation
* Shared mutable organizational state
* Authentication ownership
* External API invocation
* Oversized aggregate hydration
* Hidden mutable persistence state

---

# 20. AI IMPLEMENTATION RULES

All AI-generated User repositories MUST:

* remain fully reactive
* preserve tenant isolation
* preserve aggregate boundaries
* avoid business orchestration
* avoid blocking execution
* support optimistic locking
* preserve ownership consistency
* preserve organizational scalability
* preserve observability
* preserve tenant-safe propagation

---

# 21. CODECORE USER REPOSITORY PHILOSOPHY

```text id="userrepositoryphilosophy"
User Management repositories exist to provide
reactive, scalable and tenant-safe
organizational persistence
through isolated operational participation boundaries,
non-blocking ownership-aware data access
and consistency-preserving organizational orchestration.
```

```
```
