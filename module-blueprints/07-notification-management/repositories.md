# 05-authentication-management/repositories.md

````md id="m8v2xp"
# Authentication Management Repositories

## 1. Introduction

This document defines the repository contracts and persistence responsibilities of the Authentication Management module.

Repositories are responsible for:

- Authentication persistence
- Session lifecycle storage
- Token management
- MFA persistence
- Device trust persistence
- Authentication auditing
- Security event storage
- Distributed authentication state management

The repository layer is designed following:

- Domain-Driven Design (DDD)
- Repository Pattern
- Hexagonal Architecture
- Multi-tenant SaaS security principles
- Zero Trust authentication architecture

---

# 2. Repository Design Principles

| Principle | Description |
|---|---|
| Aggregate-oriented | One repository per aggregate root |
| Tenant-aware | Tenant isolation mandatory |
| Persistence ignorance | Domain isolation |
| Security-first | Authentication integrity |
| Explicit querying | Avoid ambiguous access |
| Immutable auditing | Preserve security evidence |
| CQRS-friendly | Optimized read projections |

---

# 3. Repository Overview

| Repository | Responsibility |
|---|---|
| AuthenticationRepository | Authentication persistence |
| SessionRepository | Session lifecycle management |
| RefreshTokenRepository | Refresh token persistence |
| MFAChallengeRepository | MFA workflow persistence |
| TrustedDeviceRepository | Device trust management |
| APIKeyRepository | API authentication persistence |
| ServiceIdentityRepository | Internal service authentication |
| AuthenticationAuditRepository | Authentication evidence |
| LoginAttemptRepository | Failed/suspicious login tracking |
| SessionSecurityStateRepository | Runtime security state |
| OAuthIdentityRepository | External identity linkage |
| PasswordCredentialRepository | Password credential persistence |
| AuthenticationCacheRepository | Distributed auth caching |
| EffectiveSessionProjectionRepository | Session read optimization |

---

# 4. AuthenticationRepository

## Purpose

Persists authentication attempts and results.

---

## Responsibilities

- Store authentication outcomes
- Query authentication history
- Support authentication analytics
- Track authentication methods

---

## Example Contract

```java id="x3n8vt"
public interface AuthenticationRepository {

    Mono<Authentication> save(
        Authentication authentication
    );

    Mono<Authentication> findById(
        AuthenticationId authenticationId
    );

    Flux<Authentication> findByUserId(
        UserId userId
    );

    Flux<Authentication> findFailedAttempts(
        TenantId tenantId
    );
}
````

---

## Security Rules

| Rule                        | Description  |
| --------------------------- | ------------ |
| Tenant filtering mandatory  | Isolation    |
| Sensitive data forbidden    | Security     |
| Immutable history preferred | Auditability |

---

# 5. SessionRepository

## Purpose

Manages authenticated sessions.

Critical repository.

---

## Responsibilities

* Persist sessions
* Validate active sessions
* Revoke sessions
* Query user sessions
* Support distributed session management

---

## Example Contract

```java id="f7w2xr"
public interface SessionRepository {

    Mono<AuthenticatedSession> save(
        AuthenticatedSession session
    );

    Mono<AuthenticatedSession> findById(
        SessionId sessionId
    );

    Flux<AuthenticatedSession> findActiveByUser(
        UserId userId,
        TenantId tenantId
    );

    Mono<Void> revoke(
        SessionId sessionId
    );
}
```

---

## Important Constraints

| Constraint                 | Description   |
| -------------------------- | ------------- |
| Revoked sessions unusable  | Security      |
| Expired sessions invalid   | Integrity     |
| Tenant isolation mandatory | SaaS security |

---

# 6. RefreshTokenRepository

## Purpose

Persists refresh token lifecycle.

Critical for replay protection.

---

## Responsibilities

* Store refresh tokens
* Rotate refresh tokens
* Detect replay attempts
* Revoke compromised tokens

---

## Example Contract

```java id="u9m4wp"
public interface RefreshTokenRepository {

