# api-contracts.md

````md id="p3z7dw"
# Identity & Access Management (IAM)
## API Contract Standards
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official API contract standards for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- standardize authentication APIs
- preserve contract consistency
- protect security boundaries
- enforce tenant-aware authentication
- support reactive-safe communication
- preserve observability and auditability
- guide frontend integration
- guide AI-assisted implementation

---

# 2. API PHILOSOPHY

IAM APIs exist to:
- expose authentication capabilities
- coordinate secure identity access
- propagate authentication context safely
- preserve stateless authentication

IAM APIs MUST:
- remain reactive
- remain tenant-aware
- remain security-safe
- remain observable
- remain contract-stable

---

# 3. OFFICIAL API STRATEGY

IAM officially adopts:

```text id="iamapistrategy"
REST + JSON + Reactive APIs
````

---

# 3.1 Base Path

Recommended base path:

```text id="basepath"
/api/v1/iam
```

---

# 3.2 Reactive Contract Principle

All IAM APIs MUST:

* remain non-blocking
* support Reactor-based execution
* preserve async-safe processing

---

# 4. OFFICIAL IAM ENDPOINTS

---

# 4.1 Authentication Endpoints

| Endpoint       | Method | Purpose                  |
| -------------- | ------ | ------------------------ |
| /auth/login    | POST   | Authenticate identity    |
| /auth/refresh  | POST   | Refresh access token     |
| /auth/logout   | POST   | Revoke active session    |
| /auth/validate | POST   | Validate token integrity |

---

# 4.2 Password Management Endpoints

| Endpoint                | Method | Purpose                 |
| ----------------------- | ------ | ----------------------- |
| /password/change        | POST   | Change credentials      |
| /password/reset/request | POST   | Request password reset  |
| /password/reset/confirm | POST   | Complete password reset |

---

# 4.3 Session Endpoints

| Endpoint              | Method | Purpose              |
| --------------------- | ------ | -------------------- |
| /sessions             | GET    | List active sessions |
| /sessions/{sessionId} | DELETE | Revoke session       |
| /sessions/revoke-all  | POST   | Revoke all sessions  |

---

# 4.4 Security Endpoints

| Endpoint           | Method | Purpose                          |
| ------------------ | ------ | -------------------------------- |
| /security/lockouts | GET    | Retrieve lockout status          |
| /security/unlock   | POST   | Unlock account                   |
| /security/activity | GET    | Retrieve authentication activity |

---

# 5. REQUEST DTO CONTRACTS

---

# 5.1 Login Request Contract

Recommended structure:

```json id="loginrequest"
{
  "tenantId": "tenant-001",
  "usernameOrEmail": "user@example.com",
  "password": "********",
  "deviceMetadata": {
    "deviceId": "device-123",
    "platform": "web"
  }
}
```

---

# 5.2 Refresh Request Contract

```json id="refreshrequest"
{
  "refreshToken": "refresh-token"
}
```

---

# 5.3 Password Change Request Contract

```json id="passwordchangerequest"
{
  "currentPassword": "********",
  "newPassword": "********"
}
```

---

# 5.4 Password Reset Request Contract

```json id="passwordresetrequestapi"
{
  "tenantId": "tenant-001",
  "email": "user@example.com"
}
```

---

# 5.5 Password Reset Confirmation Contract

```json id="passwordresetconfirm"
{
  "resetToken": "reset-token",
  "newPassword": "********"
}
```

---

# 6. RESPONSE DTO CONTRACTS

---

# 6.1 Authentication Response Contract

Recommended structure:

```json id="authenticationresponse"
{
  "accessToken": "jwt-token",
  "refreshToken": "refresh-token",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "tenantId": "tenant-001",
  "identityId": "identity-001",
  "sessionId": "session-001"
}
```

---

# 6.2 Session Response Contract

```json id="sessionresponse"
{
  "sessionId": "session-001",
  "status": "ACTIVE",
  "deviceMetadata": {
    "platform": "web"
  },
  "lastActivityAt": "2026-05-16T10:00:00Z",
  "expiresAt": "2026-05-17T10:00:00Z"
}
```

---

# 6.3 Error Response Contract

Recommended structure:

```json id="errorresponse"
{
  "timestamp": "2026-05-16T10:00:00Z",
  "correlationId": "corr-001",
  "traceId": "trace-001",
  "errorCode": "INVALID_CREDENTIALS",
  "message": "Authentication failed",
  "path": "/api/v1/iam/auth/login"
}
```

---

# 7. AUTHENTICATION CONTRACT RULES

---

# 7.1 Stateless Authentication Principle

IAM APIs MUST support:

* stateless JWT authentication
* refresh token rotation
* distributed scalability

---

# 7.2 JWT Propagation Rules

JWT tokens SHOULD propagate through:

```text id="jwtheader"
Authorization: Bearer <token>
```

---

# 7.3 Token Integrity Rules

JWT contracts MUST:

* support expiration
* support revocation
* support tenant-aware claims

---

# 8. SECURITY CONTRACT RULES

---

# 8.1 Sensitive Data Restrictions

IAM APIs MUST NEVER expose:

* raw passwords
* password hashes
* internal secrets
* raw refresh token persistence identifiers

---

# 8.2 Credential Protection Principle

Sensitive credentials MUST:

* remain transient
* remain non-loggable
* remain serialization-safe

---

# 8.3 Secure Failure Principle

Authentication failures MUST:

* fail securely
* avoid credential leakage
* avoid identity enumeration

---

# 9. MULTITENANCY RULES

---

# 9.1 Tenant-Aware Authentication

IAM APIs MUST validate:

* tenant ownership
* tenant existence
* tenant status

---

# 9.2 Cross Tenant Access Forbidden

IAM APIs MUST NEVER:

* authenticate identities across tenants unintentionally

---

# 9.3 Tenant Metadata Propagation

Tenant metadata MUST propagate through:

* JWT claims
* security context
* observability metadata

---

# 10. SESSION CONTRACT RULES

---

# 10.1 Session Visibility Principle

Session APIs SHOULD expose:

* minimal session information
* device traceability
* activity timestamps

---

# 10.2 Session Revocation Rules

Revoked sessions MUST:

* reject refresh operations
* reject future renewal

---

# 10.3 Concurrent Session Rules

Concurrent sessions SHOULD support:

* configurable policies
* session visibility
* distributed revocation

---

# 11. VALIDATION RULES

---

# 11.1 Request Validation Principle

All incoming contracts MUST validate:

* required fields
* format integrity
* tenant integrity
* security constraints

---

# 11.2 Validation Failure Rules

Invalid requests SHOULD return:

```text id="validationstatus"
400 Bad Request
```

---

# 11.3 Security Validation Rules

Authentication validation MUST:

* remain timing-safe
* remain observable
* remain auditable

---

# 12. STATUS CODE RULES

---

# 12.1 Recommended Status Codes

| Status Code | Purpose                          |
| ----------- | -------------------------------- |
| 200         | Successful operation             |
| 201         | Resource created                 |
| 204         | Successful empty response        |
| 400         | Validation failure               |
| 401         | Authentication failure           |
| 403         | Authorization failure            |
| 404         | Resource not found               |
| 409         | Concurrency conflict             |
| 429         | Too many authentication attempts |

---

# 12.2 Security Failure Rules

Authentication failures SHOULD return:

```text id="authenticationfailure"
401 Unauthorized
```

without exposing internal security details.

---

# 13. REACTIVE CONTRACT RULES

---

# 13.1 Official Reactive Standard

IAM APIs MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 13.2 Blocking API Operations Forbidden

Forbidden:

* JDBC
* Thread.sleep
* .block()
* imperative waiting

inside API execution chains.

---

# 13.3 Reactive Context Propagation

Reactive API flows MUST preserve:

* tenant context
* security context
* correlation IDs
* trace IDs

---

# 14. OBSERVABILITY RULES

---

# 14.1 Traceability Principle

IAM APIs MUST expose:

* correlation IDs
* trace IDs
* tenant-aware diagnostics

---

# 14.2 Mandatory Metadata

Recommended metadata:

```text id="observabilitymetadata"
correlation_id
trace_id
tenant_id
session_id
identity_id
```

---

# 14.3 Security Visibility Principle

Authentication anomalies SHOULD remain:

* observable
* measurable
* traceable

---

# 15. AUDITING RULES

---

# 15.1 Mandatory Auditability

Critical IAM API operations MUST remain:

* auditable
* historically traceable

---

# 15.2 Mandatory Audited Endpoints

The following MUST generate audit records:

* Login
* Logout
* Password Change
* Password Reset
* Session Revocation
* Unlock Operations

---

# 16. API VERSIONING RULES

---

# 16.1 Versioning Principle

Public IAM APIs SHOULD support:

* backward compatibility
* explicit versioning

---

# 16.2 Recommended Strategy

Recommended format:

```text id="versioningstrategy"
/api/v1/iam
```

---

# 16.3 Breaking Change Rules

Breaking API changes MUST:

* increment major version
* preserve migration strategy

---

# 17. IDEMPOTENCY RULES

---

# 17.1 Idempotency Principle

Sensitive operations SHOULD support:

* idempotency protection

---

# 17.2 Retry Safety

Retries MUST preserve:

* authentication integrity
* session consistency
* token rotation safety

---

# 18. FAILURE HANDLING RULES

---

# 18.1 Failure Isolation Principle

API failures SHOULD remain:

* observable
* traceable
* recoverable

---

# 18.2 Secure Failure Principle

Security failures MUST:

* deny access safely
* avoid sensitive exposure

---

# 18.3 Retry Protection

Authentication APIs SHOULD avoid:

* retry storms
* brute-force amplification

---

# 19. FORBIDDEN API ANTI-PATTERNS

---

# Forbidden

* Entity exposure
* Raw credential exposure
* Cross-tenant authentication leakage
* Blocking authentication APIs
* ThreadLocal security propagation
* Oversized authentication payloads
* Non-traceable security failures
* Imperative reactive leakage
* Password logging
* Hidden authentication side effects

---

# 20. AI IMPLEMENTATION RULES

All AI-generated IAM APIs MUST:

* remain reactive-safe
* preserve tenant isolation
* avoid sensitive payload exposure
* preserve JWT integrity
* preserve observability
* preserve auditability
* avoid blocking execution
* support distributed scalability
* preserve contract consistency
* avoid entity exposure

---

# 21. CODECORE IAM API PHILOSOPHY

```text id="iamapiphilosophy"
IAM APIs exist to expose
secure, reactive and tenant-aware
authentication capabilities
through traceable stateless contracts,
distributed security propagation
and consistency-preserving communication boundaries.
```

```
```
