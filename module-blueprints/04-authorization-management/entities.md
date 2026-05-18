# 04-authorization-management/entities.md

````md id="g8k3pv"
# Authorization Management Entities

## 1. Introduction

This document defines the entities of the Authorization Management module.

Entities represent domain objects that:

- Have identity
- Maintain lifecycle continuity
- Encapsulate business behavior
- Participate in authorization workflows
- Enforce security rules

The entities are designed following:

- Domain-Driven Design (DDD)
- Hexagonal Architecture
- Multi-tenant SaaS principles
- Secure authorization modeling

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| Role | Represents a collection of permissions |
| Permission | Represents an atomic authorization action |
| AuthorizationPolicy | Represents dynamic security rules |
| Privilege | Represents grouped capabilities |
| AuthorizationDecision | Represents access evaluation result |
| AccessContext | Represents runtime authorization context |
| PermissionAssignment | Represents permission-role linkage |
| PolicyCondition | Represents conditional policy logic |
| RoleHierarchy | Represents role inheritance relationships |
| SecurityConstraint | Represents enforced security limitations |

---

# 3. Role Entity

## Purpose

Represents a security role assigned to users.

Roles encapsulate:

- Permissions
- Tenant boundaries
- Authorization capabilities
- Security constraints

---

## Identity

```text id="y9m4dc"
roleId
````

---

## Attributes

| Attribute   | Type    | Description                     |
| ----------- | ------- | ------------------------------- |
| roleId      | UUID    | Unique role identifier          |
| tenantId    | String  | Owner tenant                    |
| name        | String  | Role name                       |
| description | String  | Human-readable description      |
| systemRole  | Boolean | Indicates platform-managed role |
| active      | Boolean | Indicates if role is enabled    |
| createdAt   | Instant | Creation timestamp              |
| updatedAt   | Instant | Last modification timestamp     |

---

## Behaviors

| Behavior             | Description                     |
| -------------------- | ------------------------------- |
| assignPermission()   | Assigns permission              |
| revokePermission()   | Removes permission              |
| deactivate()         | Disables role                   |
| rename()             | Updates role name               |
| validateAssignment() | Validates permission assignment |

---

## Business Rules

* Role names must be unique per tenant
* System roles cannot be modified by tenants
* Roles cannot contain duplicate permissions
* Tenant roles cannot exceed allowed privilege boundaries

---

## Example

```text id="w5f7te"
Role:
PSYCHOLOGIST
```

---

# 4. Permission Entity

## Purpose

Represents the smallest unit of authorization.

Permissions define allowed actions.

---

## Identity

```text id="m2k8pa"
permissionId
```

---

## Attributes

| Attribute        | Type    | Description                    |
| ---------------- | ------- | ------------------------------ |
| permissionId     | UUID    | Unique permission identifier   |
| code             | String  | Permission code                |
| resource         | String  | Protected resource             |
| action           | String  | Allowed action                 |
| scope            | String  | Authorization scope            |
| systemPermission | Boolean | Indicates immutable permission |
| createdAt        | Instant | Creation timestamp             |

---

## Example

```text id="j1n5sb"
Resource: PATIENT
Action: CREATE

