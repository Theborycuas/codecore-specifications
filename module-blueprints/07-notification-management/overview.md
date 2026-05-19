# overview.md

````md
# Notification Management
## Module Blueprint Overview
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

The Notification Management module is responsible for:

- outbound notification orchestration
- email notifications
- SMS notifications
- push notifications
- in-app notifications
- notification templates
- notification preferences
- delivery tracking and lifecycle
- notification retries and dead-letter handling
- notification provider abstraction
- notification observability

Notification Management acts as the **authoritative bounded context** for how CodeCore delivers messages to users and systems inside tenant boundaries.

This module defines:

- WHAT notification is requested
- HOW it is rendered and routed
- WHICH channel and provider deliver it
- WHETHER delivery succeeded, failed, or must retry

Notification Management does NOT authenticate users.

Authentication belongs exclusively to:

- Identity & Access Management (IAM)

Notification Management does NOT authorize platform access.

Authorization belongs to:

- Authorization Management

---

# 2. BOUNDED CONTEXT DEFINITION

The Notification Management bounded context governs:

```text
Tenant-safe message delivery,
notification lifecycle,
template rendering,
channel routing,
delivery reliability,
and notification preference enforcement.
```

Notification Management owns:

* notification requests and delivery state
* templates and localization of message content
* channel configuration (email, SMS, push, in-app)
* recipient targeting (operational references, not identity credentials)
* delivery attempts and retry policies
* provider orchestration ports (provider-agnostic)
* notification preferences and opt-out rules
* notification observability metadata

Notification Management does NOT own:

* user authentication
* permission evaluation
* operational user profiles (except consuming references)
* long-running business workflows
* immutable compliance audit storage
* external CRM/ERP integration orchestration

Those belong to:

* Identity & Access Management
* Authorization Management
* User Management
* Workflow Management
* Audit Management
* Integration Management

---

# 3. CORE RESPONSIBILITIES

## 3.1 Channel responsibilities

| Channel | Responsibility |
|---------|----------------|
| Email | Transactional and operational email delivery |
| SMS | Text alerts and verification delivery (content only — codes issued by IAM when auth-related) |
| Push | Mobile/web push notifications |
| In-app | Tenant-scoped in-app notification inbox |

## 3.2 Template responsibilities

* template definition and versioning
* variable binding and rendering
* locale and tenant branding hooks
* template activation and deprecation

## 3.3 Delivery responsibilities

* enqueue and dispatch
* idempotent delivery attempts
* retry with backoff
* dead-letter for non-recoverable failures
* delivery status tracking

## 3.4 Preference responsibilities

* per-user and per-tenant channel preferences
* category-based opt-in/opt-out
* quiet hours and throttling policies

---

# 4. OWNERSHIP BOUNDARIES

## Notification Management owns

* `Notification` lifecycle
* `NotificationTemplate` lifecycle
* `NotificationPreference` rules
* `DeliveryAttempt` traceability
* provider routing through **ports** (hexagonal)

## Notification Management does NOT own

* JWT, sessions, MFA → IAM
* `UserProfile` data → User Management
* compliance audit records → Audit Management (consumes events)
* third-party API connectors → Integration Management (adapters)

---

# 5. EXTERNAL DEPENDENCIES

Notification Management depends on:

* Identity & Access Management (IAM) — security context only
* User Management — recipient resolution
* Tenant Management — tenant policies and isolation
* Authorization Management — protected admin APIs
* Audit Management — compliance ingestion via events
* Observability infrastructure — traces and metrics
* Integration Management — optional external delivery adapters

---

# 6. MULTITENANCY STRATEGY

All notifications MUST be tenant-scoped.

* `tenant_id` is mandatory on every notification and template
* cross-tenant recipient routing is forbidden
* provider credentials are tenant-scoped or platform-scoped with explicit isolation
* reactive pipelines MUST preserve tenant context (Reactor Context)

---

# 7. EVENT-DRIVEN INTEGRATION

Notification Management is **event-driven**:

* consumes integration events from IAM, User, Tenant, Billing, etc.
* publishes `NotificationDispatched`, `NotificationFailed`, `NotificationDelivered` facts
* MUST NOT require synchronous coupling to domain modules

See `events.md` for the official event catalog.

---

# 8. REACTIVE RESPONSIBILITIES

* non-blocking dispatch pipelines
* backpressure-aware provider calls
* reactive retry orchestration
* no blocking I/O on event loop threads

See `06-reactive-architecture-rules.md`.

---

# 9. PROVIDER-AGNOSTIC ARCHITECTURE

Delivery MUST use hexagonal ports:

* `EmailProviderPort`
* `SmsProviderPort`
* `PushProviderPort`

Concrete vendors (SendGrid, Twilio, FCM, etc.) are **adapters**, not domain owners.

---

# 10. OBSERVABILITY

Notification Management MUST emit:

* delivery latency metrics
* failure rates by channel and provider
* retry counts
* correlation and tenant-aware structured logs

Operational dashboards are consumed by Observability Management; compliance facts go to Audit Management.

---

# 11. RELATED DOCUMENTS

* `aggregates.md`, `entities.md`, `events.md`, `workflows.md`
* `AUTHENTICATION-CANONICALIZATION.md`
* `MODULE-CATALOG.md`
* `DOCUMENTATION-REPAIR-NOTES.md`

````
