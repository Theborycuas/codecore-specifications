# 09-subscription-management/aggregates.md

````md id="t8x4vp"
# Subscription Management Aggregates

## 1. Introduction

This document defines the aggregates of the Subscription Management module.

Aggregates represent the transactional consistency boundaries of the subscription domain and encapsulate:

- Subscription lifecycle management
- Plan assignment
- Entitlement orchestration
- Quota enforcement
- Trial management
- Commercial transitions
- Usage governance
- Subscription state consistency
- Tenant commercial isolation

The aggregates are designed following:

- Domain-Driven Design (DDD)
- Event-Driven Architecture (EDA)
- Hexagonal Architecture
- Multi-tenant SaaS principles
- Reactive commercial orchestration
- Enterprise monetization standards

---

# 2. Aggregate Overview

| Aggregate | Responsibility |
|---|---|
| SubscriptionAggregate | Core subscription lifecycle |
| SubscriptionPlanAggregate | Commercial plan management |
| EntitlementAggregate | Feature enablement |
| QuotaAggregate | Usage limits |
| TrialAggregate | Trial lifecycle |
| UsageMeteringAggregate | Usage tracking |
| SubscriptionTransitionAggregate | Upgrades and downgrades |
| SubscriptionBillingLinkAggregate | Billing linkage |
| SubscriptionSuspensionAggregate | Commercial suspension |
| AddonAggregate | Add-on commercial capabilities |
| PricingAggregate | Pricing configuration |
| UsagePolicyAggregate | Consumption governance |

---

# 3. SubscriptionAggregate

## Purpose

Represents the central aggregate of the module.

Controls the lifecycle of a tenant subscription.

---

## Aggregate Root