    Mono<RefreshToken> save(
        RefreshToken refreshToken
    );

    Mono<RefreshToken> findByHash(
        String tokenHash
    );

    Mono<Void> revoke(
        RefreshTokenId tokenId
    );

    Flux<RefreshToken> findBySession(
        SessionId sessionId
    );
}
```

---

## Security Rules

| Rule                              | Description       |
| --------------------------------- | ----------------- |
| Tokens stored hashed              | Secret protection |
| Replay detection mandatory        | Threat prevention |
| Rotation chain integrity required | Security          |

---

# 7. MFAChallengeRepository

## Purpose

Manages MFA challenges.

---

## Responsibilities

* Persist MFA challenges
* Validate active challenges
* Expire challenges
* Track invalid attempts

---

## Example Contract

```java id="q5v8xt"
public interface MFAChallengeRepository {

    Mono<MFAChallenge> save(
        MFAChallenge challenge
    );

    Mono<MFAChallenge> findActiveChallenge(
        UserId userId
    );

    Mono<Void> expire(
        MFAChallengeId challengeId
    );
}
```

---

## Important Rules

* Expired challenges invalid
* Single-use challenges only
* Brute force tracking required

---

# 8. TrustedDeviceRepository

## Purpose

Persists trusted device relationships.

---

## Responsibilities

* Store trusted devices
* Validate trust state
* Revoke trusted devices
* Support adaptive authentication

---

## Example Contract

```java id="r2n7vp"
public interface TrustedDeviceRepository {

    Mono<TrustedDevice> save(
        TrustedDevice device
    );

    Mono<TrustedDevice> findByFingerprint(
        DeviceFingerprint fingerprint
    );

    Flux<TrustedDevice> findByUser(
        UserId userId
    );

    Mono<Void> revoke(
        DeviceTrustId deviceTrustId
    );
}
```

---

# 9. APIKeyRepository

## Purpose

Persists API authentication credentials.

---

## Responsibilities

* Store API keys
* Validate scopes
* Revoke keys
* Rotate secrets

---

## Example Contract

```java id="v8x3wr"
public interface APIKeyRepository {

    Mono<APIKey> save(
        APIKey apiKey
    );

    Mono<APIKey> findByPrefix(
        String prefix
    );

    Mono<Void> revoke(
        APIKeyId apiKeyId
    );
}
```

---

## Security Rules

* Secrets stored hashed
* Revocation support mandatory
* Expiration enforcement required

---

# 10. ServiceIdentityRepository

## Purpose

Persists internal service authentication identities.

---

## Responsibilities

* Store service credentials
* Validate internal identities
* Rotate secrets
* Support Zero Trust service validation

---

## Example Contract

```java id="g1k6xt"
public interface ServiceIdentityRepository {

    Mono<ServiceIdentity> save(
        ServiceIdentity identity
    );

    Mono<ServiceIdentity> findByClientId(
        String clientId
    );
}
```

---

# 11. AuthenticationAuditRepository

## Purpose

Stores immutable authentication evidence.

Critical compliance repository.

---

## Responsibilities

* Persist audit records
* Support security analytics
* Support forensic investigation
* Provide compliance exports

---

## Example Contract

```java id="w4n9vp"
public interface AuthenticationAuditRepository {

    Mono<AuthenticationAuditRecord> save(
        AuthenticationAuditRecord record
    );

    Flux<AuthenticationAuditRecord> search(
        AuthenticationAuditCriteria criteria
    );
}
```

---

## Characteristics

| Characteristic           | Description            |
| ------------------------ | ---------------------- |
| Append-only preferred    | Immutable evidence     |
| High-volume writes       | Authentication traffic |
| Partitioning recommended | Scalability            |

---

# 12. LoginAttemptRepository

## Purpose

Tracks authentication attempts.

Supports:

* Brute force protection
* Threat analysis
* Lockout workflows

---

## Example Contract

```java id="t7m2wr"
public interface LoginAttemptRepository {

