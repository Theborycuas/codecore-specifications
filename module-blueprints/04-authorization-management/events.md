# 04-authorization-management/events.md

````md id="u4k9xz"
# Authorization Management Domain Events

## 1. Introduction

This document defines the domain events emitted by the Authorization Management module.

Domain events represent important business occurrences inside the authorization domain and are fundamental for:

- Event-driven architecture
- Distributed consistency
- Auditability
- Security monitoring
- Reactive integrations
- Cache invalidation
- Compliance tracking

The events are designed following:

- Domain-Driven Design (DDD)
- Event-Driven Architecture (EDA)
- Immutable event principles
- Multi-tenant SaaS security standards

---

# 2. Event Design Principles

All events must follow these principles:

| Principle | Description |
|---|---|
| Immutable | Events cannot change after publication |
| Explicit | Clearly represent a business occurrence |
| Auditable | Must support traceability |
| Serializable | Compatible with messaging systems |
| Tenant-aware | Must include tenant context |
| Security-safe | Avoid sensitive payload leakage |

---

# 3. Event Categories

| Category | Purpose |
|---|---|
| Role Events | Role lifecycle operations |
| Permission Events | Permission assignment/removal |
| Policy Events | Policy lifecycle and enforcement |
| Authorization Events | Access evaluation results |
| Security Events | Suspicious or dangerous activity |
| Cache Events | Authorization cache synchronization |
| Audit Events | Compliance and tracking |

---

# 4. Common Event Metadata

All authorization events should include:

| Field | Type | Description |
|---|---|---|
| eventId | UUID | Unique event identifier |
| eventType | String | Event name |
| occurredAt | Instant | Event timestamp |
| tenantId | String | Tenant context |
| actorId | UUID | User/system initiating action |
| correlationId | String | Distributed tracing |
| aggregateId | UUID | Aggregate identifier |
| aggregateType | String | Aggregate type |
| version | Integer | Event schema version |

---

# 5. RoleCreated Event

## Purpose

Published when a new role is created.

---

## Trigger

```text id="v7m3dx"
Role successfully created
````

---

## Payload

| Field     | Type   | Description   |
| --------- | ------ | ------------- |
| roleId    | UUID   | Created role  |
| roleName  | String | Role name     |
| roleType  | String | SYSTEM/TENANT |
| createdBy | UUID   | Creator       |

---

## Consumers

* Audit Service
* Notification Service
* Security Monitoring
* Cache Systems

---

# 6. RoleUpdated Event

## Purpose

Published when a role is modified.

---

## Payload

| Field     | Type   | Description    |
| --------- | ------ | -------------- |
| roleId    | UUID   | Updated role   |
| changes   | Object | Updated fields |
| updatedBy | UUID   | Actor          |

---

## Security Importance

Critical for:

* Audit trails
* Security review
* Permission tracking

---

# 7. RoleDeactivated Event

## Purpose

Published when a role becomes inactive.

---

## Payload

| Field         | Type   | Description         |
| ------------- | ------ | ------------------- |
| roleId        | UUID   | Deactivated role    |
| reason        | String | Deactivation reason |
| deactivatedBy | UUID   | Actor               |

---

## Side Effects

```text id="w5p8tk"
- Cache invalidation
- Session refresh
- JWT permission refresh
```

---

# 8. PermissionAssigned Event

## Purpose

Published when a permission is assigned to a role.

One of the most security-sensitive events.

---

## Payload

| Field               | Type    | Description         |
| ------------------- | ------- | ------------------- |
| roleId              | UUID    | Target role         |
| permissionCode      | String  | Assigned permission |
| assignedBy          | UUID    | Actor               |
| assignmentTimestamp | Instant | Assignment time     |

---

## Security Importance

Tracks:

* Privilege changes
* Permission escalation
* Administrative actions

---

## Example

```text id="t1n6vy"
Permission:
DELETE_PATIENT

