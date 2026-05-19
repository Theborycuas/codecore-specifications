# 09-subscription-management/api-contracts.md

````md id="y8x4vp"
# Subscription Management API Contracts

## 1. Introduction

This document defines the API contracts exposed by the Subscription Management module.

The APIs provide capabilities related to:

- Subscription lifecycle management
- Plan retrieval
- Entitlement evaluation
- Quota validation
- Usage metering
- Trial management
- Upgrade and downgrade orchestration
- Add-on management
- Renewal handling
- Commercial governance

The contracts are designed following:

- RESTful principles
- Reactive API architecture
- Multi-tenant SaaS isolation
- Enterprise monetization governance
- Event-driven workflows
- Distributed entitlement orchestration

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Tenant-aware APIs | Mandatory isolation |
| Reactive-first design | Scalability |
| Idempotent operations | Retry safety |
| Commercial consistency | Lifecycle integrity |
| Event-driven side effects | Distributed orchestration |
| Versioned contracts | Backward compatibility |

---

# 3. Base URL

```text id="u5m1wr"
/api/v1/subscriptions
````

---

# 4. Common Headers

| Header           | Required    | Description         |
| ---------------- | ----------- | ------------------- |
| Authorization    | Yes         | Bearer JWT          |
| X-Tenant-ID      | Yes         | Tenant context      |
| X-Correlation-ID | Recommended | Distributed tracing |
| Content-Type     | Yes         | Request mime type   |

---

# 5. Subscription APIs

# 5.1 Create Subscription

## Endpoint

```text id="m8v3xp"
POST /
```

---

## Purpose

Creates a new tenant subscription.

---

## Request

```json id="f2x7wr"
{
  "tenantId": "uuid",
  "planCode": "PRO",
  "billingCycle": "MONTHLY"
}
```

---

## Response

```json id="r4m9vt"
{
  "success": true,
  "data": {
    "subscriptionId": "uuid",
    "state": "ACTIVE"
  }
}
```

---

## Side Effects

```text id="x9v1wr"
- Entitlements assigned
- Quotas configured
- Events emitted
```

---

# 5.2 Retrieve Subscription

## Endpoint

```text id="k3m8xp"
GET /{subscriptionId}
```

---

## Response

```json id="p1v9wr"
{
  "success": true,
  "data": {
    "subscriptionId": "uuid",
    "tenantId": "uuid",
    "planCode": "BUSINESS",
    "state": "ACTIVE"
  }
}
```

---

## Security Rules

* Tenant ownership validation mandatory

---

# 5.3 Retrieve Tenant Subscription

## Endpoint

```text id="g6m2xt"
GET /tenant/{tenantId}
```

---

## Purpose

Retrieves active tenant subscription.

---

# 5.4 Cancel Subscription

## Endpoint

```text id="u7m1wr"
POST /{subscriptionId}/cancel
```

---

## Request

```json id="m4v8wr"
{
  "reason": "MANUAL_CANCELLATION"
}
```

---

## Side Effects

```text id="t5v3xp"
- Future renewals disabled
- Cancellation events emitted
```

---

# 5.5 Suspend Subscription

## Endpoint

```text id="w2m8vt"
POST /{subscriptionId}/suspend
```

---

## Request

```json id="q7x1wr"
{
  "reason": "PAYMENT_FAILURE"
}
```

---

## Effects

```text id="y9v4xp"
- Premium access restricted
- API throttling enabled
```

---

# 5.6 Reactivate Subscription

## Endpoint

```text id="f4m7wr"
POST /{subscriptionId}/reactivate
```

---

## Purpose

Restores suspended subscription access.

---

# 6. Plan APIs

# 6.1 Retrieve Available Plans

## Endpoint

```text id="u1x8vt"
GET /plans
```

---

## Response

```json id="m6v2wr"
{
  "success": true,
  "data": [
    {
      "code": "FREE"
    },
    {
      "code": "PRO"
    }
  ]
}
```

---

# 6.2 Retrieve Plan Details

## Endpoint

```text id="g3x9vp"
GET /plans/{planCode}
```

---

## Purpose

Returns detailed plan configuration.

---

## Includes

| Capability         | Included |
| ------------------ | -------- |
| Entitlements       | Yes      |
| Quotas             | Yes      |
| Pricing references | Yes      |

---

# 7. Upgrade and Downgrade APIs

# 7.1 Upgrade Subscription

## Endpoint

```text id="r5m1xt"
POST /{subscriptionId}/upgrade
```

---

## Request

```json id="x8v4wr"
{
  "targetPlan": "BUSINESS"
}
```

---

## Side Effects

```text id="n7m1vt"
- Entitlements expanded
- Quotas increased
- Upgrade event emitted
```

---

# 7.2 Downgrade Subscription

## Endpoint

```text id="k2v7xp"
POST /{subscriptionId}/downgrade
```

---

## Request

```json id="d1m8wr"
{
  "targetPlan": "PRO"
}
```

---

## Important Rule

```text id="h6x2vt"
Downgrades must validate quota compatibility
```

---

# 8. Entitlement APIs

# 8.1 Retrieve Tenant Entitlements

## Endpoint

```text id="t9v4xp"
GET /tenant/{tenantId}/entitlements
```

---

## Response

```json id="j4x9wt"
{
  "success": true,
  "data": [
    "AI_REPORTING",
    "OCR_PROCESSING"
  ]
}
```

---

# 8.2 Validate Entitlement

## Endpoint

```text id="m7v1xp"
POST /tenant/{tenantId}/entitlements/validate
```

---

## Request

```json id="u5x8wr"
{
  "entitlementCode": "AI_REPORTING"
}
```

---

## Response

```json id="q9m3vt"
{
  "success": true,
  "data": {
    "allowed": true
  }
}
```

---

## Characteristics

| Characteristic | Description |
| -------------- | ----------- |
| Low latency    | Critical    |
| Cache-friendly | Recommended |
| Runtime-safe   | Mandatory   |

---

# 9. Quota APIs

# 9.1 Retrieve Tenant Quotas

## Endpoint

```text id="k1m8vt"
GET /tenant/{tenantId}/quotas
```

---

## Response

```json id="d2m8wr"
{
  "success": true,
  "data": [
    {
      "quotaType": "MAX_STORAGE",
      "limitValue": 100
    }
  ]
}
```

---

# 9.2 Validate Quota Usage

## Endpoint

```text id="u8x3wp"
POST /tenant/{tenantId}/quotas/validate
```

---

## Request

```json id="f6m9wr"
{
  "quotaType": "MAX_STORAGE",
  "requestedAmount": 5
}
```

---

## Response

```json id="c8m4xt"
{
  "success": true,
  "data": {
    "allowed": true,
    "remaining": 12
  }
}
```

---

## Important Rule

```text id="u1x8wr"
Quota validation
must execute before resource allocation
```

---

# 9.3 Consume Quota

## Endpoint

```text id="w6x3wr"
POST /tenant/{tenantId}/quotas/consume
```

---

## Purpose

Registers resource usage.

---

# 10. Usage Metering APIs

# 10.1 Record Usage

## Endpoint

```text id="r1m7vp"
POST /usage
```

---

## Request

```json id="x4v8xt"
{
  "tenantId": "uuid",
  "resourceType": "AI_TOKENS",
  "consumedAmount": 500
}
```

---

## Side Effects

```text id="f2v9xp"
- Usage aggregation
- Quota recalculation
- Threshold validation
```

---

# 10.2 Retrieve Usage Metrics

## Endpoint

```text id="m6x3vt"
GET /tenant/{tenantId}/usage
```

---

## Query Parameters

| Parameter    | Description      |
| ------------ | ---------------- |
| resourceType | Metered category |
| from         | Start date       |
| to           | End date         |

---

# 11. Trial APIs

# 11.1 Start Trial

## Endpoint

```text id="y5v2wp"
POST /tenant/{tenantId}/trial
```

---

## Request

```json id="m2x7wp"
{
  "trialPeriod": "P14D"
}
```

---

## Critical Rules

| Rule                        | Description           |
| --------------------------- | --------------------- |
| Duplicate trials restricted | Commercial protection |
| Expiration mandatory        | Governance            |

---

# 11.2 Retrieve Trial Status

## Endpoint

```text id="q6v3xt"
GET /tenant/{tenantId}/trial
```

---

# 12. Addon APIs

# 12.1 Activate Addon

## Endpoint

```text id="h4m9wr"
POST /{subscriptionId}/addons
```

---

## Request

```json id="d1x8vp"
{
  "addonCode": "AI_PACKAGE"
}
```

---

## Side Effects

```text id="v7m2xt"
- Quotas expanded
- Entitlements enabled
```

---

# 12.2 Remove Addon

## Endpoint

```text id="u5m1wr"
DELETE /{subscriptionId}/addons/{addonCode}
```

---

## Important Consideration

Removal may trigger quota reconciliation.

---

# 13. Renewal APIs

# 13.1 Renew Subscription

## Endpoint

```text id="m8v3xp"
POST /{subscriptionId}/renew
```

---

## Request

```json id="f2x7wr"
{
  "renewalPeriod": "P1M"
}
```

---

## Side Effects

```text id="r4m9vt"
- Expiration extended
- Renewal event emitted
```

---

# 13.2 Retrieve Renewal Status

## Endpoint

```text id="x9v1wr"
GET /{subscriptionId}/renewal-status
```

---

# 14. Overage APIs

# 14.1 Retrieve Overage Status

## Endpoint

```text id="k3m8xp"
GET /tenant/{tenantId}/overage
```

---

## Purpose

Returns excess resource consumption.

---

# 14.2 Resolve Overage

## Endpoint

```text id="p1v9wr"
POST /tenant/{tenantId}/overage/resolve
```

---

## Resolution Strategies

```text id="g6m2xt"
- Upgrade subscription
- Purchase add-on
- Reduce usage
```

---

# 15. Feature Flag APIs

# 15.1 Retrieve Enabled Features

## Endpoint

```text id="u7m1wr"
GET /tenant/{tenantId}/features
```

---

## Purpose

Returns runtime-enabled capabilities.

---

# 16. Commercial Analytics APIs

# 16.1 Retrieve Subscription Metrics

## Endpoint

```text id="m4v8wr"
GET /analytics/subscriptions
```

---

## Examples

```text id="t5v3xp"
- Active subscriptions
- Trial conversions
- Upgrade rates
```

---

# 16.2 Retrieve Revenue Metrics

## Endpoint

```text id="w2m8vt"
GET /analytics/revenue
```

---

# 17. Common Response Structure

## Success Response

```json id="q7x1wr"
{
  "success": true,
  "timestamp": "2026-05-20T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="y9v4xp"
{
  "success": false,
  "timestamp": "2026-05-20T10:00:00Z",
  "error": {
    "code": "QUOTA_EXCEEDED",
    "message": "Storage limit exceeded"
  }
}
```

---

# 18. HTTP Status Codes

| Status | Meaning                 |
| ------ | ----------------------- |
| 200    | Success                 |
| 201    | Created                 |
| 202    | Async processing        |
| 400    | Validation error        |
| 401    | Unauthenticated         |
| 403    | Forbidden               |
| 404    | Resource not found      |
| 409    | Conflict                |
| 422    | Business rule violation |
| 429    | Rate limit exceeded     |
| 500    | Internal error          |

---

# 19. Pagination Standards

Paginated endpoints should return:

```json id="f4m7wr"
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 100
  }
}
```

---

# 20. Security Rules

## Mandatory Protections

| Protection                        | Required |
| --------------------------------- | -------- |
| Tenant isolation                  | Yes      |
| Subscription ownership validation | Yes      |
| Quota enforcement                 | Yes      |
| Entitlement validation            | Yes      |

---

## Forbidden Behavior

```text id="u1x8vt"
Expired subscriptions
must not bypass API restrictions
```

---

# 21. Reactive API Considerations

Reactive implementations should support:

```text id="m6v2wr"
Mono<SubscriptionResponse>
Flux<UsageMetric>
```

---

## Requirements

* Non-blocking entitlement validation
* Reactive quota evaluation
* High concurrency support
* Async event propagation

---

# 22. CQRS Considerations

Recommended projections:

| Projection             | Purpose              |
| ---------------------- | -------------------- |
| SubscriptionProjection | Fast retrieval       |
| UsageProjection        | Metering analytics   |
| RevenueProjection      | Commercial reporting |
| QuotaProjection        | Runtime enforcement  |

---

# 23. Distributed System Considerations

The APIs support:

* Multi-region SaaS deployments
* Distributed entitlement caches
* Event-driven synchronization
* Horizontal scalability
* Reactive commercial orchestration

---

# 24. API Versioning Strategy

Recommended:

```text id="g3x9vp"
/api/v1/subscriptions
```

Future evolution:

```text id="r5m1xt"
/api/v2/subscriptions
```

---

# 25. Error Codes

| Code                   | Description            |
| ---------------------- | ---------------------- |
| SUBSCRIPTION_NOT_FOUND | Missing subscription   |
| QUOTA_EXCEEDED         | Resource limit reached |
| ENTITLEMENT_DENIED     | Feature restricted     |
| PLAN_NOT_AVAILABLE     | Invalid plan           |
| TRIAL_ALREADY_USED     | Trial restriction      |
| SUBSCRIPTION_EXPIRED   | Lifecycle restriction  |
| SUBSCRIPTION_SUSPENDED | Commercial restriction |
| INVALID_DOWNGRADE      | Compatibility failure  |

---

# 26. Future API Extensions

Future APIs may include:

* Usage-based billing APIs
* Seat licensing APIs
* Marketplace subscription APIs
* AI consumption APIs
* Enterprise contract APIs
* Dynamic pricing APIs

---

# 27. Summary

The Subscription Management API contracts provide:

* Enterprise-grade subscription lifecycle APIs
* Multi-tenant commercial isolation
* Reactive entitlement orchestration
* Real-time quota governance
* Distributed usage metering
* SaaS monetization consistency
* Scalable commercial feature management

These APIs form the external contract layer of the subscription ecosystem.

```
```