    Mono<LoginAttempt> save(
        LoginAttempt attempt
    );

    Flux<LoginAttempt> findRecentFailures(
        Username username
    );

    Mono<Long> countRecentFailures(
        Username username
    );
}
```

---

# 13. SessionSecurityStateRepository

## Purpose

Persists runtime security evaluation state.

---

## Responsibilities

* Risk scoring
* Suspicious session tracking
* Adaptive authentication support

---

## Example Contract

```java id="k5x8vt"
public interface SessionSecurityStateRepository {

    Mono<SessionSecurityState> save(
        SessionSecurityState state
    );

    Mono<SessionSecurityState> findBySession(
        SessionId sessionId
    );
}
```

---

# 14. OAuthIdentityRepository

## Purpose

Persists external identity provider linkages.

---

## Responsibilities

* Link provider identities
* Resolve external users
* Manage federation mappings

---

## Example Contract

```java id="d3v7xp"
public interface OAuthIdentityRepository {

    Mono<OAuthIdentity> save(
        OAuthIdentity identity
    );

    Mono<OAuthIdentity> findByProviderAndExternalId(
        OAuthProvider provider,
        String externalId
    );
}
```

---

# 15. PasswordCredentialRepository

## Purpose

Persists password credentials securely.

---

## Responsibilities

* Store password hashes
* Rotate password credentials
* Validate expiration policies

---

## Example Contract

```java id="j9n4wr"
public interface PasswordCredentialRepository {

    Mono<PasswordCredential> save(
        PasswordCredential credential
    );

    Mono<PasswordCredential> findByUser(
        UserId userId
    );
}
```

---

## Security Rules

| Rule                      | Description |
| ------------------------- | ----------- |
| Plaintext forbidden       | Security    |
| Strong hashing mandatory  | Hardening   |
| Rotation support required | Compliance  |

---

# 16. AuthenticationCacheRepository

## Purpose

Provides distributed authentication caching.

---

## Cached Elements

```text id="m2x7vt"
- Active sessions
- JWT revocation state
- Device trust state
- MFA validation state
```

---

## Example Contract

```java id="f6w1xp"
public interface AuthenticationCacheRepository {

    Mono<AuthenticatedSession> getSession(
        SessionId sessionId
    );

    Mono<Void> putSession(
        AuthenticatedSession session
    );

    Mono<Void> invalidateSession(
        SessionId sessionId
    );
}
```

---

## Recommended Technologies

| Technology | Suitability                      |
| ---------- | -------------------------------- |
| Redis      | Distributed authentication state |
| Caffeine   | Local cache                      |
| Hazelcast  | Clustered caching                |

---

# 17. EffectiveSessionProjectionRepository

## Purpose

Provides optimized session read models.

CQRS-oriented repository.

---

## Responsibilities

* Active session views
* User device views
* Security dashboards
* Session analytics

---

## Example Contract

```java id="q8v3wr"
public interface EffectiveSessionProjectionRepository {