assigned to:
PSYCHOLOGIST
```

---

# 9. PermissionRevoked Event

## Purpose

Published when a permission is removed.

---

## Payload

| Field          | Type   | Description        |
| -------------- | ------ | ------------------ |
| roleId         | UUID   | Target role        |
| permissionCode | String | Removed permission |
| revokedBy      | UUID   | Actor              |

---

## Important Side Effects

```text id="r8w4fa"
- Cache invalidation
- JWT snapshot refresh
- Session revalidation
```

---

# 10. PermissionCreated Event

## Purpose

Published when a new permission is registered.

Usually platform-level only.

---

## Payload

| Field        | Type   | Description        |
| ------------ | ------ | ------------------ |
| permissionId | UUID   | Permission ID      |
| code         | String | Permission code    |
| resource     | String | Protected resource |
| action       | String | Authorized action  |

---

# 11. PolicyCreated Event

## Purpose

Published when an authorization policy is created.

---

## Payload

| Field      | Type   | Description       |
| ---------- | ------ | ----------------- |
| policyId   | UUID   | Policy identifier |
| policyName | String | Policy name       |
| resource   | String | Target resource   |
| action     | String | Target action     |

---

# 12. PolicyActivated Event

## Purpose

Published when a policy becomes active.

---

## Importance

Immediately affects authorization decisions.

---

## Side Effects

```text id="g3x9lp"
- Policy cache invalidation
- Distributed synchronization
- Runtime policy reload
```

---

# 13. PolicyDeactivated Event

## Purpose

Published when a policy is disabled.

---

## Security Considerations

May reduce authorization restrictions.

Requires audit tracking.

---

# 14. AuthorizationGranted Event

## Purpose

Published when access is granted.

---

## Payload

| Field             | Type    | Description       |
| ----------------- | ------- | ----------------- |
| userId            | UUID    | Authorized user   |
| resource          | String  | Accessed resource |
| action            | String  | Executed action   |
| decisionTimestamp | Instant | Decision time     |

---

## Usage

Usually sampled or rate-limited to avoid excessive volume.

---

# 15. AuthorizationDenied Event

## Purpose

Published when access is denied.

Critical security event.

---

## Payload

| Field        | Type   | Description      |
| ------------ | ------ | ---------------- |
| userId       | UUID   | Requesting user  |
| resource     | String | Target resource  |
| action       | String | Requested action |
| denialReason | String | Explanation      |
| ipAddress    | String | Request origin   |

---

## Importance

Used for:

* Threat detection
* Compliance
* Security analytics
* Intrusion monitoring

---

## Example

```text id="q4k7vr"
DENIED:
Missing permission DELETE_INVOICE
```

---

# 16. SuspiciousAuthorizationDetected Event

## Purpose

Published when abnormal authorization behavior is detected.

---

## Detection Examples

```text id="o6t2pb"
- Excessive denied requests
- Cross-tenant access attempts
- Privilege escalation attempts
- Unauthorized admin operations
```

---

## Payload

| Field         | Type   | Description         |
| ------------- | ------ | ------------------- |
| userId        | UUID   | Suspicious actor    |
| detectionType | String | Threat category     |
| severity      | String | LOW/MEDIUM/HIGH     |
| evidence      | Object | Supporting evidence |

---

# 17. TenantIsolationViolationDetected Event

## Purpose

Published when cross-tenant access is attempted.

Critical SaaS security event.

---

## Example

```text id="y9m5ht"
Tenant A attempting
access to Tenant B resource
```

---

## Severity

Usually:

```text id="d7f1zx"
HIGH
```

---

# 18. PrivilegeEscalationAttemptDetected Event

## Purpose

Published when unauthorized privilege escalation is attempted.

---

## Example

```text id="a2n8wp"
Tenant admin attempting
to assign SUPER_ADMIN
```

---

## Security Importance

High-priority security incident.

---

# 19. AuthorizationCacheInvalidated Event

## Purpose

Synchronizes distributed authorization caches.

---

## Trigger Examples

```text id="u5r3kl"
- Permission assignment
- Permission revocation
- Policy activation
- Role update
```

---

## Payload

| Field             | Type   | Description   |
| ----------------- | ------ | ------------- |
| affectedTenantId  | String | Tenant        |
| affectedRoles     | List   | Changed roles |
| invalidationScope | String | Cache scope   |

---

# 20. EffectivePermissionsRecomputed Event

## Purpose

Published after runtime permission recalculation.

---

## Use Cases

* JWT refresh
* Session synchronization
* Distributed consistency

---

# 21. SecurityConstraintViolationDetected Event

## Purpose

Published when a security constraint is violated.

---

## Examples

```text id="k8w4tv"
- Max permission limit exceeded
- Invalid role hierarchy
- Reserved permission modification
```

---

# 22. AuthorizationAuditRecorded Event

## Purpose

Published after audit evidence persistence.

---

## Usage

Supports:

* Compliance systems
* SIEM integrations
* Long-term archival

---

# 23. Event Ordering Considerations

Certain events require ordering guarantees.

---

## Examples

```text id="m3x7rc"
RoleCreated
    before
