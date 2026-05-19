# 09-subscription-management/entities.md

````md id="u8x4vp"
# Subscription Management Entities

## 1. Introduction

This document defines the entities of the Subscription Management module.

Entities represent domain objects that:

- Possess identity
- Maintain commercial lifecycle continuity
- Govern SaaS monetization
- Coordinate entitlements
- Enforce quotas
- Preserve tenant isolation
- Support subscription transitions
- Enable usage governance

The entities are designed following:

- Domain-Driven Design (DDD)
- Multi-tenant SaaS architecture
- Event-driven monetization workflows
- Reactive commercial orchestration
- Enterprise subscription governance

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| Subscription | Core tenant subscription |
| SubscriptionPlan | Commercial plan |
| Entitlement | Feature capability |
| Quota | Usage limit |
| QuotaConsumption | Resource usage |
| TrialSubscription | Trial lifecycle |
| SubscriptionTransition | Upgrade/downgrade |
| SubscriptionRenewal | Renewal lifecycle |
| SubscriptionSuspension | Commercial restriction |
| SubscriptionAddon | Optional capability |
| PricingModel | Commercial pricing |
| UsageRecord | Metered usage |
| UsagePolicy | Consumption governance |
| GracePeriod | Temporary access continuation |
| FeatureFlagBinding | Feature enablement |
| SubscriptionAuditReference | Audit traceability |
| PlanFeature | Plan capability mapping |
| TenantCommercialProfile | Tenant monetization profile |
| OverageRecord | Excess resource usage |
| BillingReference | Billing linkage |

---

# 3. Subscription Entity

## Purpose

Represents the primary commercial agreement between tenant and platform.

Central entity of the module.

---

## Identity

