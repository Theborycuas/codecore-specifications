# api-contracts.md

````md id="tenantapicontracts"
# Tenant Management
## API Contract Standards
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official API contract standards for the Tenant Management bounded context.

Its objectives are:

- standardize tenant lifecycle APIs
- preserve tenant-safe communication
- enforce multitenancy boundaries
- support scalable SaaS operations
- preserve reactive-safe execution
- guarantee contract consistency
- preserve observability and auditability
- guide AI-assisted implementation

---

# 2. API PHILOSOPHY

Tenant Management APIs exist to:
- expose tenant operational capabilities
- coordinate tenant lifecycle execution
- preserve operational isolation
- support scalable SaaS orchestration
- propagate tenant-safe execution context

Tenant APIs MUST:
- remain reactive
- remain tenant-aware
- remain observable
- remain auditable
- preserve operational consistency

---

# 3. OFFICIAL API STRATEGY

Tenant Management officially adopts:

```text id="tenantapistrategy"
REST + JSON + Reactive APIs
````

---

# 3.1 Base Path

Recommended base path:

```text id="tenantbasepath"
/api/v1/tenants
```

---

# 3.2 Reactive Contract Principle

All Tenant APIs MUST:

* remain non-blocking
* support Reactor-based execution
* preserve async-safe communication

---

# 4. OFFICIAL TENANT MANAGEMENT ENDPOINTS

---

# 4.1 Tenant Lifecycle Endpoints

| Endpoint             | Method | Purpose          |
| -------------------- | ------ | ---------------- |
| /                    | POST   | Provision tenant |
| /{tenantId}          | GET    | Retrieve tenant  |
| /{tenantId}/activate | POST   | Activate tenant  |
| /{tenantId}/suspend  | POST   | Suspend tenant   |
| /{tenantId}/restore  | POST   | Restore tenant   |
| /{tenantId}/archive  | POST   | Archive tenant   |

---

# 4.2 Tenant Configuration Endpoints

| Endpoint                  | Method | Purpose                |
| ------------------------- | ------ | ---------------------- |
| /{tenantId}/configuration | GET    | Retrieve configuration |
| /{tenantId}/configuration | PUT    | Update configuration   |
| /{tenantId}/branding      | PUT    | Update branding        |
| /{tenantId}/localization  | PUT    | Update localization    |

---

# 4.3 Tenant Feature Endpoints

| Endpoint                                  | Method | Purpose           |
| ----------------------------------------- | ------ | ----------------- |
| /{tenantId}/features                      | GET    | Retrieve features |
| /{tenantId}/features/{featureKey}/enable  | POST   | Enable feature    |
| /{tenantId}/features/{featureKey}/disable | POST   | Disable feature   |
| /{tenantId}/modules                       | GET    | Retrieve modules  |

---

# 4.4 Tenant Quota Endpoints

| Endpoint           | Method | Purpose                 |
| ------------------ | ------ | ----------------------- |
| /{tenantId}/quotas | GET    | Retrieve quotas         |
| /{tenantId}/quotas | PUT    | Update quotas           |
| /{tenantId}/usage  | GET    | Retrieve resource usage |

---

# 4.5 Tenant Onboarding Endpoints

| Endpoint                        | Method | Purpose                   |
| ------------------------------- | ------ | ------------------------- |
| /{tenantId}/onboarding          | GET    | Retrieve onboarding state |
| /{tenantId}/onboarding/start    | POST   | Start onboarding          |
| /{tenantId}/onboarding/complete | POST   | Complete onboarding       |

---

# 4.6 Tenant Validation Endpoints

| Endpoint             | Method | Purpose                          |
| -------------------- | ------ | -------------------------------- |
| /{tenantId}/validate | POST   | Validate operational eligibility |
| /{tenantId}/status   | GET    | Retrieve operational state       |

---

# 5. REQUEST DTO CONTRACTS

---

# 5.1 Tenant Provisioning Request

Recommended structure:

```json id="tenantprovisioningrequest"
{
  "name": "Smile Dental",
  "slug": "smile-dental",
  "planType": "PROFESSIONAL",
  "locale": "es-EC",
  "timezone": "America/Guayaquil",
  "currency": "USD",
  "ownerEmail": "admin@smiledental.com"
}
```

---

# 5.2 Tenant Activation Request

```json id="tenantactivationrequest"
{
  "reason": "Operational onboarding completed"
}
```

---

# 5.3 Tenant Suspension Request

```json id="tenantsuspensionrequest"
{
  "reason": "Subscription expired"
}
```

---

# 5.4 Tenant Configuration Update Request

```json id="tenantconfigurationrequest"
{
  "timezone": "America/Guayaquil",
  "language": "es",
  "currency": "USD",
  "dateFormat": "dd/MM/yyyy"
}
```

---

# 5.5 Tenant Branding Update Request

```json id="tenantbrandingrequest"
{
  "brandName": "Smile Dental",
  "primaryColor": "#0F172A",
  "secondaryColor": "#3B82F6",
  "logoUrl": "https://cdn.codecore.app/logo.png"
}
```

---

# 5.6 Tenant Quota Update Request

```json id="tenantquotarequest"
{
  "maxUsers": 50,
  "maxStorageMb": 10240,
  "maxApiRequests": 500000
}
```

---

# 5.7 Tenant Feature Enablement Request

```json id="tenantfeatureenablementrequest"
{
  "featureKey": "ONLINE_BOOKING"
}
```

---

# 6. RESPONSE DTO CONTRACTS

---

# 6.1 Tenant Response Contract

Recommended structure:

```json id="tenantresponse"
{
  "tenantId": "tenant-001",
  "tenantCode": "DENTAL-001",
  "name": "Smile Dental",
  "slug": "smile-dental",
  "status": "ACTIVE",
  "planType": "PROFESSIONAL",
  "createdAt": "2026-05-16T10:00:00Z"
}
```

---

# 6.2 Tenant Configuration Response

```json id="tenantconfigurationresponse"
{
  "tenantId": "tenant-001",
  "timezone": "America/Guayaquil",
  "language": "es",
  "currency": "USD",
  "dateFormat": "dd/MM/yyyy"
}
```

---

# 6.3 Tenant Quota Response

```json id="tenantquotaresponse"
{
  "tenantId": "tenant-001",
  "maxUsers": 50,
  "usedUsers": 12,
  "maxStorageMb": 10240,
  "usedStorageMb": 2300
}
```

---

# 6.4 Tenant Feature Response

```json id="tenantfeatureresponse"
{
  "tenantId": "tenant-001",
  "enabledFeatures": [
    "ONLINE_BOOKING",
    "MULTI_BRANCH"
  ]
}
```

---

# 6.5 Tenant Onboarding Response

```json id="tenantonboardingresponse"
{
  "tenantId": "tenant-001",
  "status": "IN_PROGRESS",
  "currentStep": "FEATURE_SETUP",
  "startedAt": "2026-05-16T10:00:00Z"
}
```

---

# 6.6 Error Response Contract

Recommended structure:

```json id="tenanterrorresponse"
{
  "timestamp": "2026-05-16T10:00:00Z",
  "correlationId": "corr-001",
  "traceId": "trace-001",
  "errorCode": "TENANT_SUSPENDED",
  "message": "Tenant operational access denied",
  "path": "/api/v1/tenants/tenant-001/validate"
}
```

---

# 7. API VALIDATION RULES

---

# 7.1 Request Validation Principle

All Tenant APIs MUST validate:

* tenant ownership
* lifecycle eligibility
* quota integrity
* feature availability
* configuration consistency

---

# 7.2 Validation Failure Rules

Invalid requests SHOULD return:

```text id="tenantvalidationstatus"
400 Bad Request
```

---

# 7.3 Operational Validation Rules

Operationally invalid tenants MUST:

* reject execution safely

---

# 8. MULTITENANCY RULES

---

# 8.1 Tenant Isolation Principle

Tenant APIs MUST preserve:

* strict tenant isolation

---

# 8.2 Cross Tenant Access Forbidden

Tenant APIs MUST NEVER:

* expose another tenant’s operational state
* mutate another tenant’s configuration unintentionally

---

# 8.3 Tenant Context Propagation

Tenant metadata MUST propagate through:

* JWT claims
* Reactor Context
* observability metadata
* distributed workflows

---

# 9. SECURITY CONTRACT RULES

---

# 9.1 Secure API Principle

Tenant APIs MUST:

* require HTTPS
* validate authorization
* validate tenant ownership

---

# 9.2 Sensitive Exposure Restrictions

Tenant APIs MUST NEVER expose:

* internal aggregate state
* secrets
* infrastructure internals

---

# 9.3 Operational Restriction Principle

Suspended tenants MUST:

* reject protected operational APIs

---

# 10. STATUS CODE RULES

---

# 10.1 Recommended Status Codes

| Status Code | Purpose                   |
| ----------- | ------------------------- |
| 200         | Successful operation      |
| 201         | Tenant provisioned        |
| 204         | Successful empty response |
| 400         | Validation failure        |
| 401         | Authentication failure    |
| 403         | Authorization failure     |
| 404         | Tenant not found          |
| 409         | Concurrency conflict      |
| 429         | Quota exceeded            |

---

# 10.2 Operational Restriction Status

Restricted tenant operations SHOULD return:

```text id="tenantrestrictedstatus"
403 Forbidden
```

---

# 11. REACTIVE CONTRACT RULES

---

# 11.1 Official Reactive Standard

Tenant APIs MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 11.2 Blocking Operations Forbidden

Forbidden:

* JDBC
* Thread.sleep
* .block()
* imperative waiting

inside API execution chains.

---

# 11.3 Reactive Context Propagation

Reactive APIs MUST preserve:

* tenant context
* correlation IDs
* trace IDs
* operational metadata

---

# 12. OBSERVABILITY RULES

---

# 12.1 Traceability Principle

Tenant APIs MUST expose:

* correlation IDs
* trace IDs
* tenant-aware diagnostics

---

# 12.2 Mandatory Metadata

Recommended metadata:

```text id="tenantapimetadata"
tenant_id
correlation_id
trace_id
tenant_status
tenant_plan
```

---

# 12.3 Provisioning Visibility Principle

Provisioning APIs SHOULD remain:

* observable
* measurable
* diagnosable

---

# 13. AUDITING RULES

---

# 13.1 Mandatory Auditability

Critical Tenant APIs MUST generate:

* audit records
* lifecycle traces
* operational history

---

# 13.2 Mandatory Audited Endpoints

The following MUST remain auditable:

* Tenant Provisioning
* Tenant Activation
* Tenant Suspension
* Tenant Restoration
* Tenant Archival
* Configuration Updates
* Feature Enablement
* Feature Disablement
* Quota Changes

---

# 14. IDEMPOTENCY RULES

---

# 14.1 Idempotency Principle

Sensitive Tenant operations SHOULD support:

* idempotent execution

---

# 14.2 Retry Safety Principle

Retries MUST preserve:

* onboarding consistency
* quota consistency
* lifecycle consistency

---

# 15. VERSIONING RULES

---

# 15.1 API Versioning Principle

Public Tenant APIs SHOULD support:

* backward compatibility
* explicit versioning

---

# 15.2 Recommended Versioning Strategy

Recommended format:

```text id="tenantversioningstrategy"
/api/v1/tenants
```

---

# 15.3 Breaking Changes Rule

Breaking changes MUST:

* increment major version
* preserve migration strategy

---

# 16. FAILURE HANDLING RULES

---

# 16.1 Failure Isolation Principle

API failures SHOULD remain:

* isolated
* observable
* recoverable

---

# 16.2 Provisioning Failure Principle

Provisioning failures MUST:

* avoid inconsistent tenant states

---

# 16.3 Quota Failure Principle

Quota failures MUST:

* reject operations safely

---

# 17. FORBIDDEN API ANTI-PATTERNS

---

# Forbidden

* Cross-tenant API leakage
* Blocking reactive APIs
* Entity exposure
* Shared mutable tenant state
* Tenant-blind operations
* Hidden operational side effects
* Oversized payloads
* Infrastructure leakage
* Non-traceable failures
* Imperative reactive leakage

---

# 18. AI IMPLEMENTATION RULES

All AI-generated Tenant APIs MUST:

* remain fully reactive
* preserve tenant isolation
* preserve onboarding consistency
* preserve quota consistency
* avoid entity exposure
* avoid blocking execution
* preserve observability
* preserve auditability
* preserve reactive context propagation
* support scalable SaaS execution

---

# 19. CODECORE TENANT API PHILOSOPHY

```text id="tenantapiphilosophy"
Tenant Management APIs exist to expose
reactive, scalable and tenant-safe
operational lifecycle capabilities
through consistency-preserving SaaS contracts,
immutable ownership propagation
and observable distributed execution boundaries.
```

```
```