Permission:
CREATE_PATIENT
```

---

## Behaviors

| Behavior             | Description                    |
| -------------------- | ------------------------------ |
| validateScope()      | Validates permission scope     |
| isSystemPermission() | Indicates protected permission |

---

## Business Rules

* Permission codes must be globally unique
* Permissions should remain atomic
* System permissions cannot be deleted
* Permissions must belong to valid resources

---

# 5. AuthorizationPolicy Entity

## Purpose

Represents dynamic authorization rules beyond RBAC.

Policies allow contextual access validation.

---

## Identity

```text id="t6r3xm"
policyId
```

---

## Attributes

| Attribute   | Type    | Description              |
| ----------- | ------- | ------------------------ |
| policyId    | UUID    | Unique policy identifier |
| tenantId    | String  | Owner tenant             |
| name        | String  | Policy name              |
| description | String  | Policy description       |
| resource    | String  | Protected resource       |
| action      | String  | Protected action         |
| active      | Boolean | Policy status            |
| priority    | Integer | Evaluation priority      |
| createdAt   | Instant | Creation timestamp       |

---

## Behaviors

| Behavior             | Description            |
| -------------------- | ---------------------- |
| activate()           | Enables policy         |
| deactivate()         | Disables policy        |
| evaluate()           | Evaluates policy       |
| validateConditions() | Validates policy rules |

---

## Example

```text id="b4h9ko"
Policy:
Cannot edit closed appointment
```

---

## Business Rules

* Policies must be deterministic
* Policies must target valid resources
* Policies cannot violate tenant isolation
* Policies must contain valid conditions

---

# 6. Privilege Entity

## Purpose

Represents grouped security capabilities.

Privileges simplify permission grouping.

---

## Identity

```text id="r8c1qy"
privilegeId
```

---

## Attributes

| Attribute   | Type    | Description                       |
| ----------- | ------- | --------------------------------- |
| privilegeId | UUID    | Unique privilege identifier       |
| tenantId    | String  | Owner tenant                      |
| code        | String  | Privilege code                    |
| description | String  | Description                       |
| active      | Boolean | Indicates if privilege is enabled |

---

## Example

```text id="z7x2mh"
CLINICAL_OPERATIONS
```

---

## Behaviors

| Behavior              | Description                   |
| --------------------- | ----------------------------- |
| addPermission()       | Adds permission               |
| removePermission()    | Removes permission            |
| validateComposition() | Validates grouped permissions |

---

## Business Rules

* Privileges cannot contain cyclic references
* Privileges must contain valid permissions
* Privileges respect tenant security boundaries

---

# 7. AuthorizationDecision Entity

## Purpose

Represents the result of an authorization evaluation.

Important for:

* Auditing
* Security tracking
* Compliance
* Monitoring

---

## Identity

```text id="o3v8tb"
decisionId
```

---

## Attributes

| Attribute   | Type    | Description                |
| ----------- | ------- | -------------------------- |
| decisionId  | UUID    | Unique decision identifier |
| userId      | UUID    | Evaluated user             |
| tenantId    | String  | User tenant                |
| resource    | String  | Evaluated resource         |
| action      | String  | Evaluated action           |
| result      | Enum    | ALLOW/DENY                 |
| reason      | String  | Decision explanation       |
| evaluatedAt | Instant | Evaluation timestamp       |

---

## Behaviors

| Behavior       | Description               |
| -------------- | ------------------------- |
| allow()        | Marks decision as allowed |
| deny()         | Marks decision as denied  |
| attachReason() | Adds denial reason        |

---

## Example

```text id="u5j0dn"
Result:
DENY

Reason:
Missing permission DELETE_PATIENT
```

---

# 8. AccessContext Entity

## Purpose

Represents runtime authorization evaluation context.

Contains all data required to evaluate access.

---

## Identity

```text id="k9h6wr"
contextId
```

---

## Attributes

| Attribute   | Type             | Description           |
| ----------- | ---------------- | --------------------- |
| contextId   | UUID             | Context identifier    |
| userId      | UUID             | Current user          |
| tenantId    | String           | Current tenant        |
| roles       | List<Role>       | User roles            |
| permissions | List<Permission> | Effective permissions |
| resourceId  | String           | Target resource       |
| action      | String           | Requested action      |
| ipAddress   | String           | Request origin        |
| deviceId    | String           | Request device        |
| requestTime | Instant          | Evaluation timestamp  |

---

## Behaviors

| Behavior          | Description                 |
| ----------------- | --------------------------- |
| hasPermission()   | Checks permission existence |
| belongsToTenant() | Validates tenant ownership  |
| matchesResource() | Validates resource access   |

---

## Business Rules

* Context must always contain tenant information
* Access context cannot omit identity information
* Runtime context must remain immutable during evaluation

---

# 9. PermissionAssignment Entity

## Purpose

Represents the assignment relationship between:

* Roles
* Permissions

Useful for:

* Auditing
* Tracking
* Metadata enrichment

---

## Identity

```text id="q2f7ls"
assignmentId
```

---

## Attributes

| Attribute    | Type    | Description                  |
| ------------ | ------- | ---------------------------- |
| assignmentId | UUID    | Unique assignment identifier |
| roleId       | UUID    | Assigned role                |
| permissionId | UUID    | Assigned permission          |
| assignedBy   | UUID    | User who assigned            |
| assignedAt   | Instant | Assignment timestamp         |

---

## Behaviors

| Behavior             | Description                    |
| -------------------- | ------------------------------ |
| validateAssignment() | Validates assignment integrity |

---

## Business Rules

* Duplicate assignments are forbidden
* Assignment must respect tenant boundaries
* Only authorized users may assign permissions

---

# 10. PolicyCondition Entity

## Purpose

Represents a conditional rule inside a policy.

---

## Identity

```text id="n1d5ga"
conditionId
```

---

## Attributes

| Attribute       | Type   | Description                 |
| --------------- | ------ | --------------------------- |
| conditionId     | UUID   | Unique condition identifier |
| field           | String | Evaluated field             |
| operator        | String | Comparison operator         |
| expectedValue   | String | Expected value              |
| logicalOperator | String | AND/OR                      |

---

## Example

```text id="m6t9xv"
field = status
operator = EQUALS
expectedValue = CLOSED
```

---

## Behaviors

| Behavior           | Description         |
| ------------------ | ------------------- |
| evaluate()         | Evaluates condition |
| validateOperator() | Validates operator  |

---

# 11. RoleHierarchy Entity

## Purpose

Represents role inheritance relationships.

Optional entity depending on architecture strategy.

---

## Identity

```text id="s4k2pe"
hierarchyId
```

---

## Example

```text id="c7m8qh"
ADMIN
    └── SUPERVISOR
            └── USER