    Flux<SessionProjection> findActiveSessions(
        UserId userId,
        TenantId tenantId
    );
}
```

---

# 18. Multi-Tenant Repository Rules

## Mandatory Tenant Isolation

Repositories must enforce:

```sql id="y4k9vp"
WHERE tenant_id = :tenantId
```

---

## Forbidden Behavior

```text id="u7m2xt"
Cross-tenant authentication access
```

---

# 19. Persistence Strategies

| Aggregate                    | Strategy                   |
| ---------------------------- | -------------------------- |
| AuthenticationAggregate      | Relational persistence     |
| SessionAggregate             | Relational + Redis         |
| RefreshTokenAggregate        | Relational secure storage  |
| MFAAggregate                 | Short-lived persistence    |
| AuthenticationAuditAggregate | Append-only/event-oriented |

---

# 20. Recommended Database Technologies

| Technology    | Use Case                       |
| ------------- | ------------------------------ |
| PostgreSQL    | Core authentication data       |
| Redis         | Session/token caching          |
| Elasticsearch | Security audit search          |
| Kafka         | Authentication event streaming |

---

# 21. CQRS Considerations

Recommended separation:

## Write Side

* Authentication consistency
* Session integrity
* Security enforcement

---

## Read Side

* Security dashboards
* Session projections
* Audit analytics

---

# 22. Reactive Repository Considerations

Reactive support strongly recommended.

---

## Example

```java id="r1x6wt"
Mono<AuthenticatedSession>
Flux<AuthenticationAuditRecord>
```

---

## Benefits

| Benefit                  | Description             |
| ------------------------ | ----------------------- |
| Non-blocking IO          | Scalability             |
| High concurrency         | Reactive authentication |
| Efficient resource usage | Performance             |

---

# 23. Transaction Management

## Strong Consistency Required

| Operation              | Reason                   |
| ---------------------- | ------------------------ |
| Session creation       | Authentication integrity |
| Refresh token rotation | Replay prevention        |
| MFA validation         | Security correctness     |
| Session revocation     | Immediate invalidation   |

---

## Eventual Consistency Acceptable

| Operation             | Reason              |
| --------------------- | ------------------- |
| Analytics projections | Reporting           |
| Security dashboards   | Monitoring          |
| Audit indexing        | Search optimization |

---

# 24. Security-Critical Repository Rules

## Secrets Never Retrievable

Forbidden retrieval:

```text id="x5n8vr"
- Plain passwords
- Raw refresh tokens
- API secrets
- MFA secrets
```

---

## Fail Closed Principle

Repository failures:

```text id="p3v7wt"
AUTHENTICATION = DENIED
```

---

## Immutable Audit Evidence

Audit records must not be mutable.

---

# 25. Performance Considerations

Critical performance areas:

| Area                 | Optimization   |
| -------------------- | -------------- |
| Session validation   | Redis          |
| JWT revocation       | Cache indexing |
| Refresh token lookup | Hashed indexes |
| Audit search         | Partitioning   |

---

# 26. Indexing Recommendations

| Table                | Recommended Index       |
| -------------------- | ----------------------- |
| sessions             | tenant_id + user_id     |
| refresh_tokens       | token_hash              |
| login_attempts       | username + attempted_at |
| authentication_audit | tenant_id + occurred_at |

---

# 27. Soft Delete Strategy

Recommended for:

| Entity          | Reason              |
| --------------- | ------------------- |
| Sessions        | Auditability        |
| API keys        | Historical evidence |
| Trusted devices | Security forensics  |

---

## Example

```text id="n6k2xp"
revoked = true
```

instead of physical deletion.

---

# 28. Failure Handling Strategies

| Failure                  | Strategy            |
| ------------------------ | ------------------- |
| Redis unavailable        | Fallback validation |
| DB timeout               | Fail closed         |
| Token replay uncertainty | Revoke session      |

---

# 29. Distributed System Considerations

Repositories must support:

* Horizontal scaling
* Distributed session state
* Eventual consistency
* Multi-region deployments
* Distributed cache synchronization

---

# 30. Future Repository Extensions

Future repositories may include:

* WebAuthnRepository
* BiometricCredentialRepository
* AdaptiveAuthenticationRepository
* RiskEvaluationRepository
* PasswordlessAuthenticationRepository

---

# 31. Summary

The Authentication Management repositories provide:

* Secure authentication persistence
* Strong session integrity
* Replay-resistant token management
* Distributed authentication scalability
* Reactive authentication support
* Immutable security auditability
* Enterprise-grade authentication consistency

These repositories form the persistence backbone of the authentication ecosystem.

```
```
