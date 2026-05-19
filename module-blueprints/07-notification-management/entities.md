# entities.md

````md
# Notification Management
## Entity Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. OFFICIAL ENTITIES

| Entity | Aggregate | Responsibility |
|--------|-----------|----------------|
| Notification | NotificationAggregate | Delivery unit root |
| NotificationContent | NotificationAggregate | Rendered or raw content snapshot |
| NotificationRecipient | NotificationAggregate | Target reference |
| NotificationTemplate | NotificationTemplateAggregate | Template root |
| TemplateVersion | NotificationTemplateAggregate | Versioned content |
| TemplateVariable | NotificationTemplateAggregate | Variable definitions |
| NotificationPreference | NotificationPreferenceAggregate | Preference root |
| ChannelPreference | NotificationPreferenceAggregate | Per-channel settings |
| DeliveryAttempt | DeliveryAttemptAggregate | Provider attempt record |
| ProviderRoute | NotificationAggregate | Selected provider route metadata |

---

# 2. ENTITY RULES

Entities MUST:

* remain tenant-scoped
* avoid cross-context foreign keys to internal tables of other modules (use ids)
* remain reactive-persistence compatible (R2DBC)

Entities MUST NOT:

* embed IAM credentials
* embed authorization permission matrices

````