PermissionAssigned
```

---

## Recommended Strategies

| Strategy           | Use Case              |
| ------------------ | --------------------- |
| Aggregate ordering | Role consistency      |
| Kafka partitioning | Tenant-level ordering |
| Outbox pattern     | Reliable publication  |

---

# 24. Event Delivery Guarantees

Recommended delivery semantics:

| Event Type                    | Guarantee              |
| ----------------------------- | ---------------------- |
| Security events               | At least once          |
| Audit events                  | At least once          |
| Cache events                  | Best effort acceptable |
| Critical authorization events | Strong durability      |

---

# 25. Event Versioning Strategy

Events must support schema evolution.

---

## Recommendations

| Strategy               | Description           |
| ---------------------- | --------------------- |
| Explicit version field | Event schema tracking |
| Backward compatibility | Consumer safety       |
| Immutable contracts    | Stable integrations   |

---

# 26. Sensitive Data Restrictions

Authorization events must NEVER expose:

* Passwords
* Tokens
* Secrets
* Full JWT payloads
* Sensitive clinical data
* Raw credentials

---

# 27. Recommended Messaging Infrastructure

Suitable event brokers:

| Technology    | Suitability              |
| ------------- | ------------------------ |
| Apache Kafka  | High scalability         |
| RabbitMQ      | Flexible routing         |
| Redis Streams | Lightweight eventing     |
| AWS SNS/SQS   | Cloud-native scalability |

---

# 28. Event Replay Considerations

Certain events should support replay:

| Event              | Reason                      |
| ------------------ | --------------------------- |
| PermissionAssigned | Rebuild projections         |
| RoleUpdated        | Audit recovery              |
| PolicyActivated    | Policy state reconstruction |

---

# 29. CQRS Integration

Events may update read models including:

* Effective permissions projections
* Authorization audit views
* Security dashboards
* Suspicious activity dashboards

---

# 30. Distributed System Considerations

Authorization events support:

* Multi-region deployments
* Reactive systems
* Horizontal scaling
* Eventual consistency
* Distributed cache synchronization

---

# 31. Failure Handling

If event publication fails:

| Event Type         | Strategy                     |
| ------------------ | ---------------------------- |
| Security-critical  | Retry mandatory              |
| Audit              | Durable persistence required |
| Cache invalidation | Retry recommended            |

---

# 32. Future Event Extensions

Future events may include:

* TemporaryAccessGranted
* DelegatedAccessApproved
* MFAAuthorizationRequired
* EmergencyAccessActivated
* DeviceTrustViolationDetected
* RiskThresholdExceeded

---

# 33. Summary

The Authorization Management events provide:

* Enterprise-grade auditability
* Distributed authorization synchronization
* Security monitoring integration
* Reactive system communication
* Cache consistency
* Compliance traceability
* Scalable event-driven authorization architecture

These events form the integration backbone of the authorization ecosystem.

```
```