```text id="u5m1wr"
subscriptionId
````

---

## Attributes

| Attribute      | Type    | Description             |
| -------------- | ------- | ----------------------- |
| subscriptionId | UUID    | Subscription identifier |
| tenantId       | UUID    | Owning tenant           |
| planId         | UUID    | Assigned plan           |
| state          | String  | Lifecycle state         |
| startsAt       | Instant | Activation timestamp    |
| expiresAt      | Instant | Expiration timestamp    |
| autoRenew      | Boolean | Renewal behavior        |
| createdAt      | Instant | Creation timestamp      |

---

## Lifecycle States

```text id="m8v3xp"
TRIAL
ACTIVE
PAST_DUE
SUSPENDED
CANCELED
EXPIRED
```

---

## Behaviors

| Behavior   | Description          |
| ---------- | -------------------- |
| activate() | Enables subscription |
| suspend()  | Restricts access     |
| cancel()   | Terminates lifecycle |
| renew()    | Extends validity     |

---

## Business Rules

* One active subscription per tenant
* Expired subscriptions lose premium access
* Tenant ownership mandatory

---

# 4. SubscriptionPlan Entity

## Purpose

Represents a predefined commercial offering.

---

## Identity

```text id="f2x7wr"
planId
```

---

## Attributes

| Attribute   | Type    | Description        |
| ----------- | ------- | ------------------ |
| planId      | UUID    | Plan identifier    |
| code        | String  | Unique plan code   |
| name        | String  | Commercial name    |
| description | String  | Plan details       |
| active      | Boolean | Availability       |
| createdAt   | Instant | Creation timestamp |

---

## Examples

```text id="r4m9vt"
FREE
PRO
BUSINESS
ENTERPRISE
```

---

## Behaviors

| Behavior         | Description   |
| ---------------- | ------------- |
| activatePlan()   | Enables plan  |
| deactivatePlan() | Disables plan |

---

## Business Rules

* Plan codes unique
* Historical plans preserved
* Plan deletion discouraged

---

# 5. Entitlement Entity

## Purpose

Represents an enabled commercial capability.

---

## Identity

```text id="x9v1wr"
entitlementId
```

---

## Attributes

| Attribute       | Type    | Description            |
| --------------- | ------- | ---------------------- |
| entitlementId   | UUID    | Entitlement identifier |
| entitlementCode | String  | Capability code        |
| enabled         | Boolean | Activation status      |
| tenantId        | UUID    | Tenant scope           |

---

## Examples

```text id="k3m8xp"
AI_REPORTING
OCR_PROCESSING
ADVANCED_ANALYTICS
```

---

## Behaviors

| Behavior  | Description    |
| --------- | -------------- |
| enable()  | Grants access  |
| disable() | Revokes access |

---

## Critical Rule

```text id="p1v9wr"
Entitlements are tenant-scoped
```

---

# 6. Quota Entity

## Purpose

Represents operational resource limits.

---

## Identity

```text id="g6m2xt"
quotaId
```

---

## Attributes

| Attribute  | Type   | Description       |
| ---------- | ------ | ----------------- |
| quotaId    | UUID   | Quota identifier  |
| quotaType  | String | Resource category |
| limitValue | Long   | Allowed maximum   |
| tenantId   | UUID   | Tenant scope      |

---

## Examples

| Quota        | Example  |
| ------------ | -------- |
| MAX_USERS    | 20       |
| STORAGE_GB   | 100      |
| API_REQUESTS | 1000/min |

---

## Behaviors

| Behavior        | Description           |
| --------------- | --------------------- |
| validateUsage() | Validates consumption |
| increaseLimit() | Expands quota         |

---

## Business Rules

* Negative quotas forbidden
* Tenant ownership mandatory

---

# 7. QuotaConsumption Entity

## Purpose

Represents resource consumption tracking.

---

## Identity

```text id="u7m1wr"
quotaConsumptionId
```

---

## Attributes

| Attribute     | Type    | Description           |
| ------------- | ------- | --------------------- |
| consumedValue | Long    | Resource usage        |
| measuredAt    | Instant | Measurement timestamp |
| quotaId       | UUID    | Associated quota      |

---

## Behaviors

| Behavior  | Description      |
| --------- | ---------------- |
| consume() | Registers usage  |
| release() | Returns capacity |

---

# 8. TrialSubscription Entity

## Purpose

Represents temporary evaluation subscriptions.

---

## Identity

```text id="m4v8wr"
trialSubscriptionId
```

---

## Attributes

| Attribute | Type    | Description       |
| --------- | ------- | ----------------- |
| tenantId  | UUID    | Tenant owner      |
| startsAt  | Instant | Trial start       |
| expiresAt | Instant | Trial expiration  |
| converted | Boolean | Conversion status |

---

## Trial States

```text id="t5v3xp"
TRIAL_ACTIVE
TRIAL_EXPIRED
TRIAL_CONVERTED
```

---

## Behaviors

| Behavior        | Description               |
| --------------- | ------------------------- |
| expireTrial()   | Ends trial                |
| convertToPaid() | Creates paid subscription |

---

## Business Rules

* Trial expiration mandatory
* Trial abuse prevention required

---

# 9. SubscriptionTransition Entity

## Purpose

Represents plan upgrades and downgrades.

---

## Identity

```text id="w2m8vt"
transitionId
```

---

## Attributes

| Attribute      | Type    | Description         |
| -------------- | ------- | ------------------- |
| fromPlanId     | UUID    | Previous plan       |
| toPlanId       | UUID    | Target plan         |
| transitionType | String  | Upgrade/downgrade   |
| executedAt     | Instant | Execution timestamp |

---

## Examples

```text id="q7x1wr"
FREE → PRO
BUSINESS → ENTERPRISE
```

---

## Behaviors

| Behavior            | Description         |
| ------------------- | ------------------- |
| executeTransition() | Applies plan change |

---

## Critical Rule

Downgrades must validate quota compatibility.

---

# 10. SubscriptionRenewal Entity

## Purpose

Represents renewal lifecycle management.

---

## Identity

```text id="y9v4xp"
renewalId
```

---

## Attributes

| Attribute      | Type     | Description            |
| -------------- | -------- | ---------------------- |
| subscriptionId | UUID     | Subscription reference |
| renewedAt      | Instant  | Renewal timestamp      |
| renewalPeriod  | Duration | Extension period       |

---

## Behaviors

| Behavior            | Description      |
| ------------------- | ---------------- |
| renewSubscription() | Extends validity |

---

# 11. SubscriptionSuspension Entity

## Purpose

Represents temporary commercial restriction.

---

## Identity

```text id="f4m7wr"
suspensionId
```

---

## Attributes

| Attribute           | Type    | Description          |
| ------------------- | ------- | -------------------- |
| reason              | String  | Suspension rationale |
| suspendedAt         | Instant | Suspension timestamp |
| reactivationAllowed | Boolean | Recovery possibility |

---

## Suspension Triggers

```text id="u1x8vt"
PAYMENT_FAILURE
ABUSE_DETECTED
COMPLIANCE_ISSUE
```

---

## Behaviors

| Behavior        | Description     |
| --------------- | --------------- |
| suspendAccess() | Restricts usage |
| reactivate()    | Restores access |

---

# 12. SubscriptionAddon Entity

## Purpose

Represents optional commercial extensions.

---

## Identity

```text id="m6v2wr"
addonId
```

---

## Examples

```text id="g3x9vp"
- Additional storage
- AI package
- Extended retention
```

---

## Behaviors

| Behavior          | Description        |
| ----------------- | ------------------ |
| activateAddon()   | Enables extension  |
| deactivateAddon() | Disables extension |

---

# 13. PricingModel Entity

## Purpose

Represents commercial pricing configuration.

---

## Identity

```text id="r5m1xt"
pricingModelId
```

---

## Pricing Types

```text id="x8v4wr"
FIXED
USAGE_BASED
SEAT_BASED
HYBRID
```

---

## Attributes

| Attribute | Type    | Description       |
| --------- | ------- | ----------------- |
| currency  | String  | Pricing currency  |
| amount    | Decimal | Base price        |
| recurring | Boolean | Recurring pricing |

---

## Behaviors

| Behavior        | Description        |
| --------------- | ------------------ |
| calculateCost() | Pricing evaluation |

---

# 14. UsageRecord Entity

## Purpose

Represents metered resource usage.

---

## Identity

```text id="n7m1vt"
usageRecordId
```

---

## Attributes

| Attribute      | Type    | Description      |
| -------------- | ------- | ---------------- |
| resourceType   | String  | Metered category |
| consumedAmount | Long    | Usage value      |
| measuredAt     | Instant | Usage timestamp  |

---

## Metered Examples

```text id="k2v7xp"
- Storage
- AI tokens
- API requests
```

---

# 15. UsagePolicy Entity

## Purpose

Represents usage governance rules.

---

## Identity

```text id="d1m8wr"
usagePolicyId
```

---

## Policy Examples

```text id="h6x2vt"
HARD_LIMIT
SOFT_LIMIT
GRACE_PERIOD
BURST_ALLOWED
```

---

## Behaviors

| Behavior        | Description           |
| --------------- | --------------------- |
| evaluateUsage() | Governance validation |

---

# 16. GracePeriod Entity

## Purpose

Represents temporary continuation of access.

---

## Identity

```text id="t9v4xp"
gracePeriodId
```

---

## Attributes

| Attribute | Type    | Description          |
| --------- | ------- | -------------------- |
| startsAt  | Instant | Grace start          |
| expiresAt | Instant | Grace end            |
| reason    | String  | Governance rationale |

---

## Behaviors

| Behavior    | Description            |
| ----------- | ---------------------- |
| isExpired() | Validates continuation |

---

# 17. FeatureFlagBinding Entity

## Purpose

Represents linkage between entitlements and runtime feature flags.

---

## Identity

```text id="j4x9wt"
featureBindingId
```

---

## Examples

```text id="m7v1xp"
ENTITLEMENT_AI_REPORTS
→ feature.ai.reports.enabled
```

---

# 18. SubscriptionAuditReference Entity

## Purpose

Represents audit traceability linkage.

---

## Identity

```text id="u5x8wr"
auditReferenceId
```

---

## Usage

Supports:

* Commercial auditing
* Compliance traceability
* Historical reconstruction

---

# 19. PlanFeature Entity

## Purpose

Represents feature mappings within plans.

---

## Identity

```text id="q9m3vt"
planFeatureId
```

---

## Examples

```text id="k1m8vt"
PRO
→ ADVANCED_ANALYTICS
```

---

# 20. TenantCommercialProfile Entity

## Purpose

Represents tenant monetization context.

---

## Identity

```text id="d2m8wr"
commercialProfileId
```

---

## Attributes

| Attribute   | Type   | Description          |
| ----------- | ------ | -------------------- |
| tenantId    | UUID   | Tenant owner         |
| currentPlan | String | Active plan          |
| usageTier   | String | Consumption category |

---

# 21. OverageRecord Entity

## Purpose

Represents excess quota usage.

---

## Identity

```text id="u8x3wp"
overageRecordId
```

---

## Behaviors

| Behavior           | Description        |
| ------------------ | ------------------ |
| calculateOverage() | Excess computation |

---

## Usage

Supports:

* Usage-based billing
* Enforcement policies
* Analytics

---

# 22. BillingReference Entity

## Purpose

Represents linkage with Billing Management.

---

## Identity

```text id="f6m9wr"
billingReferenceId
```

---

## Important Principle

```text id="c8m4xt"
Billing responsibilities
remain external
```

---

# 23. Entity Relationships

```text id="u1x8wr"
Subscription
    ├── uses -> SubscriptionPlan
    ├── owns -> Entitlement
    ├── governed by -> Quota
    ├── measured by -> UsageRecord
    ├── extended by -> SubscriptionAddon
    ├── linked to -> PricingModel
    └── governed by -> UsagePolicy
