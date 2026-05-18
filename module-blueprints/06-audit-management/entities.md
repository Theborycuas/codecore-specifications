# 05-authentication-management/entities.md

````md id="v8k3wp"
# Authentication Management Entities

## 1. Introduction

This document defines the entities of the Authentication Management module.

Entities represent domain objects that:

- Possess identity
- Maintain lifecycle continuity
- Encapsulate authentication behaviors
- Enforce security constraints
- Participate in authentication workflows

The entities are designed following:

- Domain-Driven Design (DDD)
- Security-first architecture
- Multi-tenant SaaS principles
- Zero Trust authentication models

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| Authentication | Represents an authentication attempt/result |
| AuthenticatedSession | Represents an authenticated user session |
| RefreshToken | Represents refresh token lifecycle |
| MFAChallenge | Represents MFA verification process |
| TrustedDevice | Represents a trusted user device |
| APIKey | Represents API authentication credentials |
| ServiceIdentity | Represents internal service identity |
| AuthenticationAuditRecord | Represents immutable authentication evidence |
| LoginAttempt | Represents authentication attempt tracking |
| SessionSecurityState | Represents runtime session security |
| OAuthIdentity | Represents external identity provider linkage |
| PasswordCredential | Represents protected password credentials |

---

# 3. Authentication Entity

## Purpose

Represents the authentication process and result.

Coordinates:

- Credential validation
- MFA enforcement
- Account validation
- Session establishment

---

## Identity

