# 05-authentication-management/events.md

> **DEPRECATED** — See [DEPRECATED.md](./DEPRECATED.md). Authoritative: [IAM](../01-identity-access-management/).

````md id="r5x8vp"
# Authentication Management Domain Events

## 1. Introduction

This document defines the domain events emitted by the Authentication Management module.

Authentication events represent important business and security occurrences related to:

- Identity verification
- Session lifecycle
- Token management
- MFA operations
- Device trust validation
- Security incidents
- Authentication auditing

These events are fundamental for:

- Event-Driven Architecture (EDA)
- Distributed consistency
- Security monitoring
- Auditability
- Threat detection
- Cache synchronization
- Compliance reporting

The events are designed following:

- Domain-Driven Design (DDD)
- Immutable event principles
- Security-first architecture
- Multi-tenant SaaS standards

---

# 2. Event Design Principles

All authentication events must follow:

| Principle | Description |
|---|---|
| Immutable | Events cannot change |
| Auditable | Full traceability |
| Tenant-aware | Tenant context mandatory |
| Serializable | Messaging compatibility |
| Security-safe | Sensitive data excluded |
| Explicit | Clear business meaning |

---

# 3. Event Categories

| Category | Purpose |
|---|---|
| Authentication Events | Login/authentication lifecycle |
| Session Events | Session management |
| Token Events | JWT/refresh token lifecycle |
| MFA Events | MFA verification |
| Device Trust Events | Trusted device management |
| Security Events | Threat detection |
| Audit Events | Compliance evidence |
| Integration Events | External system synchronization |

---

# 4. Common Event Metadata

All authentication events should include:

| Field | Type | Description |
|---|---|---|
| eventId | UUID | Unique identifier |
| eventType | String | Event name |
| occurredAt | Instant | Timestamp |
| tenantId | String | Tenant context |
| actorId | UUID | User/service actor |
| correlationId | String | Distributed tracing |
| aggregateId | UUID | Aggregate identifier |
| aggregateType | String | Aggregate type |
| version | Integer | Event schema version |

---

# 5. UserAuthenticated Event

## Purpose

Published after successful authentication.

---

## Trigger

