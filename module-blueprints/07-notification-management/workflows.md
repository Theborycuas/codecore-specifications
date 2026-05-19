# workflows.md

````md
# Notification Management
## Workflows
### CodeCore Module Blueprints
### Version 1.0

---

# 1. DISPATCH WORKFLOW (reactive)

```text
IntegrationEvent received
    → Resolve recipient (User Management port)
    → Load preferences
    → Validate template
    → Create Notification (PENDING)
    → Render content
    → Dispatch via provider port
    → Record DeliveryAttempt
    → Publish NotificationDelivered or NotificationFailed
```

---

# 2. RETRY WORKFLOW

```text
NotificationFailed (retriable)
    → Schedule retry (backoff)
    → Re-dispatch
    → Max attempts exceeded → DLQ + NotificationFailedIntegrationEvent
```

---

# 3. TEMPLATE ACTIVATION WORKFLOW

```text
TemplateVersion created
    → Validate schema
    → Activate version
    → TemplateVersionActivated event
```

---

# 4. RULES

* workflows orchestrate; aggregates enforce invariants
* long-running retry belongs in orchestration / worker, not blocking request thread
* IAM MFA/OTP **codes** are issued by IAM; Notification only delivers content provided by IAM events

````
