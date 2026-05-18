# 04-authorization-management/repositories.md

````md id="r6x2wp"
# Authorization Management Repositories

## 1. Introduction

This document defines the repository contracts and persistence responsibilities of the Authorization Management module.

Repositories are responsible for:

- Aggregate persistence
- Authorization data retrieval
- Permission resolution
- Policy querying
- Audit persistence
- Tenant-scoped access enforcement

The repository layer is designed following:

- Domain-Driven Design (DDD)
- Hexagonal Architecture
- Repository Pattern
- CQRS-friendly principles
- Multi-tenant SaaS security architecture

---

# 2. Repository Design Principles

| Principle | Description |
|---|---|
| Aggregate-oriented | One repository per aggregate root |
| Persistence ignorance | Domain isolated from infrastructure |
| Tenant-aware | Mandatory tenant isolation |
| Security-first | Prevent unauthorized access |
| Read optimization | CQRS-compatible querying |
| Explicit querying | Avoid ambiguous retrieval |
| Immutable auditability | Preserve security evidence |

---

# 3. Repository Overview

| Repository | Responsibility |
|---|---|
| RoleRepository | Role aggregate persistence |
| PermissionRepository | Permission persistence |
| PolicyRepository | Authorization policy persistence |
| AuthorizationDecisionRepository | Authorization audit persistence |
| PrivilegeRepository | Privilege persistence |
| PermissionAssignmentRepository | Assignment tracking |
| SecurityConstraintRepository | Security restriction persistence |
| AuthorizationAuditRepository | Security audit querying |
| AuthorizationCacheRepository | Cached authorization data |
| EffectivePermissionProjectionRepository | Optimized permission reads |

---

# 4. RoleRepository

## Purpose

Manages persistence of the `RoleAggregate`.

---

## Responsibilities

- Save roles
- Load roles
- Search tenant roles
- Validate uniqueness
- Resolve role assignments
- Support role projections

---

## Example Contract

```java id="k8v4ty"
public interface RoleRepository {

    Mono<Role> save(Role role);

    Mono<Role> findById(RoleId roleId);

    Mono<Role> findByTenantIdAndName(
        TenantId tenantId,
        RoleName roleName
    );

    Flux<Role> findAllByTenantId(
        TenantId tenantId
    );

    Mono<Boolean> existsByTenantIdAndName(
        TenantId tenantId,
        RoleName roleName
    );

    Mono<Void> deactivate(RoleId roleId);
}
````

---

## Important Constraints

| Constraint                 | Description               |
| -------------------------- | ------------------------- |
| Tenant isolation mandatory | No cross-tenant retrieval |
| Soft delete preferred      | Preserve auditability     |
| Optimized lookup required  | Authorization performance |

---

# 5. PermissionRepository

## Purpose

Manages atomic permission persistence.

---

## Responsibilities

* Store permissions
* Search permissions
* Validate existence
* Resolve permission metadata

---

## Example Contract

```java id="f3n7xr"
public interface PermissionRepository {

    Mono<Permission> save(Permission permission);

    Mono<Permission> findByCode(
        PermissionCode code
    );

    Flux<Permission> findByResource(
        ResourceName resource
    );

    Mono<Boolean> existsByCode(
        PermissionCode code
    );
}
```

---

## Characteristics

| Characteristic    | Description               |
| ----------------- | ------------------------- |
| Mostly immutable  | Stable security structure |
| Globally scoped   | Shared across tenants     |
| Frequently cached | Performance optimization  |

---

# 6. PolicyRepository

## Purpose

Manages authorization policy persistence.

---

## Responsibilities

* Persist policies
* Load active policies
* Query policies by resource/action
* Support runtime policy evaluation

---

## Example Contract

```java id="m5w1zk"
public interface PolicyRepository {

    Mono<AuthorizationPolicy> save(
        AuthorizationPolicy policy
    );

    Flux<AuthorizationPolicy> findActivePolicies(
        TenantId tenantId,
        ResourceName resource,
        ActionName action
    );

    Mono<Void> activate(
        PolicyId policyId
    );

    Mono<Void> deactivate(
        PolicyId policyId
    );
}
```

---

## Performance Considerations

Policies are heavily read during runtime authorization.

Recommended:

* Redis caching
* In-memory indexing
* Priority-based retrieval

---

# 7. AuthorizationDecisionRepository

## Purpose

Stores authorization evaluation results.

Supports:

* Auditing
* Security analytics
* Compliance
* Forensics

---

## Example Contract

```java id="v2x8tp"
public interface AuthorizationDecisionRepository {

