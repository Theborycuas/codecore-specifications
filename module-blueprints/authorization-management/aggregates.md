# 04-authorization-management/aggregates.md

````md id="o4s7ka"
# Authorization Management Aggregates

## 1. Introduction

This document defines the aggregates of the Authorization Management module.

The aggregates represent the main transactional consistency boundaries inside the domain and encapsulate:

- Business invariants
- Authorization rules
- Permission integrity
- Tenant isolation rules
- Security validation logic

The design follows:

- Domain-Driven Design (DDD)
- Aggregate consistency principles
- High cohesion
- Low coupling
- Multi-tenant SaaS architecture

---

# 2. Aggregate Overview

| Aggregate | Responsibility |
|---|---|
| RoleAggregate | Manages roles and assigned permissions |
| PermissionAggregate | Manages atomic permissions |
| PolicyAggregate | Manages authorization policies |
| AccessContextAggregate | Encapsulates runtime authorization context |
| AuthorizationDecisionAggregate | Represents evaluated authorization decisions |
| PrivilegeAggregate | Manages higher-level privilege groupings |

---

# 3. RoleAggregate

## Purpose

The RoleAggregate manages:

- Role lifecycle
- Permission assignments
- Tenant ownership
- Role hierarchy restrictions
- Security boundaries

This is the most important aggregate in the authorization module.

---

## Aggregate Root

```text id="t8h0mg"
Role
````

---

## Responsibilities

* Create roles
* Update roles
* Assign permissions
* Remove permissions
* Validate permission assignments
* Prevent invalid privilege escalation
* Validate tenant ownership
* Enforce immutable system role rules

---

## Invariants

The aggregate enforces:

| Invariant                      | Description                                     |
| ------------------------------ | ----------------------------------------------- |
| Role name uniqueness           | Unique per tenant                               |
| Tenant isolation               | Roles belong to one tenant                      |
| System role immutability       | Protected platform roles cannot be modified     |
| No duplicated permissions      | A role cannot contain duplicates                |
| Permission validity            | Only valid permissions may be assigned          |
| Privilege boundary enforcement | Tenant admins cannot exceed allowed permissions |

---

## Example Structure

```text id="z6xqpc"
RoleAggregate
│
├── Role (Root)
├── AssignedPermissions
├── RoleMetadata
└── SecurityConstraints
```

---

## Example

```text id="v3xh8o"
Role: PSYCHOLOGIST

Permissions:
- CREATE_PATIENT
- UPDATE_PATIENT
- APPLY_TEST
- VIEW_MEDICAL_RECORD
```

---

## Important Behaviors

### assignPermission()

Validates:

* Tenant boundaries
* Permission existence
* Duplicate assignment prevention
* Privilege escalation restrictions

---

### revokePermission()

Validates:

* Critical system permissions
* Minimum required role policies

---

### deactivateRole()

Prevents deletion when:

* Users still depend on the role
* System policies require the role

---

# 4. PermissionAggregate

## Purpose

The PermissionAggregate manages atomic permissions used throughout the platform.

Permissions represent the smallest unit of authorization.

---

## Aggregate Root

```text id="mx4v1w"
Permission
```

---

## Responsibilities

* Create permissions
* Categorize permissions
* Maintain permission metadata
* Maintain permission scopes
* Validate permission uniqueness

---

## Invariants

| Invariant                  | Description                            |
| -------------------------- | -------------------------------------- |
| Permission code uniqueness | Globally unique                        |
| Immutable core permissions | Critical permissions cannot be removed |
| Atomicity                  | Permissions represent one action only  |
| Scope consistency          | Scope must be valid                    |

---

## Example

```text id="4p6h2y"
CREATE_PATIENT
UPDATE_PATIENT
DELETE_PATIENT
EXPORT_PATIENT
```

---

## Aggregate Structure

```text id="e7o3lh"
PermissionAggregate
│
├── Permission (Root)
├── PermissionMetadata
└── PermissionScope
```

---

# 5. PolicyAggregate

## Purpose

The PolicyAggregate manages dynamic authorization rules.

Policies extend RBAC capabilities with business conditions.

---

## Aggregate Root

```text id="0l7dse"
AuthorizationPolicy
```

---

## Responsibilities

* Create policies
* Activate/deactivate policies
* Define rule conditions
* Associate policies to resources/actions
* Evaluate policy applicability

---

## Examples

```text id="z5k9wq"
A psychologist cannot modify
a closed medical record.
```

---

## Invariants

| Invariant                | Description                               |
| ------------------------ | ----------------------------------------- |
| Policy consistency       | Rules must be syntactically valid         |
| Policy scope validity    | Policies must target valid resources      |
| Tenant isolation         | Tenant policies cannot affect others      |
| Deterministic evaluation | Policies must return predictable outcomes |

---

## Aggregate Structure

```text id="y4a1pe"
PolicyAggregate
│
├── AuthorizationPolicy (Root)
├── PolicyRules
├── PolicyConditions
└── PolicyMetadata
```

---

# 6. AccessContextAggregate

## Purpose

Encapsulates all runtime authorization context required to evaluate access.

This aggregate is usually transient/runtime-oriented.

---

## Aggregate Root

```text id="jc0q7b"
AccessContext
```

---

## Responsibilities

* Encapsulate user context
* Encapsulate tenant context
* Encapsulate request metadata
* Provide evaluation-ready context

---

## Included Data

```text id="jv5m8r"
- User ID
- Tenant ID
- Roles
- Permissions
- Resource
- Action
- IP
- Device
- Session
- Ownership information
```

---

## Aggregate Structure

```text id="nl2t8f"
AccessContextAggregate
│
├── AccessContext (Root)
├── UserContext
├── TenantContext
├── ResourceContext
└── SecurityContext
```

---

# 7. AuthorizationDecisionAggregate

## Purpose

Represents the result of an authorization evaluation.

Useful for:

* Audit logging
* Security analytics
* Compliance
* Distributed authorization tracking

---

## Aggregate Root

```text id="s8f4tm"
AuthorizationDecision
```

---

## Responsibilities

* Store evaluation results
* Track denial reasons
* Persist security evidence
* Track evaluated policies

---

## Decision Types

```text id="c5w7pa"
ALLOW
DENY
CONDITIONAL_ALLOW
```

---

## Aggregate Structure

```text id="v8m2dc"
AuthorizationDecisionAggregate
│
├── AuthorizationDecision (Root)
├── EvaluatedPolicies
├── EvaluationMetadata
└── DenialReasons
```

---

## Example

```text id="r7n3bf"
Decision: DENY

