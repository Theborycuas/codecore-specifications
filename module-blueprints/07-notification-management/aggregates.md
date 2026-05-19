# aggregates.md

````md
# Notification Management
## Aggregate Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

Defines aggregate consistency boundaries for the Notification Management bounded context.

---

# 2. OFFICIAL NOTIFICATION AGGREGATES

| Aggregate | Responsibility |
|-----------|----------------|
| NotificationAggregate | Notification request and delivery lifecycle |
| NotificationTemplateAggregate | Template definition, versioning, activation |
| NotificationPreferenceAggregate | Recipient/channel preference rules |
| DeliveryAttemptAggregate | Attempt traceability (optional child boundary per notification) |

---

# 3. NOTIFICATION AGGREGATE

## Aggregate Root

```text
Notification
```

## Owns

* channel selection
* delivery state (PENDING, DISPATCHED, DELIVERED, FAILED, CANCELLED)
* recipient reference (user/actor id — not credentials)
* template reference
* retry eligibility
* idempotency key

## Invariants

* MUST belong to exactly one tenant
* MUST NOT dispatch without valid template or explicit raw content policy
* MUST respect preference aggregate rules before dispatch
* FAILED notifications MAY retry only within policy limits

## Forbidden

* storing passwords or MFA secrets in notification payload
* calling providers directly from entity methods (use domain services / application layer + ports)

---

# 4. NOTIFICATION TEMPLATE AGGREGATE

## Aggregate Root

```text
NotificationTemplate
```

## Owns

* template key, channel, locale variants
* body/subject structure
* variable schema
* active version pointer

## Invariants

* template key unique per tenant and channel
* only one active version per template key at a time

---

# 5. NOTIFICATION PREFERENCE AGGREGATE

## Aggregate Root

```text
NotificationPreference
```

## Owns

* per-recipient channel enablement
* category opt-in/opt-out
* quiet hours

## Invariants

* preferences scoped to tenant + recipient
* legal/compliance mandatory categories MAY NOT be disabled where regulation requires

---

# 6. DELIVERY ATTEMPT AGGREGATE

## Aggregate Root

```text
DeliveryAttempt
```

## Owns

* provider reference
* attempt number
* status and provider response metadata (non-sensitive)
* timestamps

## Invariants

* append-only attempt history
* attempts linked to single parent notification

---

# 7. AGGREGATE INTERACTION RULES

* `NotificationAggregate` references template by id; does not mutate template inline
* preference checks MAY be delegated to `NotificationPreferenceAggregate` via domain service
* cross-aggregate updates in one transaction are forbidden unless explicitly orchestrated

````