    Mono<AuthorizationDecision> save(
        AuthorizationDecision decision
    );

    Flux<AuthorizationDecision> findDeniedByTenant(
        TenantId tenantId
    );

    Flux<AuthorizationDecision> findByUser(
        UserId userId
    );
}
```

---

## Characteristics

| Characteristic           | Description         |
| ------------------------ | ------------------- |
| High-volume writes       | Runtime evaluations |
| Append-only preferred    | Immutable evidence  |
| Partitioning recommended | Scalability         |

---

# 8. PrivilegeRepository

## Purpose

Persists grouped authorization privileges.

---

## Responsibilities

* Store privileges
* Resolve privilege composition
* Query tenant privileges

---

## Example Contract

```java id="q7k4mn"
public interface PrivilegeRepository {

    Mono<Privilege> save(
        Privilege privilege
    );

    Mono<Privilege> findByCode(
        String code
    );

    Flux<Privilege> findByTenant(
        TenantId tenantId
    );
}
```

---

# 9. PermissionAssignmentRepository

## Purpose

Tracks role-permission assignments.

Useful for:

* Auditing
* Historical tracking
* Analytics
* Permission lineage

---

## Example Contract

```java id="y1v6rf"
public interface PermissionAssignmentRepository {

    Mono<PermissionAssignment> save(
        PermissionAssignment assignment
    );

    Flux<PermissionAssignment> findByRole(
        RoleId roleId
    );

    Flux<PermissionAssignment> findByPermission(
        PermissionCode permissionCode
    );
}
```

---

# 10. SecurityConstraintRepository

## Purpose

Stores platform-level security restrictions.

---

## Examples

```text id="u9m3xt"
- Reserved permissions
- Role hierarchy restrictions
- Tenant permission caps
- Immutable role rules
```

---

## Example Contract

```java id="d4k8wp"
public interface SecurityConstraintRepository {

    Flux<SecurityConstraint> findActiveConstraints();

    Mono<SecurityConstraint> findByCode(
        String code
    );
}
```

---

# 11. AuthorizationAuditRepository

## Purpose

Supports optimized audit querying.

Usually separated from transactional repositories.

---

## Responsibilities

* Audit search
* Security reporting
* Compliance exports
* Forensic investigation

---

## Example Contract

```java id="p2x7vt"
public interface AuthorizationAuditRepository {

    Flux<AuthorizationAuditView> search(
        AuthorizationAuditCriteria criteria
    );

    Mono<Long> countDeniedRequests(
        TenantId tenantId
    );
}
```

---

## Optimization Strategies

| Strategy          | Purpose                   |
| ----------------- | ------------------------- |
| CQRS projections  | Fast reads                |
| Search indexing   | Efficient filtering       |
| Time partitioning | Large-scale audit support |

---

# 12. AuthorizationCacheRepository

## Purpose

Provides distributed authorization caching.

---

## Cached Elements

```text id="w8n1za"
- Effective permissions
- Active policies
- Permission snapshots
- Role metadata
```

---

## Example Contract

```java id="s5r9pk"
public interface AuthorizationCacheRepository {

    Mono<PermissionSet> getPermissions(
        UserId userId,
        TenantId tenantId
    );

    Mono<Void> putPermissions(
        UserId userId,
        TenantId tenantId,
        PermissionSet permissions
    );

    Mono<Void> invalidateTenant(
        TenantId tenantId
    );
}
```

---

## Recommended Technologies

| Technology | Suitability           |
| ---------- | --------------------- |
| Redis      | Distributed caching   |
| Caffeine   | Local in-memory cache |
| Hazelcast  | Clustered caching     |

---

# 13. EffectivePermissionProjectionRepository

## Purpose

Provides optimized read models for runtime authorization.

CQRS-oriented repository.

---

## Responsibilities

* Runtime permission resolution
* Precomputed permission access
* JWT snapshot support

---

## Example Contract

```java id="x6t2wr"
public interface EffectivePermissionProjectionRepository {

