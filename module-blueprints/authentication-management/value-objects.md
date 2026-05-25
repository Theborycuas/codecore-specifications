# 05-authentication-management/value-objects.md

> **DEPRECATED** — See [DEPRECATED.md](./DEPRECATED.md). Authoritative: [IAM](../01-identity-access-management/).

````md id="n7v4xp"
# Authentication Management Value Objects

## 1. Introduction

This document defines the Value Objects used in the Authentication Management module.

Value Objects represent immutable conceptual elements that:

- Have no identity
- Are compared by value
- Encapsulate validation logic
- Improve domain expressiveness
- Enforce authentication consistency
- Strengthen security guarantees

The Value Objects are designed following:

- Domain-Driven Design (DDD)
- Immutable modeling principles
- Security-first architecture
- Multi-tenant SaaS authentication standards

---

# 2. Value Object Overview

| Value Object | Purpose |
|---|---|
| EmailAddress | Represents validated email identities |
| Username | Represents login usernames |
| PasswordHash | Represents protected password hashes |
| PlainPassword | Represents transient password input |
| JWTToken | Represents access token structure |
| RefreshTokenValue | Represents refresh token value |
| SessionIdentifier | Represents secure session identifiers |
| MFAChallengeCode | Represents MFA verification codes |
| DeviceFingerprint | Represents trusted device fingerprints |
| TenantAuthenticationContext | Represents tenant-aware authentication context |
| AuthenticationResult | Represents authentication outcome |
| TokenExpiration | Represents token/session expiration |
| AuthenticationMethod | Represents authentication type |
| IPAddress | Represents validated network origins |
| UserAgent | Represents client metadata |
| OAuthProvider | Represents external identity providers |
| SecurityRiskLevel | Represents authentication risk classification |
| AuthenticationFailureReason | Represents authentication failure explanations |

---

# 3. EmailAddress

## Purpose

Represents a validated email identity.

Used for:

- Login
- Notifications
- MFA delivery
- Identity verification

---

## Examples

