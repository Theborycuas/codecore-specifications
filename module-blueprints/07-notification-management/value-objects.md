# 07-user-management/value-objects.md

````md id="n8x4vp"
# User Management Value Objects

## 1. Introduction

This document defines the Value Objects used in the User Management module.

Value Objects represent immutable conceptual elements that:

- Have no identity
- Are compared by value
- Encapsulate validation rules
- Improve domain expressiveness
- Protect lifecycle consistency
- Enforce tenant-safe user modeling

The Value Objects are designed following:

- Domain-Driven Design (DDD)
- Immutable modeling principles
- Multi-tenant SaaS architecture
- Enterprise identity management standards
- Compliance-aware user modeling

---

# 2. Value Object Overview

| Value Object | Purpose |
|---|---|
| UserEmail | Represents validated user email |
| UserDisplayName | Represents visible display name |
| UserFullName | Represents legal/business name |
| UserStateType | Represents business lifecycle state |
| MembershipType | Represents tenant association type |
| MembershipStatus | Represents membership lifecycle |
| InvitationStatus | Represents invitation lifecycle |
| InvitationToken | Represents secure invitation token |
| LocalizationSettings | Represents locale preferences |
| NotificationPreferences | Represents communication settings |
| UserTimezone | Represents timezone configuration |
| UserLanguage | Represents language preference |
| UserTheme | Represents UI personalization |
| OrganizationalPath | Represents hierarchy structure |
| VisibilityScope | Represents user visibility rules |
| UserTagLabel | Represents categorization labels |
| AvatarReference | Represents avatar storage metadata |
| OnboardingStage | Represents onboarding progression |
| ContactPhoneNumber | Represents normalized phone |
| UserBiography | Represents profile biography |

---

# 3. UserEmail

## Purpose

Represents validated user email identity.

---

## Example

