# 04-authorization-management/api-contracts.md

````md id="f8m3vx"
# Authorization Management API Contracts

## 1. Introduction

This document defines the API contracts of the Authorization Management module.

The APIs expose secure authorization capabilities including:

- Role management
- Permission management
- Authorization evaluation
- Policy management
- Permission resolution
- Security auditing support

The contracts are designed following:

- RESTful principles
- Secure-by-default architecture
- Multi-tenant SaaS standards
- Domain-driven API design
- Enterprise authorization best practices

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Explicit authorization | Every endpoint requires authorization validation |
| Tenant isolation | All operations scoped by tenant |
| Least privilege | Minimal required permissions |
| Immutable auditability | Security actions are traceable |
| Idempotency | Safe retry support |
| Versioned contracts | API evolution support |
| Deny by default | Unauthorized access rejected |

---

# 3. Base URL

```text id="n2w6zt"
/api/v1/authorization
````

---

# 4. Common Headers

| Header           | Required    | Description         |
| ---------------- | ----------- | ------------------- |
| Authorization    | Yes         | Bearer JWT token    |
| X-Tenant-ID      | Yes         | Tenant context      |
| X-Correlation-ID | Recommended | Distributed tracing |
| Content-Type     | Yes         | application/json    |

---

# 5. Authentication Requirements

All endpoints require authenticated access unless explicitly declared otherwise.

Authentication handled by:

* API Gateway
* JWT validation
* OAuth2 provider
* Identity & Access Management (IAM)
* User Management

---

# 6. Authorization Requirements

Authorization is enforced at:

| Layer         | Description                   |
| ------------- | ----------------------------- |
| API Gateway   | Coarse-grained authorization  |
| Service layer | Business authorization        |
| Domain layer  | Aggregate security validation |

---

# 7. Common Response Structure

## Success Response

```json id="v4p8ra"
{
  "success": true,
  "timestamp": "2026-05-18T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="m7x2lf"
{
  "success": false,
  "timestamp": "2026-05-18T10:00:00Z",
  "error": {
    "code": "AUTHORIZATION_DENIED",
    "message": "Missing required permission",
    "details": []
  }
}
```

---

# 8. HTTP Status Codes

| Status | Meaning                     |
| ------ | --------------------------- |
| 200    | Success                     |
| 201    | Created                     |
| 204    | No content                  |
| 400    | Invalid request             |
| 401    | Unauthenticated             |
| 403    | Unauthorized                |
| 404    | Resource not found          |
| 409    | Conflict                    |
| 422    | Business validation failure |
| 429    | Rate limit exceeded         |
| 500    | Internal error              |

---

# 9. Role Management APIs

# 9.1 Create Role

## Endpoint

```text id="c3n7pw"
POST /roles
```

---

## Required Permission

```text id="x9m5tr"
CREATE_ROLE
```

---

## Request

```json id="u1k4yb"
{
  "name": "PSYCHOLOGIST",
  "description": "Clinical psychologist role",
  "roleType": "TENANT"
}
```

---

## Response

```json id="a5w8qc"
{
  "success": true,
  "data": {
    "roleId": "uuid",
    "name": "PSYCHOLOGIST",
    "active": true
  }
}
```

---

## Validation Rules

* Unique role name per tenant
* Reserved names forbidden
* Role type validation
* Tenant boundary validation

---

# 9.2 Update Role

## Endpoint

```text id="h2v9fk"
PUT /roles/{roleId}
```

---

## Required Permission

```text id="z6x1mn"
UPDATE_ROLE
```

---

## Restrictions

* System roles protected
* Immutable identifiers
* Privilege escalation checks required

---

# 9.3 Deactivate Role

## Endpoint

```text id="k4p7sy"
PATCH /roles/{roleId}/deactivate
```

---

## Required Permission

```text id="w8r2lt"
DEACTIVATE_ROLE
```

---

## Important Rules

* Critical roles protected
* Active dependency validation required

---

# 9.4 List Roles

## Endpoint

```text id="j5n3xa"
GET /roles
```

---

## Query Parameters

| Parameter | Description         |
| --------- | ------------------- |
| active    | Filter active roles |
| roleType  | SYSTEM/TENANT       |
| search    | Search by role name |
| page      | Pagination          |
| size      | Page size           |

---

## Required Permission

```text id="d7f9vq"
VIEW_ROLE
```

---

# 10. Permission Management APIs

# 10.1 Assign Permission

## Endpoint

```text id="o2m8zk"
POST /roles/{roleId}/permissions
```

---

## Required Permission

```text id="r1t5yn"
ASSIGN_PERMISSION
```

---

## Request

```json id="q6x4wh"
{
  "permissionCode": "CREATE_PATIENT"
}
```

---

## Security-Critical Validations

* Tenant isolation
* Privilege escalation prevention
* Permission existence validation
* Duplicate assignment prevention

---

# 10.2 Revoke Permission

## Endpoint

```text id="g3w7pn"
DELETE /roles/{roleId}/permissions/{permissionCode}
```

---

## Required Permission

```text id="m8v2yr"
REVOKE_PERMISSION
```

---

## Important Rules

* Critical permissions protected
* Audit required
* Cache invalidation mandatory

---

# 10.3 List Permissions

## Endpoint

```text id="t5k9xa"
GET /permissions
```

---

## Query Parameters

| Parameter | Description            |
| --------- | ---------------------- |
| resource  | Filter by resource     |
| action    | Filter by action       |
| scope     | Filter by scope        |
| search    | Search permission code |

---

## Required Permission

```text id="v9q4lp"
VIEW_PERMISSION
```

---

# 11. Authorization Evaluation APIs

# 11.1 Evaluate Authorization

## Endpoint

```text id="f4r8wy"
POST /evaluate
```

---

## Purpose

Evaluates runtime access authorization.

---

## Request

```json id="u8m1xt"
{
  "resource": "PATIENT",
  "action": "UPDATE",
  "resourceId": "12345",
  "context": {
    "ownerId": "user-1"
  }
}
```

---

## Response

```json id="n3w6zk"
{
  "success": true,
  "data": {
    "decision": "ALLOW",
    "reason": null
  }
}
```

---

## Possible Decisions

```text id="a7x2rf"
ALLOW
DENY
CONDITIONAL_ALLOW
```

---

## Evaluation Layers

| Layer                 | Validation          |
| --------------------- | ------------------- |
| Authentication        | Identity validation |
| Tenant validation     | Isolation           |
| Permission validation | RBAC                |
| Policy validation     | Dynamic rules       |
| Ownership validation  | Resource access     |

---

# 11.2 Resolve Effective Permissions

## Endpoint

```text id="y5m9vk"
GET /users/{userId}/effective-permissions
```

---

## Purpose

Returns computed runtime permissions.

---

## Required Permission

```text id="c8t4zn"
VIEW_EFFECTIVE_PERMISSIONS
```

---

## Response

```json id="l2q7xs"
{
  "success": true,
  "data": {
    "permissions": [
      "CREATE_PATIENT",
      "UPDATE_PATIENT",
      "VIEW_MEDICAL_RECORD"
    ]
  }
}
```

---

# 12. Policy Management APIs

# 12.1 Create Policy

## Endpoint

```text id="s6r1pa"
POST /policies
```

---

## Required Permission

```text id="p9n5vk"
CREATE_POLICY
```

---

## Request

```json id="h4x8wt"
{
  "name": "Closed records cannot be edited",
  "resource": "MEDICAL_RECORD",
  "action": "UPDATE",
  "priority": 1,
  "conditions": [
    {
      "field": "status",
      "operator": "EQUALS",
      "expectedValue": "CLOSED"
    }
  ]
}
```

---

## Validation Rules

* Deterministic conditions required
* Valid resource required
* Priority uniqueness validation
* Policy syntax validation

---

# 12.2 Activate Policy

## Endpoint

```text id="j7m3yb"
PATCH /policies/{policyId}/activate
```

---

## Required Permission

```text id="w2x9nt"
ACTIVATE_POLICY
```

---

## Side Effects

```text id="v5q1zr"
- Cache invalidation
- Distributed synchronization
- Runtime policy refresh
```

---

# 12.3 Deactivate Policy

## Endpoint

```text id="r4k8xm"
PATCH /policies/{policyId}/deactivate
```

---

## Important Rules

* Audit mandatory
* High-risk policies may require approval

---

# 12.4 List Policies

## Endpoint

```text id="x1v6pa"
GET /policies
```

---

## Query Parameters

| Parameter | Description            |
| --------- | ---------------------- |
| active    | Filter active policies |
| resource  | Filter by resource     |
| action    | Filter by action       |
| priority  | Filter by priority     |

---

# 13. Audit APIs

# 13.1 Authorization Audit Search

## Endpoint

```text id="q8t5yn"
GET /audit
```

---

## Required Permission

```text id="u6w3xr"
VIEW_AUTHORIZATION_AUDIT
```

---

## Query Parameters

| Parameter | Description        |
| --------- | ------------------ |
| userId    | Filter by user     |
| tenantId  | Filter by tenant   |
| resource  | Filter by resource |
| action    | Filter by action   |
| result    | ALLOW/DENY         |
| startDate | Date range         |
| endDate   | Date range         |

---

## Security Restrictions

* Tenant-scoped visibility
* Sensitive event filtering
* High-volume pagination required

---

# 14. Security Monitoring APIs

# 14.1 Suspicious Activity Search

## Endpoint

```text id="m9r4zw"
GET /security/suspicious-activities
```

---

## Required Permission

```text id="n5k2tx"
VIEW_SECURITY_ALERTS
```

---

## Example Results

```json id="f1q8yb"
{
  "success": true,
  "data": [
    {
      "type": "PRIVILEGE_ESCALATION_ATTEMPT",
      "severity": "HIGH",
      "detectedAt": "2026-05-18T12:00:00Z"
    }
  ]
}
```

---

# 15. Internal Service APIs

These APIs are intended for internal microservice communication.

---

# 15.1 Internal Authorization Validation

## Endpoint

```text id="t7x4vn"
POST /internal/evaluate
```

---

## Security Requirements

* Internal service authentication required
* mTLS recommended
* Service identity validation required

---

## Example Request

```json id="k3w9pm"
{
  "service": "clinical-service",
  "userId": "uuid",
  "tenantId": "tenant-a",
  "resource": "PATIENT",
  "action": "VIEW"
}
```

---

# 16. Pagination Standards

Paginated endpoints should return:

```json id="d8v2rk"
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 100,
    "totalPages": 5
  }
}
```

---

# 17. Sorting Standards

## Query Example

```text id="b6x1tm"
?sort=createdAt,desc
```

---

## Multiple Sorts

```text id="z4n8qy"
?sort=priority,asc&sort=name,desc
```

---

# 18. Idempotency Considerations

Recommended for:

| Operation             | Strategy              |
| --------------------- | --------------------- |
| Role creation         | Idempotency key       |
| Permission assignment | Duplicate-safe        |
| Policy activation     | State-safe operations |

---

# 19. Rate Limiting

Recommended protection:

| Endpoint Category        | Recommendation  |
| ------------------------ | --------------- |
| Authorization evaluation | High throughput |
| Audit APIs               | Medium          |
| Security monitoring      | Restricted      |
| Administrative APIs      | Strict          |

---

# 20. Security Headers

Recommended headers:

| Header             | Purpose                 |
| ------------------ | ----------------------- |
| X-Correlation-ID   | Tracing                 |
| X-Request-ID       | Request tracking        |
| X-Client-Version   | Compatibility           |
| X-Service-Identity | Internal authentication |

---

# 21. API Security Considerations

## Deny by Default

Missing authorization:

```text id="y2m7pk"
403 FORBIDDEN
```

---

## Tenant Isolation

Cross-tenant access:

```text id="w9f3ra"
403 FORBIDDEN
```

---

## Sensitive Data Protection

Never expose:

* Internal policy expressions
* JWT internals
* Security secrets
* Internal cache structures

---

# 22. Distributed System Considerations

The APIs support:

* Stateless deployments
* Horizontal scaling
* Reactive processing
* Distributed tracing
* Cache synchronization

---

# 23. Reactive API Considerations

Reactive implementations should support:

```text id="g5q8vt"
Mono<ResponseEntity<?>>
Flux<ResponseEntity<?>>
```

---

## Requirements

* Non-blocking authorization
* Async policy evaluation
* Reactive security context propagation

---

# 24. OpenAPI Recommendations

Recommended documentation:

* OpenAPI 3.x
* Swagger UI
* Security scheme definitions
* Permission requirement annotations

---

# 25. API Versioning Strategy

Recommended versioning:

```text id="r3n6wy"
/api/v1/authorization
```

Future evolution:

```text id="k1x9vb"
/api/v2/authorization
```

---

# 26. Error Codes

| Code                          | Description             |
| ----------------------------- | ----------------------- |
| AUTHORIZATION_DENIED          | Access denied           |
| TENANT_MISMATCH               | Cross-tenant violation  |
| INVALID_PERMISSION            | Unknown permission      |
| ROLE_ALREADY_EXISTS           | Duplicate role          |
| POLICY_VALIDATION_FAILED      | Invalid policy          |
| PRIVILEGE_ESCALATION_DETECTED | Unauthorized escalation |
| SYSTEM_ROLE_PROTECTED         | Immutable role          |

---

# 27. Future API Extensions

Future APIs may include:

* Temporary access APIs
* Delegated administration APIs
* MFA-required authorization APIs
* Risk-based authorization APIs
* Emergency access APIs
* External policy provider APIs

---

# 28. Summary

The Authorization Management API contracts provide:

* Secure authorization operations
* Enterprise-grade access validation
* Strong tenant isolation
* Distributed authorization support
* Reactive-ready architecture
* Fine-grained permission management
* Scalable security integration

These APIs form the external contract layer of the authorization ecosystem.

```
```