```

---

# 24. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="w6x3wr"
- Subscription
- Entitlement
- Quota
- UsageRecord
- TrialSubscription
```

---

# 25. Security-Critical Rules

## Mandatory Protections

| Protection              | Required |
| ----------------------- | -------- |
| Tenant isolation        | Yes      |
| Quota enforcement       | Yes      |
| Subscription validation | Yes      |
| Entitlement validation  | Yes      |

---

## Forbidden Behavior

```text id="r1m7vp"
Expired subscriptions
must not retain premium capabilities
```

---

# 26. Lifecycle Considerations

| Entity            | Lifecycle        |
| ----------------- | ---------------- |
| Subscription      | Long-term        |
| TrialSubscription | Temporary        |
| UsageRecord       | High-frequency   |
| Entitlement       | Runtime-critical |
| OverageRecord     | Analytical       |

---

# 27. Future Entity Extensions

Future entities may include:

* EnterpriseContract
* RegionalPricing
* MarketplaceSubscription
* SeatLicense
* AIConsumptionPlan
* DynamicPricingRule

---

# 28. Summary

The Subscription Management entities provide:

* Enterprise-grade subscription lifecycle modeling
* Multi-tenant commercial isolation
* Reactive entitlement orchestration
* Real-time quota governance
* Distributed usage metering
* SaaS monetization consistency
* Scalable commercial feature management

These entities form the operational foundation of the subscription ecosystem.

```
```
