# 07-user-management/entities.md

````md id="x8v4wp"
# User Management Entities

## 1. Introduction

This document defines the entities of the User Management module.

Entities represent domain objects that:

- Possess identity
- Maintain lifecycle continuity
- Support business operations
- Enforce tenant isolation
- Coordinate onboarding
- Preserve organizational consistency

The entities are designed following:

- Domain-Driven Design (DDD)
- Multi-tenant SaaS architecture
- Enterprise user management principles
- Reactive system design
- Compliance-aware identity modeling

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| User | Core business identity |
| UserProfile | Personal/business profile |
| TenantMembership | Tenant association |
| UserInvitation | Invitation lifecycle |
| UserPreferences | Personalization settings |
| OrganizationalAssignment | Organizational hierarchy |
| UserState | Business lifecycle state |
| UserOnboarding | Onboarding progression |
| MembershipRoleAssignment | Tenant role linkage |
| UserContactInformation | Communication data |
| UserAvatar | Profile avatar |
| UserLocalization | Localization preferences |
| UserVisibilityRule | Visibility constraints |
| MembershipMetadata | Tenant membership metadata |
| UserTag | User categorization |

---

# 3. User Entity

## Purpose

Represents the core business identity of a platform participant.

---

## Identity

```text id="m7x2vt"
userId
````

---

## Attributes

| Attribute                | Type      | Description            |
| ------------------------ | --------- | ---------------------- |
| userId                   | UUID      | Unique user identifier |
| authenticationIdentityId | UUID      | Linked auth identity   |
| primaryEmail             | String    | Primary email          |
| state                    | UserState | Lifecycle state        |
| createdAt                | Instant   | Creation timestamp     |
| updatedAt                | Instant   | Last update            |

---

## Behaviors

| Behavior           | Description               |
| ------------------ | ------------------------- |
| activate()         | Activates user            |
| suspend()          | Suspends operations       |
| deactivate()       | Disables business account |
| assignMembership() | Associates tenant         |

---

## Business Rules

* User identity immutable
* Soft delete preferred
* Authentication linkage required
* Email uniqueness enforced

---

# 4. UserProfile Entity

## Purpose

Represents personal and business profile information.

---

## Identity

```text id="u5v8wr"
userProfileId
```

---

## Attributes

| Attribute     | Type   | Description          |
| ------------- | ------ | -------------------- |
| userProfileId | UUID   | Profile identifier   |
| userId        | UUID   | Linked user          |
| firstName     | String | Given name           |
| lastName      | String | Family name          |
| displayName   | String | Visible name         |
| biography     | String | Optional profile bio |

---

## Behaviors

| Behavior            | Description         |
| ------------------- | ------------------- |
| updateProfile()     | Updates profile     |
| updateDisplayName() | Changes public name |

---

## Business Rules

* Profile ownership immutable
* Display name sanitized
* Excessive profile data rejected

---

# 5. TenantMembership Entity

## Purpose

Represents user participation inside tenants.

Critical for SaaS.

---

## Identity

```text id="k3m9xp"
tenantMembershipId
```

---

## Attributes

| Attribute          | Type    | Description           |
| ------------------ | ------- | --------------------- |
| tenantMembershipId | UUID    | Membership identifier |
| userId             | UUID    | Associated user       |
| tenantId           | UUID    | Tenant association    |
| membershipType     | String  | Clinical/admin/etc    |
| membershipState    | String  | ACTIVE/SUSPENDED      |
| joinedAt           | Instant | Membership start      |

---

## Behaviors

| Behavior              | Description          |
| --------------------- | -------------------- |
| activateMembership()  | Activates membership |
| suspendMembership()   | Suspends access      |
| terminateMembership() | Ends association     |

---

## Business Rules

* Membership tenant-scoped
* Duplicate memberships forbidden
* Membership lifecycle validated

---

# 6. UserInvitation Entity

## Purpose

Represents invitation workflows.

---

## Identity

```text id="f8v1wt"
invitationId
```

---

## Attributes

| Attribute       | Type    | Description           |
| --------------- | ------- | --------------------- |
| invitationId    | UUID    | Invitation identifier |
| invitedEmail    | String  | Invited user          |
| tenantId        | UUID    | Target tenant         |
| invitationToken | String  | Secure token          |
| status          | String  | SENT/ACCEPTED/etc     |
| expiresAt       | Instant | Expiration            |

---

## Behaviors

| Behavior           | Description        |
| ------------------ | ------------------ |
| sendInvitation()   | Sends invite       |
| acceptInvitation() | Accepts invite     |
| revokeInvitation() | Revokes invitation |
| expireInvitation() | Expires invite     |

---

## Business Rules

* Expiration mandatory
* Invitation tokens immutable
* Single acceptance enforced

---

# 7. UserPreferences Entity

## Purpose

Represents personalization settings.

---

## Identity

```text id="q2x7vr"
preferencesId
```

---

## Attributes

| Attribute            | Type    | Description            |
| -------------------- | ------- | ---------------------- |
| preferencesId        | UUID    | Preferences identifier |
| userId               | UUID    | Associated user        |
| theme                | String  | UI theme               |
| language             | String  | Preferred language     |
| timezone             | String  | Preferred timezone     |
| notificationsEnabled | Boolean | Notification setting   |

---

## Behaviors

| Behavior         | Description      |
| ---------------- | ---------------- |
| updateTheme()    | Changes UI theme |
| updateLanguage() | Updates locale   |

---

## Business Rules

* Invalid locales rejected
* Unsupported themes forbidden

---

# 8. OrganizationalAssignment Entity

## Purpose

Represents organizational structure assignment.

---

## Identity

```text id="r9m4xp"
organizationalAssignmentId
```

---

## Attributes

| Attribute                  | Type    | Description           |
| -------------------------- | ------- | --------------------- |
| organizationalAssignmentId | UUID    | Assignment identifier |
| userId                     | UUID    | Associated user       |
| organizationUnitId         | UUID    | Department/team/etc   |
| roleName                   | String  | Organizational role   |
| assignedAt                 | Instant | Assignment timestamp  |

---

## Behaviors

| Behavior               | Description        |
| ---------------------- | ------------------ |
| assignToOrganization() | Creates assignment |
| removeAssignment()     | Removes assignment |

---

## Business Rules

* Tenant consistency mandatory
* Hierarchy validation required

---

# 9. UserState Entity

## Purpose

Represents business lifecycle state.

---

## Identity

```text id="w6v3xt"
userStateId
```

---

## Attributes

| Attribute   | Type    | Description          |
| ----------- | ------- | -------------------- |
| userStateId | UUID    | State identifier     |
| state       | String  | ACTIVE/SUSPENDED/etc |
| changedAt   | Instant | Transition timestamp |
| changedBy   | UUID    | Responsible actor    |

---

## Behaviors

| Behavior             | Description        |
| -------------------- | ------------------ |
| transitionTo()       | Changes state      |
| validateTransition() | Enforces lifecycle |

---

## Allowed States

```text id="m1x8wr"
INVITED
ACTIVE
SUSPENDED
DEACTIVATED
LOCKED
```

---

## Business Rules

* Invalid transitions rejected
* Deleted users not reactivated

---

# 10. UserOnboarding Entity

## Purpose

Represents onboarding progression.

---

## Identity

```text id="t4m2vp"
onboardingId
```

---

## Attributes

| Attribute    | Type    | Description             |
| ------------ | ------- | ----------------------- |
| onboardingId | UUID    | Onboarding identifier   |
| userId       | UUID    | Associated user         |
| currentStage | String  | Current onboarding step |
| completedAt  | Instant | Completion timestamp    |

---

## Behaviors

| Behavior             | Description          |
| -------------------- | -------------------- |
| advanceStage()       | Moves workflow       |
| completeOnboarding() | Finalizes onboarding |

---

## Example Stages

```text id="d8v7wr"
- Invitation accepted
- Profile completed
- Membership assigned
- Preferences configured
```

---

# 11. MembershipRoleAssignment Entity

## Purpose

Represents linkage between memberships and authorization roles.

---

## Identity

```text id="g3x9wt"
membershipRoleAssignmentId
```

---

## Attributes

| Attribute                  | Type    | Description           |
| -------------------------- | ------- | --------------------- |
| membershipRoleAssignmentId | UUID    | Assignment identifier |
| membershipId               | UUID    | Linked membership     |
| roleId                     | UUID    | Authorization role    |
| assignedAt                 | Instant | Assignment timestamp  |

---

## Behaviors

| Behavior     | Description     |
| ------------ | --------------- |
| assignRole() | Associates role |
| revokeRole() | Removes role    |

---

## Important Note

Authorization evaluation belongs to:

```text id="u7m1xp"
Authorization Management
```

---

# 12. UserContactInformation Entity

## Purpose

Represents user communication information.

---

## Identity

```text id="y5v8wr"
contactInformationId
```

---

## Attributes

| Attribute            | Type   | Description                |
| -------------------- | ------ | -------------------------- |
| contactInformationId | UUID   | Contact identifier         |
| email                | String | Contact email              |
| phoneNumber          | String | Optional phone             |
| emergencyContact     | String | Optional emergency contact |

---

## Behaviors

| Behavior      | Description           |
| ------------- | --------------------- |
| updateEmail() | Changes contact email |
| updatePhone() | Changes phone         |

---

## Business Rules

* Email validation mandatory
* Phone normalization recommended

---

# 13. UserAvatar Entity

## Purpose

Represents profile avatar metadata.

---

## Identity

```text id="p6m4xt"
avatarId
```

---

## Attributes

| Attribute     | Type    | Description       |
| ------------- | ------- | ----------------- |
| avatarId      | UUID    | Avatar identifier |
| userId        | UUID    | Associated user   |
| fileReference | String  | Storage reference |
| uploadedAt    | Instant | Upload timestamp  |

---

## Behaviors

| Behavior       | Description    |
| -------------- | -------------- |
| uploadAvatar() | Uploads avatar |
| removeAvatar() | Removes avatar |

---

## Security Rules

* File validation mandatory
* Malware scanning recommended

---

# 14. UserLocalization Entity

## Purpose

Represents localization preferences.

---

## Identity

```text id="c2v7xp"
localizationId
```

---

## Attributes

| Attribute      | Type   | Description             |
| -------------- | ------ | ----------------------- |
| localizationId | UUID   | Localization identifier |
| language       | String | Preferred language      |
| timezone       | String | Preferred timezone      |
| dateFormat     | String | Regional formatting     |

---

## Behaviors

| Behavior         | Description      |
| ---------------- | ---------------- |
| changeLanguage() | Updates locale   |
| changeTimezone() | Updates timezone |

---

# 15. UserVisibilityRule Entity

## Purpose

Represents visibility constraints.

---

## Identity

```text id="h9m3wr"
visibilityRuleId
```

---

## Attributes

| Attribute           | Type   | Description           |
| ------------------- | ------ | --------------------- |
| visibilityRuleId    | UUID   | Visibility identifier |
| tenantId            | UUID   | Tenant scope          |
| organizationalScope | String | Department/team       |
| visibilityLevel     | String | Access scope          |

---

## Behaviors

| Behavior              | Description      |
| --------------------- | ---------------- |
| validateVisibility()  | Enforces access  |
| restrictEnumeration() | Prevents leakage |

---

## Critical Rule

```text id="n4x1vt"
Cross-tenant enumeration forbidden
```

---

# 16. MembershipMetadata Entity

## Purpose

Represents additional tenant membership metadata.

---

## Identity

```text id="v8m2wp"
membershipMetadataId
```

---

## Example Metadata

```text id="f5x7wr"
- Join source
- Invitation origin
- Internal notes
```

---

# 17. UserTag Entity

## Purpose

Represents user categorization.

---

## Identity

```text id="r1v9xp"
userTagId
```

---

## Examples

```text id="m6x4vt"
- VIP patient
- Senior psychologist
- Contractor
```

---

## Behaviors

| Behavior    | Description    |
| ----------- | -------------- |
| assignTag() | Associates tag |
| removeTag() | Removes tag    |

---

# 18. Entity Relationships

```text id="k7v3wr"
User
    ├── owns -> UserProfile
    ├── owns -> UserPreferences
    ├── owns -> UserLocalization
    ├── owns -> UserAvatar
    ├── owns -> UserContactInformation
    ├── associated with -> TenantMembership
    ├── progresses through -> UserOnboarding
    └── constrained by -> UserVisibilityRule