```

---

## Business Rules

* Circular inheritance is forbidden
* Inheritance depth may be limited
* System roles cannot inherit tenant roles

---

# 12. SecurityConstraint Entity

## Purpose

Represents enforced authorization restrictions.

Used for:

* Compliance
* Security hardening
* Tenant restrictions
* Operational policies

---

## Identity

```text id="x3p9fu"
constraintId
```

---

## Example Constraints

```text id="d6w1kr"
- Max permissions per role
- Restricted system permissions
- Tenant privilege caps
- Immutable security roles
```

---

## Behaviors

| Behavior   | Description             |
| ---------- | ----------------------- |
| validate() | Validates security rule |
| enforce()  | Applies restriction     |

---

# 13. Entity Relationships

```text id="y5n7ts"
Role
    ├── contains -> Permission
    ├── governed by -> AuthorizationPolicy
    └── restricted by -> SecurityConstraint

AuthorizationPolicy
    └── composed of -> PolicyCondition

AuthorizationDecision
    └── generated from -> AccessContext
```

---

# 14. Entity Lifecycle Considerations

| Entity                | Lifecycle          |
| --------------------- | ------------------ |
| Role                  | Long-lived         |
| Permission            | Mostly immutable   |
| AuthorizationPolicy   | Evolves frequently |
| AccessContext         | Runtime/transient  |
| AuthorizationDecision | Historical/audit   |
| PermissionAssignment  | Auditable linkage  |

---

# 15. Multi-Tenant Considerations

Entities containing tenant ownership:

```text id="v2h4rn"
- Role
- AuthorizationPolicy
- Privilege
- AuthorizationDecision
```

Global/shared entities:

```text id="a8m1zy"
- Permission
- SecurityConstraint
```

---

# 16. Auditing Requirements

Important auditable entities:

| Entity                | Auditable Actions    |
| --------------------- | -------------------- |
| Role                  | Create/update/delete |
| PermissionAssignment  | Assign/revoke        |
| AuthorizationPolicy   | Activate/deactivate  |
| AuthorizationDecision | Access deny events   |

---

# 17. Security-Critical Rules

## Deny by Default

If authorization evaluation fails:

```text id="k4r7dx"
ACCESS = DENIED
```

---

## Tenant Isolation

Cross-tenant entity access is forbidden.

---

## Immutable Core Security Objects

Examples:

```text id="p9f3wl"
SUPER_ADMIN
SYSTEM_AUDITOR
```

cannot be altered by tenants.

---

# 18. Future Extensions

Future entities may include:

* TemporaryPermission
* DelegatedAccess
* ApprovalWorkflow
* ExternalPolicyProvider
* RiskEvaluation
* SessionAuthorization
* DeviceTrustProfile

---

# 19. Summary

The Authorization Management entities provide:

* Secure authorization modeling
* Fine-grained permission representation
* Dynamic policy support
* Strong tenant isolation
* Enterprise-grade security enforcement
* Scalable authorization domain structures

These entities form the operational foundation of the authorization system.

```
```
