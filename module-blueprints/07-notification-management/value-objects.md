# value-objects.md

````md
# Notification Management
## Value Object Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. OFFICIAL VALUE OBJECTS

| Value Object | Usage |
|--------------|-------|
| NotificationId | Strongly typed notification identifier |
| TemplateKey | Tenant-scoped template key |
| ChannelType | EMAIL, SMS, PUSH, IN_APP |
| DeliveryStatus | PENDING, DISPATCHED, DELIVERED, FAILED, CANCELLED |
| RecipientReference | Opaque user/actor id + type |
| LocaleCode | Template locale |
| IdempotencyKey | Duplicate dispatch prevention |
| RetryPolicy | Max attempts, backoff strategy |
| ProviderReference | External provider message id (non-secret) |
| CorrelationMetadata | trace/correlation propagation |

---

# 2. RULES

Value objects MUST be immutable and tenant-safe where applicable.

Forbidden in value objects:

* provider API secrets
* JWT or refresh tokens

````