```text id="w4n8xp"
Successful login/authentication
````

---

## Payload

| Field                | Type   | Description        |
| -------------------- | ------ | ------------------ |
| userId               | UUID   | Authenticated user |
| sessionId            | UUID   | Session identifier |
| authenticationMethod | String | Login method       |
| ipAddress            | String | Origin             |
| deviceId             | String | Device             |

---

## Consumers

* Audit Service
* Authorization Service
* Notification Service
* Security Monitoring
* Session Cache

---

# 6. AuthenticationFailed Event

## Purpose

Published when authentication fails.

Critical security event.

---

## Payload

| Field             | Type   | Description         |
| ----------------- | ------ | ------------------- |
| username          | String | Attempted identity  |
| tenantId          | String | Tenant              |
| failureReason     | String | Failure explanation |
| ipAddress         | String | Origin              |
| deviceFingerprint | String | Device metadata     |

---

## Usage

Supports:

* Brute force detection
* Threat analysis
* Risk scoring

---

# 7. MFAChallengeGenerated Event

## Purpose

Published when MFA challenge is created.

---

## Payload

| Field         | Type    | Description   |
| ------------- | ------- | ------------- |
| challengeId   | UUID    | MFA challenge |
| userId        | UUID    | User          |
| challengeType | String  | MFA type      |
| expiresAt     | Instant | Expiration    |

---

## Consumers

* Notification Service
* Audit Service

---

# 8. MFAChallengeCompleted Event

## Purpose

Published after successful MFA verification.

---

## Payload

| Field       | Type    | Description          |
| ----------- | ------- | -------------------- |
| challengeId | UUID    | Challenge            |
| userId      | UUID    | User                 |
| completedAt | Instant | Completion timestamp |

---

## Importance

Confirms elevated identity assurance.

---

# 9. MFAChallengeFailed Event

## Purpose

Published after invalid MFA attempt.

---

## Usage

Supports:

* Brute force detection
* Account lockout workflows
* Threat monitoring

---

# 10. SessionCreated Event

## Purpose

Published after session establishment.

---

## Payload

| Field     | Type    | Description   |
| --------- | ------- | ------------- |
| sessionId | UUID    | Session       |
| userId    | UUID    | Session owner |
| expiresAt | Instant | Expiration    |
| deviceId  | String  | Device        |

---

## Side Effects

```text id="m7x3vr"
- Cache population
- Session indexing
- Security monitoring
```

---

# 11. SessionRevoked Event

## Purpose

Published when a session becomes invalid.

---

## Revocation Triggers

```text id="f2v8wt"
- Logout
- Password reset
- Suspicious activity
- Administrative action
```

---

## Payload

| Field            | Type   | Description     |
| ---------------- | ------ | --------------- |
| sessionId        | UUID   | Revoked session |
| revokedBy        | UUID   | Actor           |
| revocationReason | String | Reason          |

---

## Critical Side Effects

```text id="k9n4xp"
- Refresh token invalidation
- Cache invalidation
- JWT revocation synchronization
```

---

# 12. RefreshTokenIssued Event

## Purpose

Published when a refresh token is created.

---

## Payload

| Field          | Type    | Description        |
| -------------- | ------- | ------------------ |
| refreshTokenId | UUID    | Token identifier   |
| sessionId      | UUID    | Associated session |
| expiresAt      | Instant | Expiration         |

---

# 13. RefreshTokenRotated Event

## Purpose

Published after refresh token rotation.

Critical security event.

---

## Payload

| Field             | Type | Description       |
| ----------------- | ---- | ----------------- |
| oldRefreshTokenId | UUID | Previous token    |
| newRefreshTokenId | UUID | Replacement token |
| sessionId         | UUID | Session           |

---

## Security Importance

Supports replay detection and secure rotation chains.

---

# 14. RefreshTokenReplayDetected Event

## Purpose

Published when refresh token reuse is detected.

High-severity security event.

---

## Example

```text id="x1w7pk"
Previously rotated refresh token reused
```

---

## Recommended Actions

```text id="g5m2vx"
- Revoke entire session
- Trigger security alert
- Require re-authentication
```

---

# 15. JWTIssued Event

## Purpose

Published after JWT generation.

---

## Usage

Supports:

* Distributed session synchronization
* Monitoring
* Audit correlation

---

## Important Restriction

JWT payload must never expose secrets.

---

# 16. PasswordResetRequested Event

## Purpose

Published when password reset starts.

---

## Payload

| Field       | Type    | Description |
| ----------- | ------- | ----------- |
| userId      | UUID    | User        |
| tenantId    | String  | Tenant      |
| requestedAt | Instant | Timestamp   |

---

## Consumers

* Notification Service
* Audit Service
* Security Monitoring

---

# 17. PasswordChanged Event

## Purpose

Published after successful password rotation.

---

## Critical Side Effects

```text id="t6v9wr"
- Revoke sessions
- Revoke refresh tokens
- Force re-authentication
```

---

## Security Importance

Potential compromise response workflow.

---

# 18. TrustedDeviceRegistered Event

## Purpose

Published when a device becomes trusted.

---

## Payload

| Field         | Type   | Description        |
| ------------- | ------ | ------------------ |
| deviceTrustId | UUID   | Trusted device     |
| userId        | UUID   | Owner              |
| fingerprint   | String | Device fingerprint |

---

# 19. TrustedDeviceRevoked Event

## Purpose

Published when trust is removed.

---

## Example Triggers

```text id="r4x8vn"
- Suspicious activity
- User revocation
- Security policy changes
```

---

# 20. SuspiciousLoginDetected Event

## Purpose

Published when anomalous authentication behavior is detected.

---

## Detection Examples

```text id="p7n2wt"
- Impossible travel
- Unknown device
- Excessive failures
- Token replay
- Geo anomalies
```

---

## Payload

| Field         | Type   | Description         |
| ------------- | ------ | ------------------- |
| userId        | UUID   | Affected user       |
| severity      | String | LOW/MEDIUM/HIGH     |
| detectionType | String | Threat category     |
| evidence      | Object | Supporting evidence |

---

# 21. AccountLocked Event

## Purpose

Published after account lockout.

---

## Trigger Examples

```text id="n8v4xp"
- Excessive failures
- Credential stuffing
- Security policy enforcement
```

---

## Usage

Supports:

* User notification
* Security response
* Threat analytics

---

# 22. OAuthIdentityLinked Event

## Purpose

Published when external identity is linked.

---

## Example Providers

```text id="j3m7wr"
Google
Microsoft
Okta
```

---

## Usage

Supports identity federation auditing.

---

# 23. APIKeyCreated Event

## Purpose

Published after API key generation.

---

## Important Restriction

API secrets must never appear in events.

---

# 24. APIKeyRevoked Event

## Purpose

Published after API key invalidation.

---

## Side Effects

```text id="u1x6vt"
- Access invalidation
- Cache refresh
- Integration synchronization
```

---

# 25. ServiceAuthenticated Event

## Purpose

Published after successful service authentication.

---

## Usage

Supports:

* Internal auditability
* Service tracing
* Zero Trust observability

---

# 26. AuthenticationAuditRecorded Event

## Purpose

Published after immutable audit persistence.

---

## Consumers

* SIEM systems
* Compliance tooling
* Security analytics

---

# 27. TenantAuthenticationPolicyChanged Event

## Purpose

Published after tenant authentication policy updates.

---

## Example Policy Changes

```text id="y9k4wp"
- MFA required
- Session duration changes
- Password policy updates
```

---

## Side Effects

```text id="v2n8xr"
- Session revalidation
- Policy cache invalidation
```

---

# 28. Event Ordering Considerations

Certain events require ordering guarantees.

---

## Example

```text id="q6m1vt"
SessionCreated
    before
