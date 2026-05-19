# 05-authentication-management/api-contracts.md

> **DEPRECATED** — See [DEPRECATED.md](./DEPRECATED.md). Authoritative: [IAM](../01-identity-access-management/).

````md id="x7v3wp"
# Authentication Management API Contracts

## 1. Introduction

This document defines the API contracts of the Authentication Management module.

The APIs expose secure authentication capabilities including:

- User authentication
- Session management
- JWT issuance
- Refresh token rotation
- MFA workflows
- Password recovery
- Device trust management
- OAuth2/OIDC integration
- API key management
- Service authentication

The contracts are designed following:

- RESTful principles
- Secure-by-default architecture
- Multi-tenant SaaS standards
- Zero Trust security principles
- Enterprise authentication best practices

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Stateless authentication | JWT-based access |
| Tenant-aware operations | Tenant isolation mandatory |
| Explicit authentication | No implicit trust |
| Immutable auditability | Security actions tracked |
| Secure defaults | Deny by default |
| Idempotent security operations | Safe retries |
| Versioned APIs | Backward compatibility |

---

# 3. Base URL

```text id="n2x8vt"
/api/v1/authentication
````

---

# 4. Common Headers

| Header           | Required    | Description         |
| ---------------- | ----------- | ------------------- |
| Authorization    | Conditional | Bearer token        |
| X-Tenant-ID      | Yes         | Tenant context      |
| X-Correlation-ID | Recommended | Distributed tracing |
| Content-Type     | Yes         | application/json    |

---

# 5. Authentication Endpoints

# 5.1 Username/Password Login

## Endpoint

```text id="m7w3xr"
POST /login
```

---

## Purpose

Authenticates user credentials.

---

## Request

```json id="v4k8wp"
{
  "username": "john@example.com",
  "password": "securePassword",
  "deviceId": "device-123"
}
```

---

## Response

```json id="t9n2vx"
{
  "success": true,
  "data": {
    "accessToken": "jwt-token",
    "refreshToken": "refresh-token",
    "expiresIn": 900,
    "sessionId": "uuid"
  }
}
```

---

## Possible Responses

| Status | Meaning                |
| ------ | ---------------------- |
| 200    | Authentication success |
| 401    | Invalid credentials    |
| 423    | Account locked         |
| 428    | MFA required           |

---

## Security Rules

* Rate limiting mandatory
* Audit logging mandatory
* Passwords never logged

---

# 5.2 MFA Verification

## Endpoint

```text id="r5x1vt"
POST /mfa/verify
```

---

## Purpose

Validates MFA challenge.

---

## Request

```json id="g8m4wp"
{
  "challengeId": "uuid",
  "verificationCode": "123456"
}
```

---

## Response

```json id="y3v7xr"
{
  "success": true,
  "data": {
    "accessToken": "jwt-token",
    "refreshToken": "refresh-token"
  }
}
```

---

## Validation Rules

* Challenge expiration enforced
* Single-use codes only
* Brute force protection required

---

# 5.3 Refresh Token Rotation

## Endpoint

```text id="u1k9vp"
POST /token/refresh
```

---

## Purpose

Rotates refresh token and issues new JWT.

---

## Request

```json id="f6w2xt"
{
  "refreshToken": "refresh-token"
}
```

---

## Response

```json id="p4n8vr"
{
  "success": true,
  "data": {
    "accessToken": "new-jwt",
    "refreshToken": "new-refresh-token"
  }
}
```

---

## Security Rules

| Rule                      | Description       |
| ------------------------- | ----------------- |
| Rotation mandatory        | Replay prevention |
| Old token invalidated     | Security          |
| Replay detection required | Threat protection |

---

# 5.4 Logout

## Endpoint

```text id="x8m3wt"
POST /logout
```

---

## Purpose

Revokes active session.

---

## Required Authentication

```text id="j7v4xp"
Bearer JWT
```

---

## Actions

```text id="k2n9vr"
- Revoke session
- Revoke refresh token
- Invalidate caches
```

---

## Response

```json id="d5x1wp"
{
  "success": true
}
```

---

# 6. Session Management APIs

# 6.1 List Active Sessions

## Endpoint

```text id="m3v8xt"
GET /sessions
```

---

## Purpose

Returns active authenticated sessions.

---

## Response

```json id="v6n2wr"
{
  "success": true,
  "data": [
    {
      "sessionId": "uuid",
      "deviceId": "device-123",
      "ipAddress": "192.168.1.1",
      "createdAt": "2026-05-18T10:00:00Z"
    }
  ]
}
```

---

# 6.2 Revoke Session

## Endpoint

```text id="q9x4vp"
DELETE /sessions/{sessionId}
```

---

## Purpose

Revokes specific session.

---

## Security Rules

* Ownership validation mandatory
* Audit logging mandatory

---

# 7. Password Management APIs

# 7.1 Request Password Reset

## Endpoint

```text id="f2w7xn"
POST /password/reset-request
```

---

## Request

```json id="u8k3vt"
{
  "email": "john@example.com"
}
```

---

## Security Rules

* Avoid account enumeration
* Generic responses recommended
* Rate limiting mandatory

---

## Example Response

```json id="g4m9wr"
{
  "success": true,
  "message": "If the account exists, reset instructions were sent."
}
```

---

# 7.2 Complete Password Reset

## Endpoint

```text id="r1v6xp"
POST /password/reset
```

---

## Request

```json id="y7k2wt"
{
  "resetToken": "token",
  "newPassword": "newPassword123!"
}
```

---

## Actions

```text id="n5x8vr"
- Rotate password hash
- Revoke sessions
- Revoke refresh tokens
```

---

## Security Rules

* Reset token expiration enforced
* Password complexity validation mandatory

---

# 7.3 Change Password

## Endpoint

```text id="w4n1xp"
POST /password/change
```

---

## Required Authentication

```text id="v9m3wt"
Bearer JWT
```

---

## Request

```json id="x2k7vr"
{
  "currentPassword": "oldPassword",
  "newPassword": "newPassword"
}
```

---

## Security Rules

* Current password validation mandatory
* Audit logging required

---

# 8. Trusted Device APIs

# 8.1 Register Trusted Device

## Endpoint

```text id="j6v8wp"
POST /devices/trust
```

---

## Purpose

Registers trusted device.

---

## Request

```json id="k1x4vr"
{
  "deviceFingerprint": "fingerprint"
}
```

---

## Security Rules

* Requires successful authentication
* MFA recommended

---

# 8.2 Revoke Trusted Device

## Endpoint

```text id="s3m9wt"
DELETE /devices/{deviceId}
```

---

## Purpose

Removes trusted device.

---

## Side Effects

```text id="p8n2vx"
- Device trust invalidation
- Future MFA enforcement
```

---

# 9. OAuth2/OIDC APIs

# 9.1 OAuth Login Redirect

## Endpoint

```text id="t5v1xr"
GET /oauth2/{provider}/authorize
```

---

## Supported Providers

```text id="q7k4wp"
google
microsoft
okta
auth0
keycloak
```

---

# 9.2 OAuth Callback

## Endpoint

```text id="g9x3vt"
GET /oauth2/{provider}/callback
```

---

## Purpose

Handles provider authentication callback.

---

## Security Rules

* State validation mandatory
* Signature validation mandatory

---

# 10. API Key APIs

# 10.1 Create API Key

## Endpoint

```text id="d4m8wr"
POST /api-keys
```

---

## Required Permission

```text id="z6v2xp"
CREATE_API_KEY
```

---

## Request

```json id="u3n7wt"
{
  "name": "Billing Integration",
  "scopes": [
    "READ_PATIENT"
  ]
}
```

---

## Response

```json id="m8x1vr"
{
  "success": true,
  "data": {
    "apiKey": "generated-secret",
    "prefix": "pk_live"
  }
}
```

---

## Important Rule

API key secret shown only once.

---

# 10.2 Revoke API Key

## Endpoint

```text id="r5k9wp"
DELETE /api-keys/{apiKeyId}
```

---

## Actions

```text id="x1n4vt"
- Revoke key
- Invalidate access
- Persist audit
```

---

# 11. Internal Service Authentication APIs

# 11.1 Service Authentication

## Endpoint

```text id="n7v3xr"
POST /internal/service-authenticate
```

---

## Purpose

Authenticates internal services.

---

## Security Requirements

| Requirement                 | Description    |
| --------------------------- | -------------- |
| mTLS recommended            | Internal trust |
| Service identity validation | Mandatory      |
| Signed tokens recommended   | Security       |

---

## Request

```json id="p2x8wt"
{
  "clientId": "billing-service",
  "clientSecret": "secret"
}
```

---

# 12. Authentication Audit APIs

# 12.1 List Authentication Audits

## Endpoint

```text id="w6m1vp"
GET /audit
```

---

## Required Permission

```text id="h8k4xr"
VIEW_AUTHENTICATION_AUDIT
```

---

## Query Parameters

| Parameter | Description     |
| --------- | --------------- |
| userId    | Filter by user  |
| result    | SUCCESS/FAILURE |
| ipAddress | Filter by IP    |
| startDate | Date range      |
| endDate   | Date range      |

---

## Security Restrictions

* Tenant-scoped visibility mandatory
* Sensitive metadata filtering required

---

# 13. Security Monitoring APIs

# 13.1 Suspicious Authentication Search

## Endpoint

```text id="c5v9wt"
GET /security/suspicious-logins
```

---

## Required Permission

```text id="y2n7xp"
VIEW_SECURITY_ALERTS
```

---

## Example Response

```json id="g1m4vr"
{
  "success": true,
  "data": [
    {
      "severity": "HIGH",
      "type": "TOKEN_REPLAY",
      "detectedAt": "2026-05-18T12:00:00Z"
    }
  ]
}
```

---

# 14. Common Response Structure

## Success Response

```json id="u7k2wr"
{
  "success": true,
  "timestamp": "2026-05-18T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="f9x3vt"
{
  "success": false,
  "timestamp": "2026-05-18T10:00:00Z",
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Authentication failed",
    "details": []
  }
}
```

---

# 15. HTTP Status Codes

| Status | Meaning             |
| ------ | ------------------- |
| 200    | Success             |
| 201    | Resource created    |
| 204    | No content          |
| 400    | Invalid request     |
| 401    | Unauthenticated     |
| 403    | Unauthorized        |
| 404    | Resource not found  |
| 409    | Conflict            |
| 423    | Account locked      |
| 428    | MFA required        |
| 429    | Rate limit exceeded |
| 500    | Internal error      |

---

# 16. Pagination Standards

Paginated endpoints should return:

```json id="q4v8wp"
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 100,
    "totalPages": 5
  }
}
```

---

# 17. Sorting Standards

## Example

```text id="m1x6vr"
?sort=createdAt,desc
```

---

# 18. Security Headers

Recommended headers:

| Header             | Purpose           |
| ------------------ | ----------------- |
| X-Tenant-ID        | Tenant isolation  |
| X-Correlation-ID   | Tracing           |
| X-Request-ID       | Request tracking  |
| X-Service-Identity | Internal services |

---

# 19. Rate Limiting Recommendations

| Endpoint Category | Recommendation |
| ----------------- | -------------- |
| Login             | Strict         |
| MFA               | Strict         |
| Token refresh     | Medium         |
| Audit APIs        | Medium         |
| Internal APIs     | Controlled     |

---

# 20. API Security Rules

## Deny by Default

Invalid authentication:

```text id="v5n9xt"
401 UNAUTHORIZED
```

---

## Tenant Isolation

Tenant mismatch:

```text id="j8m2wr"
403 FORBIDDEN
```

---

## Sensitive Data Restrictions

Never expose:

* Passwords
* JWT secrets
* MFA secrets
* Raw refresh tokens in logs
* API secrets after creation

---

# 21. Reactive API Considerations

Reactive implementations should support:

```text id="r3v7xp"
Mono<ResponseEntity<?>>
Flux<ResponseEntity<?>>
```

---

## Requirements

* Non-blocking authentication
* Reactive security context propagation
* Async MFA handling

---

# 22. OpenAPI Recommendations

Recommended documentation:

* OpenAPI 3.x
* Swagger UI
* Security scheme definitions
* OAuth2/OpenID metadata

---

# 23. API Versioning Strategy

Recommended:

```text id="k7x4vt"
/api/v1/authentication
```

Future evolution:

```text id="u2m8wr"
/api/v2/authentication
```

---

# 24. Error Codes

| Code                  | Description             |
| --------------------- | ----------------------- |
| INVALID_CREDENTIALS   | Login failed            |
| ACCOUNT_LOCKED        | Locked account          |
| MFA_REQUIRED          | MFA challenge pending   |
| MFA_FAILED            | MFA verification failed |
| TOKEN_EXPIRED         | Expired token           |
| TOKEN_REVOKED         | Revoked token           |
| TOKEN_REPLAY_DETECTED | Replay attempt          |
| INVALID_SESSION       | Invalid session         |
| TENANT_MISMATCH       | Cross-tenant violation  |
| PASSWORD_EXPIRED      | Password expired        |

---

# 25. Future API Extensions

Future APIs may include:

* Passwordless authentication APIs
* WebAuthn APIs
* Biometric authentication APIs
* Adaptive authentication APIs
* Continuous authentication APIs
* Risk-based MFA APIs

---

# 26. Summary

The Authentication Management API contracts provide:

* Secure authentication operations
* Enterprise-grade session handling
* MFA orchestration
* Distributed authentication support
* Reactive authentication scalability
* Multi-tenant authentication isolation
* Zero Trust authentication foundations

These APIs form the external contract layer of the authentication ecosystem.

```
```