```text id="m5x9vr"
authenticationId
````

---

## Attributes

| Attribute          | Type    | Description               |
| ------------------ | ------- | ------------------------- |
| authenticationId   | UUID    | Authentication identifier |
| userId             | UUID    | Authenticated user        |
| tenantId           | String  | Tenant context            |
| authenticationType | Enum    | Authentication method     |
| authenticated      | Boolean | Authentication result     |
| authenticatedAt    | Instant | Authentication timestamp  |
| ipAddress          | String  | Request origin            |
| deviceId           | String  | Device identifier         |
| failureReason      | String  | Failure explanation       |

---

## Behaviors

| Behavior         | Description                |
| ---------------- | -------------------------- |
| authenticate()   | Performs authentication    |
| fail()           | Registers failure          |
| complete()       | Finalizes authentication   |
| validateTenant() | Verifies tenant membership |

---

## Business Rules

* Authentication requires valid credentials
* Tenant validation mandatory
* Suspicious attempts tracked
* MFA enforced when required

---

# 4. AuthenticatedSession Entity

## Purpose

Represents an authenticated session.

Supports:

* Session continuity
* Session revocation
* Device tracking
* Security monitoring

---

## Identity

```text id="q1v7pk"
sessionId
```

---

## Attributes

| Attribute | Type    | Description          |
| --------- | ------- | -------------------- |
| sessionId | UUID    | Session identifier   |
| userId    | UUID    | Session owner        |
| tenantId  | String  | Tenant context       |
| createdAt | Instant | Creation timestamp   |
| expiresAt | Instant | Expiration timestamp |
| revoked   | Boolean | Revocation status    |
| revokedAt | Instant | Revocation timestamp |
| deviceId  | String  | Device identifier    |
| ipAddress | String  | Origin IP            |
| userAgent | String  | Client metadata      |

---

## Behaviors

| Behavior   | Description              |
| ---------- | ------------------------ |
| revoke()   | Revokes session          |
| extend()   | Extends expiration       |
| validate() | Validates active session |
| expire()   | Marks expired            |

---

## Business Rules

* Revoked sessions invalid
* Expired sessions unusable
* Tenant context immutable
* Session ownership immutable

---

# 5. RefreshToken Entity

## Purpose

Represents refresh token lifecycle.

Supports secure token rotation.

---

## Identity

```text id="x6n2wt"
refreshTokenId
```

---

## Attributes

| Attribute      | Type    | Description          |
| -------------- | ------- | -------------------- |
| refreshTokenId | UUID    | Token identifier     |
| sessionId      | UUID    | Associated session   |
| tokenHash      | String  | Hashed token         |
| issuedAt       | Instant | Issuance timestamp   |
| expiresAt      | Instant | Expiration timestamp |
| revoked        | Boolean | Revocation status    |
| rotated        | Boolean | Rotation status      |
| replacedBy     | UUID    | Replacement token    |

---

## Behaviors

| Behavior       | Description            |
| -------------- | ---------------------- |
| rotate()       | Rotates token          |
| revoke()       | Revokes token          |
| validate()     | Validates token        |
| detectReplay() | Detects replay attacks |

---

## Business Rules

* Refresh tokens expire
* Rotated tokens invalid
* Replay attempts denied
* Tokens stored hashed

---

# 6. MFAChallenge Entity

## Purpose

Represents a multi-factor authentication challenge.

---

## Identity

```text id="r9w4vx"
challengeId
```

---

## Attributes

| Attribute      | Type    | Description          |
| -------------- | ------- | -------------------- |
| challengeId    | UUID    | Challenge identifier |
| userId         | UUID    | User owner           |
| tenantId       | String  | Tenant context       |
| challengeType  | Enum    | MFA method           |
| challengeCode  | String  | Verification code    |
| expiresAt      | Instant | Expiration timestamp |
| verified       | Boolean | Verification result  |
| failedAttempts | Integer | Invalid attempts     |

---

## Behaviors

| Behavior            | Description        |
| ------------------- | ------------------ |
| verify()            | Validates MFA code |
| expire()            | Expires challenge  |
| incrementFailures() | Tracks failures    |

---

## Business Rules

* MFA challenges expire quickly
* Verification codes single-use
* Excessive failures trigger lockout

---

# 7. TrustedDevice Entity

## Purpose

Represents a trusted device relationship.

Supports adaptive authentication.

---

## Identity

```text id="k4x8tp"
deviceTrustId
```

---

## Attributes

| Attribute         | Type    | Description                |
| ----------------- | ------- | -------------------------- |
| deviceTrustId     | UUID    | Device trust identifier    |
| userId            | UUID    | Device owner               |
| tenantId          | String  | Tenant context             |
| deviceFingerprint | String  | Browser/device fingerprint |
| trusted           | Boolean | Trust status               |
| registeredAt      | Instant | Registration timestamp     |
| lastUsedAt        | Instant | Last usage timestamp       |
| revokedAt         | Instant | Revocation timestamp       |

---

## Behaviors

| Behavior        | Description           |
| --------------- | --------------------- |
| trust()         | Marks trusted         |
| revokeTrust()   | Removes trust         |
| validateTrust() | Validates trust state |

---

## Business Rules

* Device ownership immutable
* Revoked devices invalid
* Tenant isolation mandatory

---

# 8. APIKey Entity

## Purpose

Represents API authentication credentials.

---

## Identity

```text id="n2m7wr"
apiKeyId
```

---

## Attributes

| Attribute | Type         | Description        |
| --------- | ------------ | ------------------ |
| apiKeyId  | UUID         | API key identifier |
| tenantId  | String       | Tenant context     |
| keyHash   | String       | Hashed secret      |
| keyPrefix | String       | Public prefix      |
| scopes    | List<String> | Allowed scopes     |
| expiresAt | Instant      | Expiration         |
| revoked   | Boolean      | Revocation state   |
| createdAt | Instant      | Creation timestamp |

---

## Behaviors

| Behavior   | Description     |
| ---------- | --------------- |
| revoke()   | Revokes API key |
| validate() | Validates key   |
| rotate()   | Rotates secret  |

---

## Business Rules

* API secrets never retrievable
* Revoked keys unusable
* Expired keys denied

---

# 9. ServiceIdentity Entity

## Purpose

Represents internal service authentication identity.

---

## Identity

```text id="w5v1zx"
serviceIdentityId
```

---

## Attributes

| Attribute         | Type    | Description            |
| ----------------- | ------- | ---------------------- |
| serviceIdentityId | UUID    | Service identifier     |
| serviceName       | String  | Unique service name    |
| clientId          | String  | Service client ID      |
| secretHash        | String  | Service secret         |
| active            | Boolean | Activation state       |
| createdAt         | Instant | Registration timestamp |

---

## Behaviors

| Behavior              | Description       |
| --------------------- | ----------------- |
| activate()            | Enables identity  |
| deactivate()          | Disables identity |
| validateCredentials() | Validates secret  |

---

## Business Rules

* Service names unique
* Internal identities protected
* Secrets stored hashed

---

# 10. AuthenticationAuditRecord Entity

## Purpose

Represents immutable authentication evidence.

---

## Identity

```text id="p8k4vr"
auditRecordId
```

---

## Attributes

| Attribute     | Type    | Description          |
| ------------- | ------- | -------------------- |
| auditRecordId | UUID    | Audit identifier     |
| userId        | UUID    | User                 |
| tenantId      | String  | Tenant               |
| eventType     | String  | Authentication event |
| result        | String  | SUCCESS/FAILURE      |
| ipAddress     | String  | Origin               |
| deviceId      | String  | Device               |
| occurredAt    | Instant | Timestamp            |
| metadata      | Object  | Additional evidence  |

---

## Behaviors

| Behavior         | Description     |
| ---------------- | --------------- |
| persist()        | Stores evidence |
| appendMetadata() | Adds details    |

---

## Business Rules

* Audit evidence immutable
* Sensitive data forbidden
* Retention policies enforced

---

# 11. LoginAttempt Entity

## Purpose

Tracks login attempts for security analysis.

---

## Identity

```text id="t7n3wp"
loginAttemptId
```

---

## Attributes

| Attribute      | Type    | Description        |
| -------------- | ------- | ------------------ |
| loginAttemptId | UUID    | Attempt identifier |
| username       | String  | Attempted identity |
| tenantId       | String  | Tenant             |
| success        | Boolean | Result             |
| ipAddress      | String  | Origin             |
| attemptedAt    | Instant | Timestamp          |

---

## Behaviors

| Behavior      | Description     |
| ------------- | --------------- |
| markSuccess() | Records success |
| markFailure() | Records failure |

---

## Usage

Supports:

* Brute force detection
* Threat analysis
* Lockout policies

---

# 12. SessionSecurityState Entity

## Purpose

Represents runtime session security state.

---

## Identity

```text id="g3x9vk"
securityStateId
```

---

## Attributes

| Attribute       | Type    | Description      |
| --------------- | ------- | ---------------- |
| securityStateId | UUID    | State identifier |
| sessionId       | UUID    | Session          |
| suspicious      | Boolean | Risk state       |
| riskScore       | Integer | Risk evaluation  |
| lastValidatedAt | Instant | Last validation  |

---

## Behaviors

| Behavior          | Description   |
| ----------------- | ------------- |
| markSuspicious()  | Flags session |
| updateRiskScore() | Updates score |

---

# 13. OAuthIdentity Entity

## Purpose

Represents linkage to external identity providers.

---

## Identity

```text id="v1m5tr"
oauthIdentityId
```

---

## Attributes

| Attribute       | Type    | Description       |
| --------------- | ------- | ----------------- |
| oauthIdentityId | UUID    | Identity link ID  |
| provider        | String  | OAuth provider    |
| providerUserId  | String  | External identity |
| userId          | UUID    | Internal user     |
| linkedAt        | Instant | Link timestamp    |

---

## Behaviors

| Behavior | Description             |
| -------- | ----------------------- |
| link()   | Links external identity |
| unlink() | Removes linkage         |

---

## Supported Providers

```text id="y8k2xp"
Google
Microsoft
Okta
Auth0
Keycloak
```

---

# 14. PasswordCredential Entity

## Purpose

Represents protected password credentials.

---

## Identity

```text id="c4v7wn"
credentialId
```

---

## Attributes

| Attribute         | Type    | Description           |
| ----------------- | ------- | --------------------- |
| credentialId      | UUID    | Credential identifier |
| userId            | UUID    | Owner                 |
| passwordHash      | String  | Hashed password       |
| algorithm         | String  | Hash algorithm        |
| passwordChangedAt | Instant | Rotation timestamp    |
| expiresAt         | Instant | Expiration            |

---

## Behaviors

| Behavior             | Description       |
| -------------------- | ----------------- |
| validatePassword()   | Verifies secret   |
| rotatePassword()     | Changes password  |
| validateExpiration() | Checks expiration |

---

## Business Rules

* Plaintext passwords forbidden
* Strong hashing required
* Password rotation supported

---

# 15. Entity Relationships

```text id="h6w2vt"
Authentication
    ├── creates -> AuthenticatedSession
    ├── generates -> RefreshToken
    ├── validates -> MFAChallenge
    └── validates -> TrustedDevice

