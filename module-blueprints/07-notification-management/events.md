# events.md

````md
# Notification Management
## Event Engineering
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

Official event model for Notification Management.

---

# 2. DOMAIN EVENTS (published)

| Event | Aggregate |
|-------|-----------|
| NotificationRequested | NotificationAggregate |
| NotificationDispatched | NotificationAggregate |
| NotificationDelivered | NotificationAggregate |
| NotificationFailed | NotificationAggregate |
| NotificationCancelled | NotificationAggregate |
| NotificationRetryScheduled | NotificationAggregate |
| TemplateCreated | NotificationTemplateAggregate |
| TemplateVersionActivated | NotificationTemplateAggregate |
| PreferenceUpdated | NotificationPreferenceAggregate |

---

# 3. INTEGRATION EVENTS (published)

| Event | Typical consumers |
|-------|-------------------|
| NotificationDeliveredIntegrationEvent | Audit, Observability |
| NotificationFailedIntegrationEvent | Audit, Observability, Workflow |
| NotificationBouncedIntegrationEvent | User Management, Audit |

---

# 4. INTEGRATION EVENTS (consumed)

| Event | Source module | Action |
|-------|---------------|--------|
| IdentityAuthenticatedIntegrationEvent | IAM | Optional security alert templates |
| PasswordResetRequestedIntegrationEvent | IAM | Send recovery notification |
| IdentityLockedIntegrationEvent | IAM | Security alert |
| MembershipCreatedIntegrationEvent | User Management | Welcome / onboarding |
| MembershipSuspendedIntegrationEvent | User Management | Account notice |
| TenantActivatedIntegrationEvent | Tenant Management | Onboarding |
| TenantSuspendedIntegrationEvent | Tenant Management | Suspension notice |
| TenantQuotaExceededIntegrationEvent | Tenant Management | Quota alert |
| InvoiceIssuedIntegrationEvent | Billing Management | Billing email (PLANNED contract) |
| PaymentCapturedIntegrationEvent | Payment Management | Receipt notification (PLANNED contract) |

---

# 5. EVENT RULES

* past tense naming
* immutable facts only
* mandatory `tenant_id`, `correlation_id`, `event_id`
* no passwords, tokens, or provider secrets in payloads

See `09-event-engineering-standards.md`.

````