    Mono<PermissionSet> resolveEffectivePermissions(
        UserId userId,
        TenantId tenantId
    );
}
```

---

## Optimization Goals

* Minimize joins
* Reduce runtime computation
* Enable low-latency authorization

---

# 14. Multi-Tenant Repository Rules

## Mandatory Tenant Filtering

All tenant-scoped repositories must enforce:

```text id="n4v7zy"
WHERE tenant_id = :tenantId
```

---

## Forbidden Behavior

```text id="g1x8rp"
SELECT * FROM roles
```

without tenant filtering is forbidden.

---

# 15. Persistence Strategies

| Aggregate                      | Strategy                   |
| ------------------------------ | -------------------------- |
| RoleAggregate                  | Relational persistence     |
| PermissionAggregate            | Mostly static relational   |
| PolicyAggregate                | Relational + cache         |
| AuthorizationDecisionAggregate | Append-only/event-oriented |

---

# 16. Recommended Database Technologies

| Technology    | Use Case                      |
| ------------- | ----------------------------- |
| PostgreSQL    | Core transactional data       |
| Redis         | Distributed caching           |
| Elasticsearch | Audit search                  |
| Kafka         | Event persistence/integration |

---

# 17. CQRS Considerations

Recommended separation:

## Write Side

* Aggregate consistency
* Security validation
* Transactional persistence

---

## Read Side

* Optimized projections
* Cached permission views
* Audit dashboards

---

# 18. Reactive Repository Considerations

Reactive support strongly recommended.

---

## Example

```java id="k3m9vz"
Mono<Role>
Flux<Permission>
```

---

## Benefits

| Benefit            | Description      |
| ------------------ | ---------------- |
| Non-blocking IO    | Scalability      |
| High concurrency   | Reactive systems |
| Lower thread usage | Efficiency       |

---

# 19. Transaction Management

## Strong Consistency Required

| Operation             | Reason                    |
| --------------------- | ------------------------- |
| Permission assignment | Security integrity        |
| Role updates          | Authorization correctness |
| Policy activation     | Runtime consistency       |

---

## Eventual Consistency Acceptable

| Operation             | Reason      |
| --------------------- | ----------- |
| Audit projections     | Reporting   |
| Analytics views       | Monitoring  |
| Cache synchronization | Performance |

---

# 20. Repository Security Requirements

## Deny Cross-Tenant Access

Mandatory validation at repository level.

---

## Prevent Unauthorized Enumeration

Example:

```text id="r7k5tw"
Users cannot enumerate
roles from other tenants
```

---

## Immutable Audit Persistence

Audit evidence must not be mutable.

---

# 21. Performance Considerations

Critical performance areas:

| Area                            | Optimization            |
| ------------------------------- | ----------------------- |
| Permission lookup               | Caching                 |
| Policy evaluation               | Indexing                |
| Audit search                    | Partitioning            |
| Effective permission resolution | Precomputed projections |

---

# 22. Indexing Recommendations

## Recommended Indexes

| Table                   | Index                         |
| ----------------------- | ----------------------------- |
| roles                   | tenant_id + name              |
| permissions             | code                          |
| policies                | tenant_id + resource + action |
| authorization_decisions | tenant_id + evaluated_at      |

---

# 23. Soft Delete Strategy

Recommended for:

| Entity     | Reason              |
| ---------- | ------------------- |
| Roles      | Preserve references |
| Policies   | Auditability        |
| Privileges | Historical tracking |

---

## Example

```text id="f6x2mq"
active = false
```

instead of physical deletion.

---

# 24. Event Sourcing Considerations

Future-compatible repositories may support:

* Event replay
* Aggregate reconstruction
* Temporal authorization analysis
* Historical permission reconstruction

---

# 25. Failure Handling

## Fail Closed Principle

If repository authorization validation fails:

```text id="v9n4pk"
ACCESS = DENIED
```

---

## Recommended Strategies

| Failure                  | Strategy              |
| ------------------------ | --------------------- |
| Cache unavailable        | Recompute permissions |
| DB timeout               | Fail closed           |
| Projection inconsistency | Rebuild projection    |

---

# 26. Distributed System Considerations

Repositories must support:

* Horizontal scaling
* Distributed transactions avoidance
* Eventual consistency
* Distributed caching
* Multi-region deployments

---

# 27. Future Repository Extensions

Future repositories may include:

* DelegatedAccessRepository
* TemporaryPermissionRepository
* RiskEvaluationRepository
* DeviceTrustRepository
* ExternalPolicyProviderRepository

---

# 28. Summary

The Authorization Management repositories provide:

* Secure aggregate persistence
* Strong tenant isolation
* High-performance permission resolution
* Distributed authorization support
* Reactive scalability
* Enterprise-grade audit persistence
* CQRS-compatible read optimization

These repositories form the persistence backbone of the authorization ecosystem.

```
```
