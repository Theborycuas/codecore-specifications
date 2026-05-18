# 07-user-management/aggregates.md

````md id="v7x3wp"
# User Management Aggregates

## 1. Introduction

This document defines the aggregates of the User Management module.

Aggregates represent the transactional consistency boundaries of the user domain and encapsulate:

- User lifecycle management
- Multi-tenant memberships
- User profile consistency
- Organizational relationships
- Onboarding workflows
- User preferences
- Visibility constraints

The aggregates are designed following:

- Domain-Driven Design (DDD)
- Hexagonal Architecture
- Multi-tenant SaaS principles
- Reactive system design
- Enterprise user management standards

---

# 2. Aggregate Overview

| Aggregate | Responsibility |
|---|---|
| UserAggregate | Core user lifecycle |
| UserProfileAggregate | Personal/business profile management |
| TenantMembershipAggregate | Tenant association lifecycle |
| UserInvitationAggregate | Invitation workflows |
| UserPreferenceAggregate | Personalization management |
| OrganizationalAssignmentAggregate | Organizational relationships |
| UserVisibilityAggregate | Tenant-aware visibility |
| UserOnboardingAggregate | Onboarding progression |
| UserStateAggregate | Business state transitions |

---

# 3. UserAggregate

## Purpose

Represents the central business identity of a platform user.

This is the primary aggregate of the module.

---

## Aggregate Root

```text id="m4v8wr"
User
````

---

## Responsibilities

* Manage user lifecycle
* Maintain business identity
* Coordinate memberships
* Enforce user invariants
* Manage user states
* Coordinate onboarding

---

## Invariants

| Invariant                   | Description           |
| --------------------------- | --------------------- |
| User identity immutable     | Domain consistency    |
| Tenant visibility enforced  | SaaS isolation        |
| User states valid           | Lifecycle correctness |
| Email uniqueness controlled | Identity integrity    |
| Soft deletion preferred     | Auditability          |

---

## Example Structure

```text id="p9x2vt"
UserAggregate
│
├── User (Root)
├── UserState
├── UserProfileReference
├── MembershipReferences
└── OnboardingState
```

---

## Important Behaviors

### activate()

Activates operational usage.

---

### suspend()

Temporarily disables business operations.

---

### deactivate()

Deactivates business account.

---

### assignMembership()

Associates user with tenant.

---

# 4. UserProfileAggregate

## Purpose

Represents user profile and personal/business information.

---

## Aggregate Root

```text id="u5m1xp"
UserProfile
```

---

## Responsibilities

* Manage personal profile
* Maintain user metadata
* Handle profile preferences
* Maintain localization settings

---

## Example Profile Data

```text id="f8v3wr"
- First name
- Last name
- Avatar
- Timezone
- Language
```

---

## Invariants

| Invariant                   | Description          |
| --------------------------- | -------------------- |
| Profile ownership immutable | Integrity            |
| Timezone valid              | Localization         |
| Language valid              | Internationalization |

---

## Example Structure

```text id="k2x9wt"
UserProfileAggregate
│
├── UserProfile (Root)
├── PersonalInformation
├── LocalizationSettings
└── ProfileMetadata
```

---

# 5. TenantMembershipAggregate

## Purpose

Represents user participation inside tenants.

Critical for enterprise SaaS.

---

## Aggregate Root

```text id="g7m4vp"
TenantMembership
```

---

## Responsibilities

* Manage tenant associations
* Enforce tenant isolation
* Track membership lifecycle
* Manage membership states

---

## Membership Types

| Membership       | Example      |
| ---------------- | ------------ |
| Clinical         | Psychologist |
| Administrative   | Receptionist |
| Tenant Admin     | Owner        |
| Platform Support | SaaS support |

---

## Invariants

| Invariant                  | Description        |
| -------------------------- | ------------------ |
| Membership tenant-scoped   | Isolation          |
| Membership uniqueness      | Prevent duplicates |
| Membership lifecycle valid | Consistency        |

---

## Example Structure

```text id="n3v8xr"
TenantMembershipAggregate
│
├── TenantMembership (Root)
├── MembershipState
├── MembershipMetadata
└── OrganizationalReference
```

---

# 6. UserInvitationAggregate

## Purpose

Represents invitation workflows.

---

## Aggregate Root

```text id="x1m7wt"
UserInvitation
```

---

## Responsibilities

* Manage invitation lifecycle
* Support onboarding initiation
* Validate invitation expiration
* Support multi-tenant invitations

---

## Lifecycle

```text id="r6v2wp"
CREATED
SENT
ACCEPTED
EXPIRED
REVOKED
```

---

## Invariants

| Invariant                  | Description |
| -------------------------- | ----------- |
| Expiration mandatory       | Security    |
| Single acceptance only     | Consistency |
| Invitation token immutable | Integrity   |

---

## Example Structure

```text id="d8x4vr"
UserInvitationAggregate
│
├── UserInvitation (Root)
├── InvitationToken
├── InvitationStatus
└── InvitationMetadata
```

---

# 7. UserPreferenceAggregate

## Purpose

Represents user personalization settings.

---

## Aggregate Root

```text id="t4m9xp"
UserPreferences
```

---

## Responsibilities

* Manage personalization
* Manage UI preferences
* Manage notification preferences
* Maintain localization settings

---

## Example Preferences

```text id="c5v1wr"
- Theme
- Language
- Notification preferences
- Dashboard configuration
```

---

## Invariants

| Invariant                         | Description      |
| --------------------------------- | ---------------- |
| Preferences tenant-aware optional | SaaS flexibility |
| Invalid preferences rejected      | Consistency      |

---

# 8. OrganizationalAssignmentAggregate

## Purpose

Represents organizational structure assignments.

---

## Aggregate Root

```text id="w9x3vt"
OrganizationalAssignment
```

---

## Responsibilities

* Assign departments
* Assign clinics
* Manage team relationships
* Support hierarchical visibility

---

## Example Structures

```text id="u2m8wr"
- Department
- Clinic
- Team
- Branch
```

---

## Invariants

| Invariant                      | Description |
| ------------------------------ | ----------- |
| Organizational hierarchy valid | Integrity   |
| Assignment tenant-scoped       | Isolation   |

---

# 9. UserVisibilityAggregate

## Purpose

Controls tenant-aware visibility.

---

## Aggregate Root

```text id="q7v1xp"
UserVisibilityRule
```

---

## Responsibilities

* Enforce visibility constraints
* Restrict enumeration
* Control discoverability
* Support organizational filtering

---

## Example Rules

```text id="f4m8wt"
- Same tenant visibility
- Department visibility
- Admin-only visibility
```

---

## Critical Rule

```text id="j8x2vr"
Cross-tenant user enumeration forbidden
```

---

# 10. UserOnboardingAggregate

## Purpose

Represents onboarding progression.

---

## Aggregate Root

```text id="y6v4wp"
UserOnboarding
```

---

## Responsibilities

* Track onboarding stages
* Coordinate setup flows
* Validate onboarding completion

---

## Example Stages

```text id="k1m9xt"
- Invitation accepted
- Profile completed
- Membership assigned
- Preferences configured
```

---

## Invariants

| Invariant                | Description           |
| ------------------------ | --------------------- |
| Ordered progression      | Workflow consistency  |
| Required stages enforced | Operational readiness |

---

# 11. UserStateAggregate

## Purpose

Manages business state transitions.

---

## Aggregate Root

```text id="g5x8wr"
UserState
```

---

## Responsibilities

* Enforce state transitions
* Coordinate lifecycle rules
* Preserve business consistency

---

## Example States

```text id="r2v7xp"
ACTIVE
SUSPENDED
DEACTIVATED
LOCKED
```

---

## Allowed Transitions

```text id="v9m1wt"
INVITED
    → ACTIVE