JWTIssued
```

---

## Recommended Strategies

| Strategy           | Purpose             |
| ------------------ | ------------------- |
| Aggregate ordering | Session consistency |
| Kafka partitioning | Tenant ordering     |
| Outbox pattern     | Reliable publishing |

---

# 29. Event Delivery Guarantees

Recommended semantics:

| Event Type        | Guarantee                    |
| ----------------- | ---------------------------- |
| Security events   | At least once                |
| Audit events      | Durable delivery             |
| Cache sync events | Best effort acceptable       |
| Session events    | Strong consistency preferred |

---

# 30. Sensitive Data Restrictions

Authentication events must NEVER expose:

* Plain passwords
* JWT secrets
* API secrets
* MFA secrets
* Refresh token values
* Raw credentials

---

# 31. Recommended Messaging Infrastructure

| Technology    | Suitability                |
| ------------- | -------------------------- |
| Kafka         | High-scale event streaming |
| RabbitMQ      | Flexible routing           |
| Redis Streams | Lightweight eventing       |
| AWS SNS/SQS   | Cloud-native integration   |

---

# 32. Event Replay Considerations

Replay-compatible events:

| Event                | Reason                 |
| -------------------- | ---------------------- |
| SessionCreated       | Session reconstruction |
| PasswordChanged      | Security auditing      |
| AuthenticationFailed | Threat analytics       |

---

# 33. CQRS Integration

Events may update projections including:

* Active session views
* Security dashboards
* Login analytics
* Device trust views
* Threat monitoring dashboards

---

# 34. Distributed System Considerations

Events support:

* Multi-region deployments
* Horizontal scaling
* Reactive systems
* Distributed cache synchronization
* Eventual consistency

---

# 35. Failure Handling Rules

If publication fails:

| Event Type         | Strategy                     |
| ------------------ | ---------------------------- |
| Security-critical  | Retry mandatory              |
| Audit              | Durable persistence required |
| Cache invalidation | Retry recommended            |

---

# 36. Future Event Extensions

Future events may include:

* PasswordlessLoginCompleted
* WebAuthnChallengeCompleted
* BiometricAuthenticationCompleted
* AdaptiveAuthenticationTriggered
* ContinuousAuthenticationFailed
* HardwareSecurityKeyRegistered

---

# 37. Summary

The Authentication Management events provide:

* Enterprise-grade authentication auditability
* Distributed authentication synchronization
* Security monitoring integration
* Threat detection support
* Session consistency
* Reactive authentication communication
* Scalable event-driven authentication architecture

These events form the integration backbone of the authentication ecosystem.

```
```
