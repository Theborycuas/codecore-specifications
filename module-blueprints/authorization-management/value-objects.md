# 04-authorization-management/value-objects.md

````md id="q7m2va"
# Authorization Management Value Objects

## 1. Introduction

This document defines the Value Objects used in the Authorization Management module.

Value Objects represent immutable conceptual elements that:

- Do not require identity
- Are compared by value
- Encapsulate validation logic
- Improve domain expressiveness
- Reduce primitive obsession
- Enforce authorization consistency

The design follows:

- Domain-Driven Design (DDD)
- Immutable modeling principles
- Security-oriented design
- Multi-tenant SaaS architecture

---

# 2. Value Object Overview

| Value Object | Purpose |
|---|---|
| PermissionCode | Represents standardized permission identifiers |
| RoleName | Represents validated role names |
| PolicyRule | Represents authorization rule expressions |
| ResourceName | Represents protected resources |
| ActionName | Represents authorized actions |
| AuthorizationScope | Represents permission scope |
| TenantBoundary | Represents tenant isolation constraints |
| AccessResult | Represents authorization outcome |
| SecurityLevel | Represents sensitivity classification |
| PolicyPriority | Represents policy evaluation priority |
| RoleType | Represents system or tenant role classification |
| PermissionSet | Represents immutable permission collections |
| AccessReason | Represents authorization explanations |
| ResourceOwnership | Represents ownership metadata |
| AuthorizationContextSnapshot | Represents immutable evaluation context |

---

# 3. PermissionCode

## Purpose

Represents a standardized permission identifier.

Encapsulates:

- Naming conventions
- Validation rules
- Normalization logic

---

## Structure

