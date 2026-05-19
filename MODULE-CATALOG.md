# CodeCore Module Catalog

## Engineering Specifications — Official Module Index

### Version 1.1

This catalog is the **authoritative index** of CodeCore module blueprints. It resolves AUD-020 and supports AI-assisted development per `17-ai-development-rules.md`.

**Related:** `AUTHENTICATION-CANONICALIZATION.md`, `ENTERPRISE-ARCHITECTURE-AUDIT.md`

---

## Status legend

| Status | Meaning |
|--------|---------|
| **ACTIVE** | Authoritative bounded context; use for implementation |
| **PLANNED** | Referenced by platform; blueprint not yet in repository |
| **DEPRECATED** | Retained for history only; do not implement |

---

## Platform modules (numbered blueprints)

### 01 — Identity & Access Management (IAM)

| Field | Value |
|-------|-------|
| **Bounded context** | Authentication, identity, sessions, tokens, MFA, identity OAuth, API keys, device trust |
| **Ownership** | Credentials, sessions, JWT issuance (hybrid claims), login flows, authentication events |
| **Status** | **ACTIVE** (canonical authentication) |
| **Path** | `module-blueprints/01-identity-access-management/` |
| **Depends on** | Tenant Management, User Management (references), Authorization (downstream), Audit, Observability |
| **Publishes (examples)** | `IdentityAuthenticatedIntegrationEvent`, `PasswordResetRequestedIntegrationEvent`, `SessionRevokedIntegrationEvent`, `IdentityLockedIntegrationEvent` |
| **Consumes (examples)** | `TenantActivatedIntegrationEvent`, `TenantSuspendedIntegrationEvent` |
| **Description** | Foundational security gateway; sole authoritative authentication bounded context. |

---

### 02 — Tenant Management

| Field | Value |
|-------|-------|
| **Bounded context** | Tenant lifecycle, isolation, provisioning, operational tenant configuration |
| **Ownership** | Tenant state, onboarding, tenant quotas (operational), tenant feature toggles (operational) |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/02-tenant-management/` |
| **Depends on** | IAM, User Management, Notification Management, Audit |
| **Publishes (examples)** | `TenantActivatedIntegrationEvent`, `TenantSuspendedIntegrationEvent`, `TenantQuotaExceededIntegrationEvent` |
| **Consumes (examples)** | — |
| **Description** | Authoritative multitenancy bounded context for the platform. |

---

### 03 — User Management

| Field | Value |
|-------|-------|
| **Bounded context** | Operational human actors, profiles, memberships, ownership |
| **Ownership** | User profiles, actors, memberships, organization units, operational ownership |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/03-user-management/` |
| **Depends on** | Tenant Management, IAM (reference only), Authorization, Notification |
| **Publishes (examples)** | `MembershipCreatedIntegrationEvent`, `MembershipSuspendedIntegrationEvent`, `OwnershipTransferredIntegrationEvent` |
| **Consumes (examples)** | `TenantOnboardingCompletedIntegrationEvent` |
| **Description** | Authoritative operational user context; does not authenticate. |

---

### 04 — Authorization Management

| Field | Value |
|-------|-------|
| **Bounded context** | Roles, permissions, policies, access decisions |
| **Ownership** | RBAC, policy engine, runtime authorization (authoritative over JWT hints) |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/04-authorization-management/` |
| **Depends on** | IAM, User Management, Tenant Management |
| **Publishes (examples)** | `RoleCreated`, `PermissionAssigned`, `AccessDenied` |
| **Consumes (examples)** | Membership and entitlement change events |
| **Description** | Central security enforcement; dynamic policy engine. |

---

### 05 — Authentication Management

| Field | Value |
|-------|-------|
| **Bounded context** | *(deprecated — merged into IAM)* |
| **Ownership** | **None** — see IAM (01) |
| **Status** | **DEPRECATED** |
| **Path** | `module-blueprints/05-authentication-management/` |
| **Replacement** | `01-identity-access-management` |
| **Description** | Historical duplicate of IAM. See `DEPRECATED.md` and `AUTHENTICATION-CANONICALIZATION.md`. |

---

### 06 — Audit Management

| Field | Value |
|-------|-------|
| **Bounded context** | Immutable compliance and security audit records |
| **Ownership** | Audit storage, retention, forensic queries |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/06-audit-management/` |
| **Depends on** | IAM, Tenant, Observability (derivatives only) |
| **Publishes (examples)** | Audit record appended events (internal) |
| **Consumes (examples)** | Security and domain integration events platform-wide |
| **Description** | Compliance-grade audit trail; not a generic logging platform. |

---

### 07 — Notification Management

| Field | Value |
|-------|-------|
| **Bounded context** | Email, SMS, push, in-app notifications, templates, preferences, delivery |
| **Ownership** | Notification lifecycle, templates, preferences, delivery attempts, provider ports |
| **Status** | **ACTIVE** (repaired post AUD-002) |
| **Path** | `module-blueprints/07-notification-management/` |
| **Depends on** | IAM, User Management, Tenant, Authorization, Audit, Integration (adapters) |
| **Publishes (examples)** | `NotificationDeliveredIntegrationEvent`, `NotificationFailedIntegrationEvent` |
| **Consumes (examples)** | IAM, User, Tenant integration events (see `events.md`) |
| **Description** | Enterprise notification orchestration; provider-agnostic, event-driven. |

---

### 08 — File Management