```text id="m5x2wr"
john@example.com
admin@tenant-a.com
````

---

## Validation Rules

| Rule                                    | Description       |
| --------------------------------------- | ----------------- |
| RFC-compliant format                    | Email correctness |
| Lowercase normalization                 | Consistency       |
| Max length enforced                     | Security          |
| Disposable domains optional restriction | Fraud prevention  |

---

## Behaviors

| Behavior         | Description          |
| ---------------- | -------------------- |
| normalize()      | Lowercases email     |
| validateFormat() | Validates structure  |
| extractDomain()  | Returns email domain |

---

# 4. Username

## Purpose

Represents authentication usernames.

---

## Examples

```text id="t8n4vp"
john.doe
admin_user
psychologist01
```

---

## Validation Rules

| Rule                         | Description   |
| ---------------------------- | ------------- |
| Minimum length               | Security      |
| Maximum length               | Prevent abuse |
| Reserved names forbidden     | Protection    |
| Unsafe characters restricted | Security      |

---

## Forbidden Examples

```text id="v3k7xt"
root
system
administrator
```

---

## Behaviors

| Behavior                | Description               |
| ----------------------- | ------------------------- |
| normalize()             | Standardizes value        |
| validateReservedWords() | Prevents restricted names |

---

# 5. PasswordHash

## Purpose

Represents securely hashed passwords.

Plaintext passwords must never persist.

---

## Supported Algorithms

```text id="q1w9zr"
Argon2
bcrypt
PBKDF2
```

---

## Validation Rules

| Rule                     | Description       |
| ------------------------ | ----------------- |
| Plaintext forbidden      | Security          |
| Approved algorithms only | Hardening         |
| Salt required            | Replay protection |

---

## Behaviors

| Behavior           | Description           |
| ------------------ | --------------------- |
| verify()           | Verifies password     |
| extractAlgorithm() | Returns algorithm     |
| requiresRehash()   | Detects outdated hash |

---

# 6. PlainPassword

## Purpose

Represents transient password input.

Exists only during validation.

---

## Validation Rules

| Rule                                 | Description    |
| ------------------------------------ | -------------- |
| Minimum complexity                   | Security       |
| Maximum length                       | DOS protection |
| Breached password detection optional | Hardening      |
| Short-lived memory presence          | Protection     |

---

## Complexity Requirements

Recommended:

```text id="w6m2ty"
- Uppercase
- Lowercase
- Numbers
- Special characters
- Minimum 12 characters
```

---

## Important Rule

PlainPassword must never be:

* Logged
* Persisted
* Published in events
* Cached

---

# 7. JWTToken

## Purpose

Represents JWT access tokens.

---

## Recommended Claims

```text id="r9x4pk"
sub
tenantId
roles
permissions
sessionId
iat
exp
iss
aud
```

---

## Behaviors

| Behavior             | Description         |
| -------------------- | ------------------- |
| validateSignature()  | Validates integrity |
| validateExpiration() | Checks expiration   |
| extractClaims()      | Returns claims      |

---

## Validation Rules

| Rule                            | Description |
| ------------------------------- | ----------- |
| Signature validation mandatory  | Integrity   |
| Expiration enforced             | Security    |
| Issuer validation required      | Trust       |
| Audience validation recommended | Isolation   |

---

# 8. RefreshTokenValue

## Purpose

Represents refresh token values.

---

## Characteristics

| Characteristic           | Description       |
| ------------------------ | ----------------- |
| Cryptographically secure | Entropy           |
| Long random value        | Replay resistance |
| Stored hashed            | Secret protection |

---

## Behaviors

| Behavior         | Description     |
| ---------------- | --------------- |
| hash()           | Produces hash   |
| validateFormat() | Validates token |

---

# 9. SessionIdentifier

## Purpose

Represents secure session identifiers.

---

## Requirements

| Requirement     | Description          |
| --------------- | -------------------- |
| Globally unique | Collision prevention |
| Non-guessable   | Session security     |
| Immutable       | Integrity            |

---

## Behaviors

| Behavior   | Description          |
| ---------- | -------------------- |
| generate() | Generates identifier |
| validate() | Validates structure  |

---

# 10. MFAChallengeCode

## Purpose

Represents MFA verification codes.

---

## Examples

```text id="g4k8vn"
123456
845921
```

---

## Validation Rules

| Rule                    | Description        |
| ----------------------- | ------------------ |
| Numeric or alphanumeric | MFA type dependent |
| Short expiration        | Security           |
| Single-use              | Replay prevention  |

---

## Behaviors

| Behavior   | Description           |
| ---------- | --------------------- |
| validate() | Verifies challenge    |
| expire()   | Invalidates challenge |

---

# 11. DeviceFingerprint

## Purpose

Represents trusted device identity.

---

## Components

```text id="f7v2wr"
- Browser metadata
- OS metadata
- Device characteristics
- IP heuristics
```

---

## Behaviors

| Behavior              | Description        |
| --------------------- | ------------------ |
| compare()             | Detects similarity |
| generateFingerprint() | Builds identifier  |

---

## Usage

Supports:

* Trusted devices
* Risk analysis
* Adaptive authentication

---

# 12. TenantAuthenticationContext

## Purpose

Represents tenant-aware authentication context.

Critical for SaaS isolation.

---

## Included Data

```text id="j1x6tp"
- Tenant ID
- Tenant policies
- MFA requirements
- Session policies
```

---

## Behaviors

| Behavior         | Description        |
| ---------------- | ------------------ |
| validateTenant() | Enforces isolation |
| requiresMFA()    | Checks policy      |

---

# 13. AuthenticationResult

## Purpose

Represents authentication outcome.

---

## Possible Results

```text id="u5w9rx"
SUCCESS
FAILURE
MFA_REQUIRED
ACCOUNT_LOCKED
PASSWORD_EXPIRED
```

---

## Behaviors

| Behavior                       | Description    |
| ------------------------------ | -------------- |
| isSuccessful()                 | Checks success |
| requiresAdditionalValidation() | MFA detection  |

---

# 14. TokenExpiration

## Purpose

Represents expiration policies.

---

## Examples

```text id="x8n3vq"
5 minutes
15 minutes
7 days
30 days
```

---

## Behaviors

| Behavior        | Description                   |
| --------------- | ----------------------------- |
| isExpired()     | Validates expiration          |
| remainingTime() | Calculates remaining duration |

---

# 15. AuthenticationMethod

## Purpose

Represents authentication type.

---

## Supported Values

```text id="m2v7wr"
PASSWORD
JWT
MFA
OAUTH2
OIDC
API_KEY
SERVICE_TOKEN
```

---

## Behaviors

| Behavior        | Description              |
| --------------- | ------------------------ |
| requiresMFA()   | Determines MFA necessity |
| isInteractive() | Detects user interaction |

---

# 16. IPAddress

## Purpose

Represents validated network origins.

---

## Validation Rules

| Rule                          | Description |
| ----------------------------- | ----------- |
| IPv4/IPv6 validation          | Correctness |
| Reserved ranges detection     | Security    |
| Private/public classification | Analysis    |

---

## Behaviors

| Behavior     | Description         |
| ------------ | ------------------- |
| isPrivate()  | Detects private IP  |
| isLoopback() | Detects loopback    |
| validate()   | Validates structure |

---

# 17. UserAgent

## Purpose

Represents client metadata.

---

## Included Information

```text id="k6x1vp"
- Browser
- OS
- Device type
- Client application
```

---

## Usage

Supports:

* Device trust
* Security analytics
* Session monitoring

---

# 18. OAuthProvider

## Purpose

Represents external identity providers.

---

## Supported Providers

```text id="r4n9wt"
GOOGLE
MICROSOFT
OKTA
AUTH0
KEYCLOAK
```

---

## Behaviors

| Behavior           | Description                |
| ------------------ | -------------------------- |
| validateProvider() | Ensures supported provider |

---

# 19. SecurityRiskLevel

## Purpose

Represents authentication risk classification.

---

## Levels

```text id="p8w3vx"
LOW
MEDIUM
HIGH
CRITICAL
```

---

## Usage

Supports:

* Adaptive authentication
* Step-up MFA
* Threat analysis

---

## Behaviors

| Behavior                         | Description               |
| -------------------------------- | ------------------------- |
| requiresAdditionalVerification() | Determines MFA escalation |

---

# 20. AuthenticationFailureReason

## Purpose

Represents authentication denial explanations.

---

## Examples

```text id="z7m2tr"
INVALID_CREDENTIALS
ACCOUNT_LOCKED
TOKEN_EXPIRED
MFA_FAILED
TENANT_MISMATCH
```

---

## Behaviors

| Behavior              | Description                    |
| --------------------- | ------------------------------ |
| isSecuritySensitive() | Determines logging sensitivity |

---

# 21. Equality Rules

All Value Objects compare by value.

---

## Example

```text id="h5v8pk"
EmailAddress("john@example.com")
==
EmailAddress("john@example.com")
```

---

# 22. Immutability Requirements

All Value Objects must be:

* Immutable
* Thread-safe
* Serialization-safe
* Side-effect free

---

# 23. Serialization Considerations

Value Objects must support:

* JSON serialization
* JWT embedding
* Event publishing
* Redis caching
* Reactive pipelines

---

# 24. Security-Critical Rules

## Secret Protection

The following must never be exposed:

```text id="y9k4wx"
- PlainPassword
- Raw refresh tokens
- API secrets
- MFA secrets
```

---

## Fail Closed Principle

Validation failures:

```text id="n3x7vp"
AUTHENTICATION = DENIED
```

---

## Strong Validation

Malformed authentication values must never exist in memory.

---

# 25. Validation Strategy

Validation occurs:

| Stage           | Responsibility        |
| --------------- | --------------------- |
| Constructor     | Structural validation |
| Factory methods | Controlled creation   |
| Domain services | Complex validation    |

---

# 26. Future Value Object Extensions

Future Value Objects may include:

* WebAuthnCredentialId
* BiometricSignature
* RiskScore
* DeviceTrustScore
* GeoLocationRestriction
* PasswordlessChallengeToken
* HardwareKeyIdentifier

---

# 27. Summary

The Authentication Management Value Objects provide:

* Strong domain expressiveness
* Immutable authentication modeling
* Secure credential handling
* Multi-tenant authentication safety
* Consistent validation logic
* Enterprise-grade security foundations

These Value Objects are critical to maintaining authentication correctness and security consistency across the platform.

```
```