```text id="u5m1wr"
Subscription
````

---

## Responsibilities

* Manage subscription lifecycle
* Assign plans
* Coordinate entitlements
* Validate active access
* Handle renewals
* Coordinate expiration
* Enforce tenant commercial isolation

---

## Invariants

| Invariant                          | Description            |
| ---------------------------------- | ---------------------- |
| One active subscription per tenant | Commercial consistency |
| Subscription must belong to tenant | Isolation              |
| Lifecycle transitions validated    | Integrity              |
| Expired subscriptions restricted   | Governance             |

---

## Example Structure

```text id="m8v3xp"
SubscriptionAggregate
│
├── Subscription (Root)
├── AssignedPlan
├── SubscriptionState
├── RenewalPolicy
├── TrialReference
└── ActiveEntitlements
```

---

## Lifecycle States

```text id="f2x7wr"
TRIAL
ACTIVE
PAST_DUE
SUSPENDED
CANCELED
EXPIRED
```

---

## Important Behaviors

### activate()

Transitions subscription into ACTIVE state.

---

### suspend()

Restricts commercial access.

---

### expire()

Ends subscription lifecycle.

---

### renew()

Extends operational validity.

---

# 4. SubscriptionPlanAggregate

## Purpose

Represents commercial plans offered by the platform.

---

## Aggregate Root

```text id="r4m9vt"
SubscriptionPlan
```

---

## Responsibilities

* Define plan capabilities
* Define pricing references
* Define quota templates
* Define entitlement templates

---

## Plan Examples

```text id="x9v1wr"
FREE
PRO
BUSINESS
ENTERPRISE
```

---

## Invariants

| Invariant               | Description  |
| ----------------------- | ------------ |
| Plan identity immutable | Traceability |
| Plan code unique        | Consistency  |
| Quotas non-negative     | Validation   |

---

## Example Structure

```text id="k3m8xp"
SubscriptionPlanAggregate
│
├── SubscriptionPlan (Root)
├── PlanFeatures
├── PlanQuotas
├── PricingReference
└── CommercialMetadata
```

---

# 5. EntitlementAggregate

## Purpose

Represents enabled commercial capabilities.

---

## Aggregate Root

```text id="p1v9wr"
Entitlement
```

---

## Responsibilities

* Enable platform capabilities
* Restrict premium functionality
* Coordinate feature access

---

## Example Entitlements

```text id="g6m2xt"
FILE_STORAGE
ADVANCED_ANALYTICS
AI_REPORTING
OCR_PROCESSING
```

---

## Critical Rule

```text id="u7m1wr"
Entitlements are tenant-scoped
```

---

## Example Structure

```text id="m4v8wr"
EntitlementAggregate
│
├── Entitlement (Root)
├── EntitlementType
├── ActivationRules
└── VisibilityScope
```

---

# 6. QuotaAggregate

## Purpose

Represents operational usage limits.

---

## Aggregate Root

```text id="t5v3xp"
Quota
```

---

## Responsibilities

* Define resource limits
* Validate resource consumption
* Prevent quota abuse

---

## Example Quotas

| Resource      | Example   |
| ------------- | --------- |
| Users         | 20        |
| Storage       | 100 GB    |
| API requests  | 1000/min  |
| OCR documents | 500/month |

---

## Invariants

| Invariant                 | Description |
| ------------------------- | ----------- |
| Negative quotas forbidden | Validation  |
| Quota usage tracked       | Governance  |
| Quota ownership mandatory | Isolation   |

---

## Important Behaviors

### validateUsage()

Determines if operation may proceed.

---

### consume()

Registers resource usage.

---

### release()

Returns quota capacity.

---

# 7. TrialAggregate

## Purpose

Represents temporary evaluation access.

---

## Aggregate Root

```text id="w2m8vt"
TrialSubscription
```

---

## Responsibilities

* Manage trial lifecycle
* Restrict temporary access
* Track trial expiration

---

## Trial States

```text id="q7x1wr"
TRIAL_ACTIVE
TRIAL_EXPIRED
TRIAL_CONVERTED
```

---

## Critical Rules

| Rule                            | Description           |
| ------------------------------- | --------------------- |
| Trial expiration mandatory      | Governance            |
| Trial abuse prevention required | Commercial protection |

---

# 8. UsageMeteringAggregate

## Purpose

Tracks commercial resource consumption.

---

## Aggregate Root

```text id="y9v4xp"
UsageRecord
```

---

## Responsibilities

* Track usage
* Support metered billing
* Generate analytics
* Enable quota validation

---

## Metered Resources

```text id="f4m7wr"
- Storage usage
- AI tokens
- OCR operations
- API requests
```

---

## Example Structure

```text id="u1x8vt"
UsageMeteringAggregate
│
├── UsageRecord (Root)
├── ResourceType
├── ConsumptionWindow
└── UsageMetrics
```

---

# 9. SubscriptionTransitionAggregate

## Purpose

Represents upgrades and downgrades.

---

## Aggregate Root

```text id="m6v2wr"
SubscriptionTransition
```

---

## Responsibilities

* Coordinate upgrades
* Coordinate downgrades
* Validate compatibility
* Preserve lifecycle integrity

---

## Example Transitions

```text id="g3x9vp"
FREE → PRO
PRO → BUSINESS
BUSINESS → ENTERPRISE
```

---

## Downgrade Risks

| Risk               | Example               |
| ------------------ | --------------------- |
| User overage       | Too many users        |
| Storage overage    | Excess storage        |
| Feature dependency | Restricted capability |

---

# 10. SubscriptionBillingLinkAggregate

## Purpose

Represents linkage between subscriptions and billing systems.

---

## Aggregate Root

```text id="r5m1xt"
SubscriptionBillingLink
```

---

## Responsibilities

* Maintain billing references
* Coordinate invoice linkage
* Support renewal orchestration

---

## Important Principle

```text id="x8v4wr"
Billing logic
must remain external
```

---

# 11. SubscriptionSuspensionAggregate

## Purpose

Represents commercial access restrictions.

---

## Aggregate Root

```text id="n7m1vt"
SubscriptionSuspension
```

---

## Responsibilities

* Restrict premium access
* Preserve tenant state
* Support reactivation

---

## Suspension Triggers

```text id="k2v7xp"
- Payment failure
- Abuse detection
- Compliance issue
```

---

## Effects

```text id="d1m8wr"
- Upload restrictions
- Feature limitations
- API throttling
```

---

# 12. AddonAggregate

## Purpose

Represents optional commercial extensions.

---

## Aggregate Root

```text id="h6x2vt"
SubscriptionAddon
```

---

## Examples

```text id="t9v4xp"
- Additional storage
- AI package
- Extra API capacity
- Extended retention
```

---

## Important Principle

Add-ons extend plans without replacing them.

---

# 13. PricingAggregate

## Purpose

Represents pricing configuration references.

---

## Aggregate Root

```text id="j4x9wt"
PricingModel
```

---

## Responsibilities

* Store pricing references
* Support regional pricing
* Enable dynamic pricing models

---

## Pricing Types

```text id="m7v1xp"
FIXED
USAGE_BASED
SEAT_BASED
HYBRID
```

---

# 14. UsagePolicyAggregate

## Purpose

Represents commercial usage governance.

---

## Aggregate Root

```text id="u5x8wr"
UsagePolicy
```

---

## Responsibilities

* Define throttling
* Define overage handling
* Define grace periods
* Define burst limits

---

## Example Policies

| Policy       | Example           |
| ------------ | ----------------- |
| Burst policy | Temporary overage |
| Grace period | 3 days            |
| Hard limit   | Strict rejection  |

---

# 15. Aggregate Relationships

```text id="q9m3vt"
SubscriptionAggregate
    ├── uses -> SubscriptionPlanAggregate
    ├── owns -> EntitlementAggregate
    ├── governed by -> QuotaAggregate
    ├── linked to -> TrialAggregate
    ├── measured by -> UsageMeteringAggregate
    ├── transitions through -> SubscriptionTransitionAggregate
    ├── linked to -> SubscriptionBillingLinkAggregate
    └── extended by -> AddonAggregate