```text id="t9h3vk"
RESOURCE_ACTION
````

---

## Examples

```text id="n2m8rx"
CREATE_PATIENT
UPDATE_PATIENT
DELETE_APPOINTMENT
EXPORT_BILLING_REPORT
```

---

## Validation Rules

| Rule                  | Description           |
| --------------------- | --------------------- |
| Uppercase required    | Standardization       |
| Underscore separated  | Naming consistency    |
| Must contain action   | Authorization clarity |
| Must contain resource | Resource traceability |

---

## Invalid Examples

```text id="x4f1qp"
patientCreate
CreatePatient
managePatient
```

---

## Behaviors

| Behavior          | Description       |
| ----------------- | ----------------- |
| normalize()       | Standardizes code |
| validateFormat()  | Validates naming  |
| extractResource() | Returns resource  |
| extractAction()   | Returns action    |

---

# 4. RoleName

## Purpose

Represents validated role names.

Ensures consistency across tenants.

---

## Examples

```text id="u5w7lc"
PSYCHOLOGIST
RECEPTIONIST
BILLING_ADMIN
```

---

## Validation Rules

| Rule                       | Description             |
| -------------------------- | ----------------------- |
| Non-empty                  | Required                |
| Max length enforced        | Prevent oversized names |
| Reserved names protected   | Security                |
| Tenant uniqueness required | Isolation               |

---

## Reserved Names

```text id="r8p2gm"
SUPER_ADMIN
SYSTEM
ROOT
PLATFORM_ADMIN
```

---

## Behaviors

| Behavior     | Description             |
| ------------ | ----------------------- |
| normalize()  | Standardizes naming     |
| isReserved() | Detects protected names |

---

# 5. PolicyRule

## Purpose

Represents a business authorization rule.

Encapsulates policy expressions.

---

## Examples

```text id="c3k7tx"
status != CLOSED
```

```text id="w9v4ab"
ownerId == currentUserId
```

---

## Components

| Component        | Description         |
| ---------------- | ------------------- |
| Field            | Evaluated property  |
| Operator         | Comparison operator |
| Value            | Expected value      |
| Logical operator | AND/OR              |

---

## Behaviors

| Behavior         | Description                    |
| ---------------- | ------------------------------ |
| validateSyntax() | Validates expression           |
| evaluate()       | Evaluates condition            |
| serialize()      | Converts to persistence format |

---

## Supported Operators

```text id="o7n5yu"
==
!=
>
<
>=
<=
IN
NOT_IN
CONTAINS
```

---

# 6. ResourceName

## Purpose

Represents a protected domain resource.

---

## Examples

```text id="y6f8kr"
PATIENT
APPOINTMENT
MEDICAL_RECORD
INVOICE
USER
```

---

## Validation Rules

| Rule                      | Description               |
| ------------------------- | ------------------------- |
| Must exist in registry    | Prevent invalid resources |
| Uppercase standardization | Consistency               |
| Immutable naming          | Security predictability   |

---

## Behaviors

| Behavior             | Description            |
| -------------------- | ---------------------- |
| normalize()          | Standardizes resource  |
| validateRegistered() | Ensures valid resource |

---

# 7. ActionName

## Purpose

Represents an authorized operation.

---

## Examples

```text id="p1d4wh"
CREATE
READ
UPDATE
DELETE
EXPORT
ASSIGN
APPROVE
```

---

## Validation Rules

| Rule                      | Description          |
| ------------------------- | -------------------- |
| Standardized actions only | Security consistency |
| Immutable core actions    | Predictability       |

---

## Behaviors

| Behavior         | Description         |
| ---------------- | ------------------- |
| normalize()      | Standardizes action |
| validateAction() | Validates operation |

---

# 8. AuthorizationScope

## Purpose

Defines authorization visibility boundaries.

---

## Examples

```text id="v7k2zs"
SYSTEM
TENANT
SELF
DEPARTMENT
ORGANIZATION
```

---

## Description

| Scope        | Meaning                    |
| ------------ | -------------------------- |
| SYSTEM       | Platform-wide              |
| TENANT       | Tenant-wide                |
| SELF         | Own resources only         |
| DEPARTMENT   | Organizational subdivision |
| ORGANIZATION | Multi-department access    |

---

## Behaviors

| Behavior                | Description                |
| ----------------------- | -------------------------- |
| isHigherThan()          | Scope hierarchy validation |
| validateCompatibility() | Ensures valid scope usage  |

---

# 9. TenantBoundary

## Purpose

Represents tenant isolation constraints.

Critical for SaaS security.

---

## Example

```text id="j4n6pa"
Tenant A cannot access Tenant B resources
```

---

## Behaviors

| Behavior            | Description                 |
| ------------------- | --------------------------- |
| validateIsolation() | Enforces tenant boundaries  |
| allowsCrossTenant() | Detects explicit exceptions |

---

## Rules

| Rule                     | Description                |
| ------------------------ | -------------------------- |
| Isolation by default     | Security                   |
| Explicit exceptions only | Prevent accidental leakage |

---

# 10. AccessResult

## Purpose

Represents the result of authorization evaluation.

---

## Values

```text id="m9r2yo"
ALLOW
DENY
CONDITIONAL_ALLOW
```

---

## Behaviors

| Behavior    | Description  |
| ----------- | ------------ |
| isAllowed() | Checks allow |
| isDenied()  | Checks deny  |

---

# 11. SecurityLevel

## Purpose

Represents sensitivity classification.

Used for advanced authorization.

---

## Examples

```text id="d3x8fu"
LOW
MEDIUM
HIGH
CRITICAL
```

---

## Use Cases

* Sensitive records
* Financial data
* Administrative operations
* Clinical records

---

## Behaviors

| Behavior                 | Description            |
| ------------------------ | ---------------------- |
| compareLevel()           | Compares sensitivity   |
| requiresElevatedAccess() | Checks privilege level |

---

# 12. PolicyPriority

## Purpose

Represents policy execution priority.

---

## Example

```text id="f6t1qm"
1 = Highest priority
100 = Lowest priority
```

---

## Behaviors

| Behavior          | Description             |
| ----------------- | ----------------------- |
| comparePriority() | Orders policy execution |

---

## Rules

* Lower number = higher priority
* Deny policies may execute first
* Priority conflicts are forbidden

---

# 13. RoleType

## Purpose

Represents role classification.

---

## Values

```text id="s5v9hn"
SYSTEM
TENANT
TEMPORARY
DELEGATED
```

---

## Description

| Type      | Description           |
| --------- | --------------------- |
| SYSTEM    | Platform-managed role |
| TENANT    | Tenant-managed role   |
| TEMPORARY | Time-limited role     |
| DELEGATED | Delegated permissions |

---

# 14. PermissionSet

## Purpose

Represents immutable permission collections.

Used for:

* Effective permissions
* Cached authorization
* JWT permission snapshots

---

## Characteristics

| Characteristic   | Description                 |
| ---------------- | --------------------------- |
| Immutable        | Prevent accidental mutation |
| Deduplicated     | No repeated permissions     |
| Optimized lookup | Fast validation             |

---

## Behaviors

| Behavior    | Description                 |
| ----------- | --------------------------- |
| contains()  | Checks permission existence |
| merge()     | Combines permission sets    |
| intersect() | Computes shared permissions |
| subtract()  | Removes permissions         |

---

# 15. AccessReason

## Purpose

Represents human-readable authorization explanations.

Important for:

* Auditing
* Security analysis
* Debugging
* Compliance

---

## Examples

```text id="g7u4pw"
Missing permission:
DELETE_PATIENT
```

```text id="k2x8rl"
Tenant mismatch detected
```

---

## Behaviors

| Behavior        | Description             |
| --------------- | ----------------------- |
| format()        | Formats message         |
| appendContext() | Adds diagnostic details |

---

# 16. ResourceOwnership

## Purpose

Represents ownership metadata.

Supports:

* Ownership validation
* Self-access policies
* Resource-level authorization

---

## Example

```text id="h8m5zc"
ownerId = userId
```

---

## Behaviors

| Behavior          | Description                |
| ----------------- | -------------------------- |
| isOwner()         | Validates ownership        |
| belongsToTenant() | Validates tenant ownership |

---

# 17. AuthorizationContextSnapshot

## Purpose

Represents immutable authorization evaluation state.

Used for:

* Distributed authorization
* Auditing
* Async processing
* Event-driven evaluation

---

## Included Information

```text id="l4d7vn"
- User ID
- Tenant ID
- Roles
- Permissions
- Resource
- Action
- Timestamp
```

---

## Characteristics

| Characteristic | Description               |
| -------------- | ------------------------- |
| Immutable      | Prevent runtime mutation  |
| Serializable   | Supports messaging        |
| Audit-friendly | Historical replay support |

---

# 18. Equality Rules

All Value Objects compare by value.

Example:

```text id="q6r2xs"
PermissionCode(
    "CREATE_PATIENT"
)
==
PermissionCode(
    "CREATE_PATIENT"
)
```

---

# 19. Immutability Requirements

All Value Objects must be:

* Immutable
* Thread-safe
* Side-effect free
* Serialization-safe

Mutation requires creation of new instances.

---

# 20. Validation Strategy

Validation occurs:

| Stage           | Responsibility      |
| --------------- | ------------------- |
| Constructor     | Structural validity |
| Factory methods | Controlled creation |
| Domain services | Complex validation  |

Invalid Value Objects must never exist in memory.

---

# 21. Serialization Considerations

Value Objects must support:

* JSON serialization
* JWT embedding
* Event publishing
* Redis caching
* Database persistence

---

# 22. Security Considerations

Critical security protections include:

## Strict Validation

Prevent malformed authorization rules.

---

## Immutable Context

Prevent runtime tampering.

---

## Scope Enforcement

Prevent privilege escalation.

---

## Tenant Isolation

Prevent cross-tenant leakage.

---

# 23. Future Extensions

Future Value Objects may include:

* TimeWindowConstraint
* DeviceTrustLevel
* GeoLocationRestriction
* RiskScore
* SessionFingerprint
* MFARequirement
* DelegationWindow

---

# 24. Summary

The Authorization Management Value Objects provide:

* Strong domain expressiveness
* Immutable authorization modeling
* Consistent security validation
* Reduced primitive obsession
* Multi-tenant authorization safety
* Scalable authorization foundations

These Value Objects are critical to maintaining authorization correctness and security consistency across the platform.

```
```