Reason:
User lacks permission:
DELETE_MEDICAL_RECORD
```

---

# 8. PrivilegeAggregate

## Purpose

Represents grouped higher-level security capabilities.

Privileges are broader than permissions.

They are useful for:

* Security templates
* Enterprise plans
* Delegated administration
* Subscription-based authorization

---

## Aggregate Root

```text id="n4u2qy"
Privilege
```

---

## Example

```text id="w6f9ak"
CLINICAL_MANAGEMENT
```

May internally include:

```text id="o8t1vr"
CREATE_PATIENT
UPDATE_PATIENT
VIEW_PATIENT
CREATE_MEDICAL_RECORD
```

---

## Invariants

| Invariant                       | Description                          |
| ------------------------------- | ------------------------------------ |
| No cyclic privilege composition | Prevent recursive privilege loops    |
| Permission consistency          | All permissions must exist           |
| Tenant restriction              | Privileges respect tenant boundaries |

---

# 9. Aggregate Relationships

```text id="p7e4lx"
RoleAggregate
    ├── uses -> PermissionAggregate
    ├── constrained by -> PolicyAggregate
    └── evaluated through -> AccessContextAggregate

AuthorizationDecisionAggregate
    └── produced by Authorization Engine
```

---

# 10. Aggregate Transaction Boundaries

## Strong Consistency Required

| Aggregate           | Reason                    |
| ------------------- | ------------------------- |
| RoleAggregate       | Permission integrity      |
| PermissionAggregate | Authorization correctness |
| PolicyAggregate     | Security enforcement      |

---

## Eventual Consistency Acceptable

| Aggregate                      | Reason                 |
| ------------------------------ | ---------------------- |
| AuthorizationDecisionAggregate | Audit/history          |
| AccessContextAggregate         | Runtime ephemeral data |

---

# 11. Aggregate Design Principles

The aggregates follow these principles:

## Small Transactional Boundaries

Avoid oversized aggregates.

---

## Security-Critical Validation

All sensitive rules stay inside aggregates.

---

## Encapsulation

No external component may directly manipulate permissions without aggregate validation.

---

## Tenant Isolation

Tenant boundary validation occurs at aggregate level.

---

# 12. Aggregate Lifecycle Considerations

## Roles

Rarely deleted physically.

Preferred approach:

```text id="f5h9zd"
SOFT DELETE
```

or:

```text id="i6u2mc"
DEACTIVATED
```

---

## Permissions

Usually immutable after creation.

---

## Policies

May evolve dynamically.

Require versioning support.

---

# 13. Event Emission

Aggregates emit domain events.

Examples:

| Aggregate                      | Events                          |
| ------------------------------ | ------------------------------- |
| RoleAggregate                  | RoleCreated, PermissionAssigned |
| PermissionAggregate            | PermissionCreated               |
| PolicyAggregate                | PolicyActivated                 |
| AuthorizationDecisionAggregate | AccessDenied                    |

---

# 14. Scalability Considerations

The aggregates are designed for:

* Distributed systems
* Reactive applications
* Horizontal scaling
* Cached authorization
* High throughput authorization evaluation

Strategies include:

* Read models
* Permission snapshots
* Redis caching
* CQRS separation
* Distributed invalidation

---

# 15. Security-Critical Aggregate Rules

The module enforces:

## No Cross-Tenant Aggregate Access

Example:

```text id="w0m3he"
Tenant A cannot modify
Tenant B roles.
```

---

## No Unauthorized Permission Grants

Example:

```text id="f4d9ko"
Tenant Admin cannot assign:
SUPER_ADMIN
```

---

## Immutable Core Roles

Examples:

```text id="j7r2tp"
SUPER_ADMIN
SYSTEM_AUDITOR
```

cannot be modified by tenant-level users.

---

# 16. Future Evolution

Future aggregates may include:

* DelegatedAccessAggregate
* TemporaryPermissionAggregate
* ApprovalWorkflowAggregate
* ExternalPolicyProviderAggregate
* RiskBasedAuthorizationAggregate

---

# 17. Summary

The Authorization Management module aggregates provide:

* Secure authorization consistency boundaries
* Multi-tenant protection
* Fine-grained permission management
* Dynamic policy enforcement
* Scalable authorization architecture
* Enterprise-grade security validation

These aggregates form the core transactional backbone of the authorization domain.

```
```