| Field | Value |
|-------|-------|
| **Bounded context** | File storage, upload, lifecycle, CDN, processing pipelines |
| **Ownership** | File metadata, storage abstraction, tenant file isolation |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/08-file-management/` |
| **Depends on** | IAM, Authorization, Tenant, Subscription (quotas) |
| **Publishes (examples)** | File uploaded/archived integration events |
| **Consumes (examples)** | Quota / entitlement events |
| **Description** | Multi-tenant file platform with storage ports. |

---

### 09 — Subscription Management

| Field | Value |
|-------|-------|
| **Bounded context** | Plans, entitlements, quotas, usage metering, trials |
| **Ownership** | Commercial entitlements and subscription lifecycle |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/09-subscription-management/` |
| **Depends on** | Tenant, Billing, Configuration (flags — coordinate via policy chain) |
| **Publishes (examples)** | Entitlement and subscription lifecycle integration events |
| **Consumes (examples)** | Payment/billing events |
| **Description** | Commercial subscription and entitlement authority. |

---

### 10 — Billing Management

| Field | Value |
|-------|-------|
| **Bounded context** | Invoices, charges, tax, revenue, credit notes |
| **Ownership** | Financial documents and ledger-oriented reconciliation |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/10-billing-management/` |
| **Depends on** | Subscription, Payment, Tenant, IAM |
| **Publishes (examples)** | Invoice issued, billing adjustment events |
| **Consumes (examples)** | Usage and subscription events |
| **Description** | Billing calculations and invoice lifecycle; not payment execution. |

---

### 11 — Payment Management

| Field | Value |
|-------|-------|
| **Bounded context** | Payment execution, providers, webhooks, refunds, settlement reconciliation |
| **Ownership** | Payment transactions, provider orchestration, PCI boundary |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/11-payment-management/` |
| **Depends on** | Billing, IAM, Integration |
| **Publishes (examples)** | `PaymentCapturedIntegrationEvent`, payment failed events |
| **Consumes (examples)** | Invoice / billing events |
| **Description** | Payment execution and provider settlement reconciliation. |

---

### 12 — Configuration Management

| Field | Value |
|-------|-------|
| **Bounded context** | Runtime configuration, feature flags, hot reload, branding |
| **Ownership** | Platform and tenant runtime config (not `application.yml`) |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/12-configuration-management/` |
| **Depends on** | Tenant, Subscription (precedence rules) |
| **Publishes (examples)** | Configuration changed, feature flag toggled events |
| **Consumes (examples)** | Tenant lifecycle events |
| **Description** | Enterprise runtime configuration orchestration. |

---

### 13 — Observability Management

| Field | Value |
|-------|-------|
| **Bounded context** | Metrics, traces, logs pipelines, dashboards, alerting |
| **Ownership** | Telemetry ingestion and operational visibility |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/13-observability-management/` |
| **Depends on** | All modules (telemetry producers) |
| **Publishes (examples)** | Alert and SLO breach events |
| **Consumes (examples)** | Platform and business events for dashboards |
| **Description** | Operational observability; not compliance audit storage. |

---

### 14 — Integration Management

| Field | Value |
|-------|-------|
| **Bounded context** | External integrations, webhooks, integration OAuth, idempotency, DLQ |
| **Ownership** | Integration orchestration, provider health, integration secrets (connectors) |
| **Status** | **ACTIVE** |
| **Path** | `module-blueprints/14-integration-management/` |
| **Depends on** | IAM (service auth), Tenant, Observability |
| **Publishes (examples)** | `IntegrationEventPublished`, sync completed/failed |
| **Consumes (examples)** | Versioned `*IntegrationEvent` from domain modules |
| **Description** | Provider-agnostic integration hub; not user login OAuth (IAM). |

---

## Planned modules (referenced, blueprint pending)

| # | Module | Status | Referenced by | Intended ownership |
|---|--------|--------|---------------|-------------------|
| — | Workflow Management | **PLANNED** | User, Audit | Long-running cross-module orchestration |
| — | Scheduling Management | **PLANNED** | User, Tenant | Appointments and schedules |
| — | Forms Management | **PLANNED** | User events | Dynamic forms lifecycle |
| — | Clinical / vertical modules | **PLANNED** | User, Authorization examples | Domain-specific verticals on core |

When implementing before blueprints exist, mark integrations as `Consumed By: PLANNED` in event tables.

---

## Cross-cutting specifications (mandatory)

| Document | Topic |
|----------|-------|
| `01-aggregate-design-rules.md` | Aggregates |
| `05-service-taxonomy.md` | Services |
| `06-reactive-architecture-rules.md` | Reactive |
| `07-transaction-boundaries.md` | Transactions |
| `09-event-engineering-standards.md` | Events |
| `10-multitenancy-enforcement-rules.md` | Multi-tenancy |
| `11-security-context-propagation.md` | JWT hybrid + security context |
| `12-auditing-standards.md` | Auditing |
| `13-observability-standards.md` | Observability |
| `17-ai-development-rules.md` | AI development |

---

## Recent architectural corrections (summary)

| AUD | Resolution |
|-----|------------|
| AUD-001 | IAM canonical; module 05 DEPRECATED |
| AUD-002 | Module 07 repaired — Notification Management |
| AUD-003 | Hybrid JWT in `11-security-context-propagation.md` |
| AUD-020 | This catalog |

---

## Reading order for new contributors

1. `MODULE-CATALOG.md` (this file)  
2. `AUTHENTICATION-CANONICALIZATION.md`  
3. `10-multitenancy-enforcement-rules.md` + `11-security-context-propagation.md`  
4. `09-event-engineering-standards.md`  
5. Module blueprints for your bounded context  
