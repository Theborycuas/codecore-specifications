# repositories.md

````md
# Notification Management
## Repository Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. OFFICIAL REPOSITORIES

| Repository | Aggregate |
|------------|-----------|
| NotificationRepository | NotificationAggregate |
| NotificationTemplateRepository | NotificationTemplateAggregate |
| NotificationPreferenceRepository | NotificationPreferenceAggregate |
| DeliveryAttemptRepository | DeliveryAttemptAggregate |

---

# 2. REPOSITORY RULES

Repositories MUST:

* enforce tenant_id on every query
* load/save only through aggregate roots
* remain fully reactive (Mono/Flux, R2DBC)
* support optimistic locking where applicable

Repositories MUST NOT:

* invoke external providers
* publish events (application layer publishes after commit)
* contain business orchestration

See `04-repository-rules.md`.

````
