````md id="8k2xqp"
# EVENT-CATALOG.md

# 1. Introduction

This document defines the enterprise-wide Event Catalog of the CodeCore platform.

The Event Catalog establishes:

- domain event definitions
- event ownership
- event producers
- event consumers
- event contracts
- event classification
- replay safety rules
- idempotency rules
- event propagation standards
- event-driven integration boundaries

This document follows:

- Domain-Driven Design (DDD)
- Event-Driven Architecture (EDA)
- Reactive Architecture
- Distributed Systems principles
- Multi-Tenant SaaS Architecture
- Enterprise Messaging standards

---

# 2. Purpose

The Event Catalog exists to ensure:

```text
consistent event-driven communication
+
clear event ownership
+
replay safety
+
distributed traceability
+
bounded context isolation
````

The Event Catalog is the:

```text id="m5v8qp"
canonical event language
of the platform
```

---

# 3. Event Design Principles

All events MUST follow:

| Principle           | Mandatory |
| ------------------- | --------- |
| Immutable           | Yes       |
| Replay-safe         | Yes       |
| Tenant-aware        | Yes       |
| Correlation-aware   | Yes       |
| Serializable        | Yes       |
| Business meaningful | Yes       |
| Idempotent-friendly | Yes       |

---

# 4. Event Naming Convention

## Mandatory Convention

```text id="w7p2ld"
<Entity><PastTenseVerb>
```

---

## Examples

```text id="p4q8zs"
UserRegistered
PaymentCaptured
InvoiceGenerated
SubscriptionExpired
```

---

## Forbidden Naming

```text id="m1v9tx"
DoUserCreation
UpdateInvoiceNow
PaymentProcessingStartedNow
```

---

# 5. Event Classification

| Event Type           | Description                 |
| -------------------- | --------------------------- |
| Domain Event         | Business fact               |
| Integration Event    | Cross-context propagation   |
| Infrastructure Event | Technical infrastructure    |
| Security Event       | Security-related occurrence |
| Observability Event  | Telemetry event             |

---

# 6. Common Event Metadata

All events MUST contain:

| Field         | Description             |
| ------------- | ----------------------- |
| eventId       | Unique event identifier |
| eventType     | Canonical event name    |
| occurredAt    | UTC timestamp           |
| correlationId | Distributed tracing     |
| tenantId      | Tenant scope            |
| aggregateId   | Aggregate correlation   |
| version       | Event schema version    |

---

# 7. Identity & Access Management Events

# 7.1 UserRegistered

## Published By

```text id="u4x8md"
Identity & Access Management
```

---

## Description

Published after successful user registration.

---

## Consumers

| Consumer        | Purpose          |
| --------------- | ---------------- |
| User Management | Create profile   |
| Notification    | Welcome email    |
| Audit           | Compliance trace |
| Observability   | Metrics/tracing  |

---

## Replay Safe

```text id="b2f7pw"
Yes
```

---

## Idempotent

```text id="m6k3qs"
Yes
```

---

# 7.2 UserAuthenticated

## Published By

```text id="f5w2xr"
Identity & Access Management
```

---

## Description

Published after successful authentication.

---

## Consumers

| Consumer      | Purpose              |
| ------------- | -------------------- |
| Audit         | Authentication audit |
| Observability | Security metrics     |

---

# 7.3 SessionCreated

## Published By

```text id="x2m9vl"
Identity & Access Management
```

---

## Description

Published after session creation.

---

## Consumers

| Consumer      | Purpose              |
| ------------- | -------------------- |
| Authorization | Session validation   |
| Audit         | Session traceability |

---

# 7.4 SessionRevoked

## Published By

```text id="n7d1qp"
Identity & Access Management
```

---

## Description

Published after session invalidation.

---

## Consumers

| Consumer      | Purpose             |
| ------------- | ------------------- |
| Authorization | Access invalidation |
| Audit         | Compliance          |

---

# 8. Authorization Management Events

# 8.1 RoleAssigned

## Published By

```text id="r9v2mz"
Authorization Management
```

---

## Description

Published after role assignment.

---

## Consumers

| Consumer      | Purpose            |
| ------------- | ------------------ |
| Audit         | Compliance         |
| Observability | Security telemetry |

---

# 8.2 PermissionGranted

## Published By

```text id="q5m8fd"
Authorization Management
```

---

## Description

Published after permission assignment.

---

# 8.3 PolicyUpdated

## Published By

```text id="y4p7lh"
Authorization Management
```

---

## Description

Published after authorization policy changes.

---

## Critical Rule

```text id="d6v3sq"
authorization decisions
must remain centralized
```

---

# 9. Tenant Management Events

# 9.1 TenantCreated

## Published By

```text id="z9m1vf"
Tenant Management
```

---

## Description

Published after tenant provisioning.

---

## Consumers

| Consumer      | Purpose        |
| ------------- | -------------- |
| Subscription  | Default plan   |
| Configuration | Default config |
| Audit         | Compliance     |
| Observability | Metrics        |

---

# 9.2 TenantSuspended

## Published By

```text id="j2f8xp"
Tenant Management
```

---

## Description

Published after tenant suspension.

---

## Consumers

| Consumer      | Purpose            |
| ------------- | ------------------ |
| Authorization | Access restriction |
| Billing       | Billing pause      |
| Notification  | Tenant alerts      |

---

# 10. Subscription Management Events

# 10.1 SubscriptionActivated

## Published By

```text id="v5m9qw"
Subscription Management
```

---

## Consumers

| Consumer      | Purpose               |
| ------------- | --------------------- |
| Billing       | Invoice generation    |
| Authorization | Feature activation    |
| Notification  | Customer notification |

---

# 10.2 SubscriptionUpgraded

## Published By

```text id="f3w2nv"
Subscription Management
```

---

## Description

Published after plan upgrade.

---

# 10.3 SubscriptionExpired

## Published By

```text id="u8m5lr"
Subscription Management
```

---

## Consumers

| Consumer      | Purpose             |
| ------------- | ------------------- |
| Authorization | Feature restriction |
| Billing       | Final invoice       |
| Notification  | Expiration alerts   |

---

# 11. Billing Management Events

# 11.1 InvoiceGenerated

## Published By

```text id="w9x4mp"
Billing Management
```

---

## Consumers

| Consumer     | Purpose           |
| ------------ | ----------------- |
| Payment      | Payment execution |
| Notification | Invoice email     |
| Audit        | Compliance        |

---

# 11.2 InvoicePaid

## Published By

```text id="q7m3pl"
Billing Management
```

---

## Description

Published after invoice settlement.

---

# 11.3 InvoiceOverdue

## Published By

```text id="x5v2rm"
Billing Management
```

---

## Consumers

| Consumer     | Purpose               |
| ------------ | --------------------- |
| Notification | Payment reminders     |
| Subscription | Restriction workflows |

---

# 12. Payment Management Events

# 12.1 PaymentInitiated

## Published By

```text id="l4m8qt"
Payment Management
```

---

## Description

Published after payment initialization.

---

# 12.2 PaymentCaptured

## Published By

```text id="n5w1xr"
Payment Management
```

---

## Consumers

| Consumer     | Purpose              |
| ------------ | -------------------- |
| Billing      | Invoice settlement   |
| Subscription | Activation           |
| Notification | Payment receipt      |
| Audit        | Financial compliance |

---

## Critical Rule

```text id="m2f7ps"
payments
must remain idempotent
```

---

# 12.3 PaymentFailed

## Published By

```text id="t9v4lr"
Payment Management
```

---

## Consumers

| Consumer      | Purpose              |
| ------------- | -------------------- |
| Billing       | Debt tracking        |
| Notification  | Failure notification |
| Observability | Failure metrics      |

---

# 12.4 RefundProcessed

## Published By

```text id="h6x1md"
Payment Management
```

---

## Description

Published after successful refund.

---

# 13. Notification Management Events

# 13.1 NotificationRequested

## Published By

```text id="p8v5zs"
Notification Management
```

---

## Description

Internal orchestration event.

---

# 13.2 NotificationDelivered

## Published By

```text id="x7m1qp"
Notification Management
```

---

## Consumers

| Consumer      | Purpose          |
| ------------- | ---------------- |
| Audit         | Delivery trace   |
| Observability | Delivery metrics |

---

# 13.3 NotificationFailed

## Published By

```text id="g5v9kl"
Notification Management
```

---

## Description

Published after delivery failure.

---

## Consumers

| Consumer            | Purpose  |
| ------------------- | -------- |
| Retry orchestration | Recovery |
| Observability       | Metrics  |

---

# 14. Audit Management Events

# 14.1 AuditRecordCreated

## Published By

```text id="q2x7mw"
Audit Management
```

---

## Description

Published after audit persistence.

---

## Critical Rule

```text id="k4f9zs"
audit records
are append-only
```

---

# 15. File Management Events

# 15.1 FileUploaded

## Published By

```text id="r8m2lp"
File Management
```

---

## Description

Published after successful upload.

---

## Consumers

| Consumer      | Purpose    |
| ------------- | ---------- |
| Audit         | Compliance |
| Observability | Metrics    |

---

# 15.2 FileDeleted

## Published By

```text id="m3v8qp"
File Management
```

---

## Description

Published after deletion lifecycle completion.

---

# 16. Configuration Management Events

# 16.1 FeatureFlagUpdated

## Published By

```text id="z5w2ld"
Configuration Management
```

---

## Description

Published after feature toggle updates.

---

## Consumers

| Consumer          | Purpose               |
| ----------------- | --------------------- |
| Platform services | Dynamic behavior      |
| Observability     | Configuration metrics |

---

# 16.2 RuntimeConfigurationChanged

## Published By

```text id="n8x4pr"
Configuration Management
```

---

## Description

Published after runtime configuration changes.

---

# 17. Observability Events

# 17.1 TraceRecorded

## Published By

```text id="j7v1mk"
Observability Management
```

---

## Description

Distributed tracing telemetry.

---

# 17.2 MetricThresholdExceeded

## Published By

```text id="u5m9xq"
Observability Management
```

---

## Consumers

| Consumer         | Purpose           |
| ---------------- | ----------------- |
| Alerting systems | Incident response |
| Operations       | Monitoring        |

---

# 18. Integration Management Events

# 18.1 IntegrationExecuted

## Published By

```text id="y8f4ps"
Integration Management
```

---

## Description

Published after successful integration execution.

---

# 18.2 WebhookReceived

## Published By

```text id="b3m7ql"
Integration Management
```

---

## Description

Published after inbound webhook ingestion.

---

## Critical Rule

```text id="m1q8tv"
external webhooks
must always be replay-safe
```

---

# 18.3 ProviderFailed

## Published By

```text id="t6x2wr"
Integration Management
```

---

## Consumers

| Consumer            | Purpose            |
| ------------------- | ------------------ |
| Retry orchestration | Recovery           |
| Observability       | Metrics            |
| Alerting            | Incident detection |

---

# 19. Security Events

# 19.1 SuspiciousAuthenticationDetected

## Published By

```text id="v4m8qp"
Identity & Access Management
```

---

## Description

Published after anomaly detection.

---

## Consumers

| Consumer            | Purpose             |
| ------------------- | ------------------- |
| Security monitoring | Threat response     |
| Audit               | Compliance          |
| Alerting            | Incident escalation |

---

# 19.2 ReplayAttackDetected

## Published By

```text id="d8x5lr"
Integration Management
```

---

## Description

Published after replay attack detection.

---

# 20. Event Replay Rules

## Mandatory Rules

| Rule                      | Mandatory |
| ------------------------- | --------- |
| Events immutable          | Yes       |
| Replay-safe consumers     | Yes       |
| Idempotent handlers       | Yes       |
| Duplicate-safe processing | Yes       |

---

## Critical Rule

```text id="x2f9ps"
events
may be delivered
more than once
```

---

# 21. Event Versioning Rules

## Mandatory Versioning

All public events MUST support versioning.

---

## Example

```text id="u9m1qp"
UserRegistered:v1
UserRegistered:v2
```

---

## Rules

| Rule                    | Mandatory |
| ----------------------- | --------- |
| Backward compatibility  | Yes       |
| Explicit schema version | Yes       |
| Consumer compatibility  | Yes       |

```
```