```text id="u5m1wr"
john.doe@company.com
````

---

## Validation Rules

| Rule                                    | Description |
| --------------------------------------- | ----------- |
| Valid email format                      | Mandatory   |
| Lowercase normalization                 | Recommended |
| Length limits                           | Security    |
| Disposable domains optional restriction | Anti-abuse  |

---

## Behaviors

| Behavior    | Description           |
| ----------- | --------------------- |
| normalize() | Standardizes casing   |
| domain()    | Extracts email domain |

---

# 4. UserDisplayName

## Purpose

Represents public-facing display identity.

---

## Validation Rules

| Rule                        | Description |
| --------------------------- | ----------- |
| Non-empty                   | Mandatory   |
| Offensive content filtering | Recommended |
| Length restrictions         | UI safety   |

---

## Behaviors

| Behavior   | Description            |
| ---------- | ---------------------- |
| sanitize() | Removes unsafe content |

---

# 5. UserFullName

## Purpose

Represents legal/business identity.

---

## Components

```text id="f7v3xp"
- First name
- Middle name
- Last name
```

---

## Behaviors

| Behavior   | Description            |
| ---------- | ---------------------- |
| fullName() | Returns formatted name |
| initials() | Generates initials     |

---

# 6. UserStateType

## Purpose

Represents business lifecycle state.

---

## Supported States

```text id="k2m8wr"
INVITED
PENDING_ACTIVATION
ACTIVE
SUSPENDED
LOCKED
DEACTIVATED
DELETED
```

---

## Behaviors

| Behavior        | Description      |
| --------------- | ---------------- |
| isOperational() | Active usage     |
| isSuspended()   | Restricted state |

---

## Important Clarification

Business state ≠ authentication lockout.

---

# 7. MembershipType

## Purpose

Represents tenant participation category.

---

## Examples

```text id="r9v1wt"
CLINICAL
PATIENT
ADMINISTRATIVE
TENANT_ADMIN
SUPPORT
```

---

## Usage

Supports:

* Organizational modeling
* Visibility rules
* Tenant workflows

---

# 8. MembershipStatus

## Purpose

Represents tenant membership lifecycle.

---

## Supported States

```text id="g4x7vp"
PENDING
ACTIVE
SUSPENDED
TERMINATED
```

---

## Behaviors

| Behavior    | Description           |
| ----------- | --------------------- |
| activate()  | Enables participation |
| terminate() | Ends membership       |

---

# 9. InvitationStatus

## Purpose

Represents invitation lifecycle.

---

## Supported States

```text id="w8m3xr"
CREATED
SENT
ACCEPTED
EXPIRED
REVOKED
```

---

## Behaviors

| Behavior       | Description           |
| -------------- | --------------------- |
| isExpired()    | Expiration validation |
| isAcceptable() | Acceptance validation |

---

# 10. InvitationToken

## Purpose

Represents secure invitation tokens.

---

## Security Rules

| Rule                     | Description |
| ------------------------ | ----------- |
| Cryptographically secure | Mandatory   |
| Immutable                | Integrity   |
| Expiration-aware         | Security    |

---

## Behaviors

| Behavior             | Description         |
| -------------------- | ------------------- |
| validateExpiration() | Security validation |

---

# 11. LocalizationSettings

## Purpose

Represents user localization configuration.

---

## Components

```text id="m5v9wr"
- Language
- Timezone
- Date format
- Number format
```

---

## Behaviors

| Behavior              | Description          |
| --------------------- | -------------------- |
| resolveLocale()       | Produces locale      |
| applyRegionalFormat() | Localization support |

---

# 12. NotificationPreferences

## Purpose

Represents communication settings.

---

## Examples

```text id="d1x6vt"
- Email notifications
- Push notifications
- SMS preferences
```

---

## Behaviors

| Behavior                   | Description             |
| -------------------------- | ----------------------- |
| enableEmailNotifications() | Activates email alerts  |
| disablePushNotifications() | Deactivates push alerts |

---

# 13. UserTimezone

## Purpose

Represents timezone configuration.

---

## Examples

```text id="t7m2xp"
America/Guayaquil
UTC
Europe/Madrid
```

---

## Validation Rules

| Rule                | Description |
| ------------------- | ----------- |
| Valid IANA timezone | Mandatory   |

---

## Behaviors

| Behavior   | Description              |
| ---------- | ------------------------ |
| toZoneId() | Converts to runtime zone |

---

# 14. UserLanguage

## Purpose

Represents preferred language.

---

## Examples

```text id="p4v8wr"
es-EC
en-US
fr-FR
```

---

## Validation Rules

| Rule                    | Description |
| ----------------------- | ----------- |
| ISO language compliance | Recommended |

---

# 15. UserTheme

## Purpose

Represents UI personalization theme.

---

## Examples

```text id="y9m1vt"
LIGHT
DARK
SYSTEM
```

---

## Behaviors

| Behavior       | Description          |
| -------------- | -------------------- |
| resolveTheme() | UI rendering support |

---

# 16. OrganizationalPath

## Purpose

Represents organizational hierarchy.

---

## Example

```text id="f2x7wr"
organization/clinic/department/team
```

---

## Usage

Supports:

* Hierarchical visibility
* Organizational filtering
* Department-scoped access

---

# 17. VisibilityScope

## Purpose

Represents user visibility constraints.

---

## Examples

```text id="u8m4xp"
TENANT
DEPARTMENT
TEAM
SELF
```

---

## Behaviors

| Behavior           | Description       |
| ------------------ | ----------------- |
| allowsVisibility() | Access validation |

---

## Critical Rule

```text id="n3v9wt"
Cross-tenant visibility forbidden
```

---

# 18. UserTagLabel

## Purpose

Represents user categorization labels.

---

## Examples

```text id="g5x2vr"
VIP_PATIENT
SENIOR_PSYCHOLOGIST
CONTRACTOR
```

---

## Validation Rules

| Rule                            | Description     |
| ------------------------------- | --------------- |
| Controlled vocabulary preferred | Standardization |

---

# 19. AvatarReference

## Purpose

Represents avatar storage metadata.

---

## Components

```text id="k7m1wp"
- Storage path
- CDN URL
- File checksum
```

---

## Behaviors

| Behavior           | Description          |
| ------------------ | -------------------- |
| resolvePublicUrl() | Retrieves avatar URL |

---

# 20. OnboardingStage

## Purpose

Represents onboarding progression.

---

## Supported Stages

```text id="x4v8xt"
INVITATION_ACCEPTED
PROFILE_COMPLETED
MEMBERSHIP_ASSIGNED
PREFERENCES_CONFIGURED
COMPLETED
```

---

## Behaviors

| Behavior     | Description           |
| ------------ | --------------------- |
| nextStage()  | Advances workflow     |
| isComplete() | Completion validation |

---

# 21. ContactPhoneNumber

## Purpose

Represents normalized contact phone.

---

## Validation Rules

| Rule                        | Description      |
| --------------------------- | ---------------- |
| International normalization | Recommended      |
| Country validation optional | Regional support |

---

## Behaviors

| Behavior    | Description         |
| ----------- | ------------------- |
| normalize() | Standardizes format |

---

# 22. UserBiography

## Purpose

Represents optional user biography.

---

## Validation Rules

| Rule              | Description      |
| ----------------- | ---------------- |
| Length limits     | Abuse prevention |
| HTML sanitization | Security         |

---

## Behaviors

| Behavior   | Description            |
| ---------- | ---------------------- |
| sanitize() | Removes unsafe content |

---

# 23. Equality Rules

All Value Objects compare by value.

---

## Example

```text id="r1m7vp"
UserLanguage("es-EC")
==
UserLanguage("es-EC")
```

---

# 24. Immutability Requirements

All Value Objects must be:

* Immutable
* Thread-safe
* Serialization-safe
* Side-effect free

---

# 25. Serialization Considerations

Value Objects must support:

* JSON serialization
* Kafka event streaming
* Reactive pipelines
* Elasticsearch indexing

---

# 26. Security-Critical Rules

## Sensitive Data Restrictions

User Value Objects must never expose:

```text id="w6x3wr"
- Password hashes
- MFA secrets
- JWT tokens
```

---

## Visibility Enforcement

Visibility scopes must preserve tenant isolation.

---

## Invitation Security

Invitation tokens must be cryptographically secure.

---

# 27. Validation Strategy

Validation occurs at:

| Stage           | Responsibility        |
| --------------- | --------------------- |
| Constructor     | Structural validation |
| Factory methods | Controlled creation   |
| Domain services | Advanced validation   |

---

# 28. Future Value Object Extensions

Future Value Objects may include:

* ProfessionalLicenseNumber
* ClinicalSpecialization
* FederatedIdentityReference
* DelegatedAccessScope
* UserRelationshipStrength

---

# 29. Summary

The User Management Value Objects provide:

* Immutable user modeling
* Enterprise-grade lifecycle consistency
* Tenant-safe organizational abstractions
* Reactive onboarding consistency
* Localization-aware user preferences
* SaaS-ready identity representation
* Compliance-aware visibility constraints

These Value Objects are fundamental to maintaining consistency and integrity across the user ecosystem.

```
```