ACTIVE
    → SUSPENDED

SUSPENDED
    → ACTIVE

ACTIVE
    → DEACTIVATED
```

---

## Forbidden Transitions

```text id="d3x4vr"
DELETED
    → ACTIVE
```

---

# 12. Aggregate Relationships

```text id="m7v2xp"
UserAggregate
    ├── references -> UserProfileAggregate
    ├── owns -> TenantMembershipAggregate
    ├── coordinates -> UserOnboardingAggregate
    ├── constrained by -> UserVisibilityAggregate
    └── governed by -> UserStateAggregate
```

---

# 13. Aggregate Transaction Boundaries

## Strong Consistency Required

| Aggregate                 | Reason                |
| ------------------------- | --------------------- |
| UserAggregate             | Identity integrity    |
| TenantMembershipAggregate | Tenant isolation      |
| UserStateAggregate        | Lifecycle consistency |

---

## Eventual Consistency Acceptable

| Aggregate                         | Reason            |
| --------------------------------- | ----------------- |
| UserPreferenceAggregate           | Personalization   |
| OrganizationalAssignmentAggregate | Hierarchy updates |

---

# 14. Event Sourcing Compatibility

The domain is compatible with:

* Event sourcing
* User lifecycle replay
* Organizational reconstruction
* Membership history reconstruction

---

# 15. Security-Critical Aggregate Rules

## Tenant Isolation Mandatory

All membership operations are tenant-scoped.

---

## Soft Delete Preferred

Preferred strategy:

```text id="u8x3wp"
DEACTIVATED
instead of hard deletion
```

---

## Sensitive Data Restrictions

Aggregates must never expose:

```text id="f6m9wr"
- Password hashes
- JWTs
- MFA secrets
```

---

# 16. Distributed System Considerations

Aggregates support:

* Horizontal scaling
* Distributed onboarding
* Event-driven workflows
* Multi-region deployments

---

# 17. Reactive Considerations

Reactive implementations should support:

```text id="t1v4xt"
Flux<User>
Mono<UserProfile>
```

---

## Requirements

* Non-blocking operations
* Async onboarding
* Reactive event propagation

---

# 18. CQRS Considerations

Recommended read models:

| Projection               | Purpose             |
| ------------------------ | ------------------- |
| UserDirectoryProjection  | Search              |
| MembershipProjection     | Tenant associations |
| UserProfileProjection    | Profile reads       |
| OrganizationalProjection | Hierarchical views  |

---

# 19. Future Aggregate Extensions

Future aggregates may include:

* ProfessionalLicenseAggregate
* ClinicalIdentityAggregate
* ExternalIdentityAggregate
* DelegatedAdministrationAggregate
* CrossOrganizationCollaborationAggregate

---

# 20. Summary

The User Management aggregates provide:

* Enterprise-grade user lifecycle management
* Multi-tenant membership consistency
* Organizational user modeling
* Reactive onboarding orchestration
* Tenant-aware visibility enforcement
* SaaS-ready identity abstraction
* Distributed user scalability

These aggregates form the transactional backbone of the user ecosystem.

```
```