```

---

# 16. Aggregate Transaction Boundaries

## Strong Consistency Required

| Aggregate                       | Reason                 |
| ------------------------------- | ---------------------- |
| SubscriptionAggregate           | Lifecycle integrity    |
| EntitlementAggregate            | Access governance      |
| QuotaAggregate                  | Usage enforcement      |
| SubscriptionTransitionAggregate | Commercial correctness |

---

## Eventual Consistency Acceptable

| Aggregate               | Reason             |
| ----------------------- | ------------------ |
| Usage analytics         | Reporting          |
| Billing synchronization | Async coordination |
| Observability metrics   | Monitoring         |

---

# 17. CQRS Compatibility

Recommended projections:

| Projection                      | Purpose                |
| ------------------------------- | ---------------------- |
| TenantSubscriptionProjection    | Fast access validation |
| QuotaUsageProjection            | Runtime quota checks   |
| SubscriptionAnalyticsProjection | Commercial analytics   |
| RevenueProjection               | Business reporting     |

---

# 18. Multi-Tenant Isolation Rules

Critical rule:

```text id="k1m8vt"
Tenant subscriptions
must never influence
other tenants
```

---

## Mandatory Protections

| Protection                 | Required |
| -------------------------- | -------- |
| Tenant-scoped quotas       | Yes      |
| Tenant-scoped entitlements | Yes      |
| Tenant lifecycle isolation | Yes      |

---

# 19. Reactive Considerations

Reactive implementations should support:

```text id="d2m8wr"
Mono<Subscription>
Flux<Entitlement>
Flux<UsageRecord>
```

---

## Requirements

* Non-blocking entitlement validation
* Reactive quota checks
* Async usage metering
* High-concurrency support

---

# 20. Distributed System Considerations

Aggregates support:

* Multi-region deployments
* Distributed quota evaluation
* Event-driven synchronization
* Horizontal scalability
* Cached entitlement propagation

---

# 21. Security-Critical Rules

## Forbidden Behavior

```text id="u8x3wp"
Expired subscriptions
must not retain premium access
```

---

## Grace Period Handling

Grace periods must be:

* Explicit
* Auditable
* Configurable

---

# 22. Event Sourcing Compatibility

The aggregates are compatible with:

* Subscription replay
* Plan transition replay
* Entitlement reconstruction
* Usage reconstruction

---

# 23. Future Aggregate Extensions

Future aggregates may include:

* MarketplaceSubscriptionAggregate
* EnterpriseContractAggregate
* DynamicPricingAggregate
* RegionalPricingAggregate
* AIConsumptionAggregate
* LicenseSeatAggregate

---

# 24. Summary

The Subscription Management aggregates provide:

* Enterprise-grade subscription lifecycle management
* Multi-tenant commercial isolation
* Reactive entitlement orchestration
* Real-time quota governance
* Distributed usage metering
* SaaS monetization consistency
* Scalable commercial feature management

These aggregates form the transactional backbone of the commercial SaaS ecosystem.

```
```