AuthenticatedSession
    └── tracked by -> SessionSecurityState

OAuthIdentity
    └── linked to -> User
```

---

# 16. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="j9x4rp"
- Authentication
- AuthenticatedSession
- RefreshToken
- MFAChallenge
- TrustedDevice
- APIKey
- AuthenticationAuditRecord
```

---

# 17. Security-Critical Rules

## Immutable Tenant Context

Authentication state cannot cross tenants.

---

## Secret Protection

Sensitive values must be:

```text id="s2n8vx"
- Hashed
- Encrypted
- Non-retrievable
```

---

## Fail Closed Principle

Authentication validation failures:

```text id="d5k1wr"
AUTHENTICATION = DENIED
```

---

# 18. Auditing Requirements

Mandatory audit actions:

| Action             | Audit Required |
| ------------------ | -------------- |
| Login success      | Yes            |
| Login failure      | Yes            |
| MFA validation     | Yes            |
| Session revocation | Yes            |
| Password rotation  | Yes            |

---

# 19. Lifecycle Considerations

| Entity                    | Lifecycle        |
| ------------------------- | ---------------- |
| Authentication            | Short-lived      |
| AuthenticatedSession      | Session duration |
| RefreshToken              | Rotation chain   |
| MFAChallenge              | Ephemeral        |
| AuthenticationAuditRecord | Long retention   |

---

# 20. Future Entity Extensions

Future entities may include:

* WebAuthnCredential
* BiometricCredential
* RiskAssessment
* AdaptiveAuthenticationProfile
* PasswordlessChallenge
* HardwareSecurityKey

---

# 21. Summary

The Authentication Management entities provide:

* Secure identity modeling
* Strong session integrity
* MFA orchestration
* Token lifecycle protection
* Trusted device management
* Distributed authentication support
* Enterprise-grade authentication enforcement

These entities form the operational foundation of the authentication ecosystem.

```
```