```

---

# 19. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="t3m8xp"
- TenantMembership
- OrganizationalAssignment
- UserVisibilityRule
- MembershipRoleAssignment
```

---

# 20. Security-Critical Rules

## Sensitive Data Restrictions

Entities must never expose:

```text id="u1x6wr"
- Password hashes
- JWT tokens
- MFA secrets
```

---

## Soft Delete Recommended

Preferred approach:

```text id="q5v2vt"
DEACTIVATED
instead of hard deletion
```

---

## Visibility Restrictions

Tenant isolation mandatory.

---

# 21. Lifecycle Considerations

| Entity           | Lifecycle   |
| ---------------- | ----------- |
| User             | Long-term   |
| TenantMembership | Medium-long |
| UserInvitation   | Short-term  |
| UserPreferences  | Continuous  |
| UserOnboarding   | Temporary   |

---

# 22. Future Entity Extensions

Future entities may include:

* ProfessionalLicense
* ClinicalSpecialization
* ExternalIdentityLink
* DelegatedAdministration
* UserRelationshipGraph
* FederatedIdentityProfile

---

# 23. Summary

The User Management entities provide:

* Enterprise-grade user lifecycle modeling
* Multi-tenant membership management
* Organizational relationship support
* Reactive onboarding orchestration
* Tenant-aware visibility enforcement
* SaaS-ready identity abstraction
* Compliance-aware user modeling

These entities form the operational foundation of the user ecosystem.

```
```
