````md id="5x8qpm"
# 03-package-structure-standard.md

# 1. Introduction

This document defines the official Package Structure Standard of the CodeCore platform.

The purpose of this standard is to establish:

- bounded context directory structure
- module organization rules
- package naming standards
- layer separation rules
- architectural boundary enforcement
- infrastructure isolation standards
- scalable modular organization

This document follows:

- ADR-004 Hexagonal Architecture
- ADR-005 Domain-Driven Design Strategy
- ADR-001 Reactive-First Architecture

---

# 2. Purpose

Package structure exists to enforce:

```text id="x7m2qp"
architectural consistency
````

across the entire CodeCore platform.

---

# Critical Principle

```text id="m4v8wr"
folder structure
is architecture
```

---

# 3. Strategic Goals

The package structure standard exists to guarantee:

| Goal            | Purpose                  |
| --------------- | ------------------------ |
| Consistency     | Predictable architecture |
| Isolation       | Clear boundaries         |
| Maintainability | Easier evolution         |
| Scalability     | Enterprise growth        |
| Discoverability | Faster onboarding        |

---

# 4. Official Monorepo Structure

# Root Structure

```text id="u8m1ld"
/codecore
│
├── apps/
├── modules/
├── shared/
├── platform/
├── infrastructure/
├── build-logic/
├── docs/
└── scripts/
```

---

# 5. Root Directory Responsibilities

# 5.1 apps/

Contains executable runtime applications.

---

# Examples

```text id="k5m7qp"
/apps/codecore-api
/apps/admin-console
```

---

# Responsibilities

| Responsibility         | Allowed |
| ---------------------- | ------- |
| Spring Boot bootstrap  | Yes     |
| Runtime initialization | Yes     |
| Global configuration   | Yes     |
| Module composition     | Yes     |

---

# Forbidden

```text id="f2m8ld"
business logic
inside apps/
```

---

# 5.2 modules/

Contains all bounded contexts.

---

# Mandatory Rule

```text id="r9m4wr"
one bounded context
=
one module
```

---

# Examples

```text id="u3m1qp"
/modules/01-identity-access-management
/modules/10-billing-management
/modules/11-payment-management
```

---

# Forbidden

```text id="m8x4qp"
shared business ownership
between modules
```

---

# 5.3 shared/

Contains minimal reusable platform primitives.

---

# Examples

```text id="x1m7wr"
/shared/shared-kernel
/shared/shared-events
/shared/shared-security
```

---

# Forbidden

```text id="v6m2qp"
shared business services
```

---

# Critical Rule

```text id="u9m4ld"
shared modules
must remain minimal
```

---

# 5.4 platform/

Contains reusable infrastructure abstractions.

---

# Examples

```text id="q7m4wr"
/platform/kafka
/platform/security
/platform/observability
```

---

# Forbidden

```text id="m9x2qp"
business rules
inside platform/
```

---

# 5.5 infrastructure/

Contains runtime infrastructure resources.

---

# Examples

```text id="f2m7wr"
/infrastructure/docker
/infrastructure/kafka
/infrastructure/postgres
```

---

# 6. Official Bounded Context Structure

Every bounded context MUST follow:

```text id="x5m1ld"
hexagonal architecture structure
```

---

# Standard Structure

```text id="u7m8qp"
<bounded-context>/
│
├── domain/
├── application/
├── ports/
├── adapters/
├── infrastructure/
└── bootstrap/
```

---

# Example

```text id="m6x7wr"
/modules/10-billing-management
│
├── domain/
├── application/
├── ports/
├── adapters/
├── infrastructure/
└── bootstrap/
```

---

# 7. Domain Layer Structure

# Purpose

Contains pure business logic.

---

# Standard Structure

```text id="u1m4ld"
domain/
│
├── aggregates/
├── entities/
├── value-objects/
├── events/
├── services/
├── exceptions/
└── policies/
```

---

# Responsibilities

| Package       | Responsibility            |
| ------------- | ------------------------- |
| aggregates    | Transaction boundaries    |
| entities      | Identity-based models     |
| value-objects | Immutable business values |
| events        | Domain events             |
| services      | Domain services           |
| policies      | Business policies         |

---

# Forbidden

```text id="v8m2qp"
framework annotations
inside domain/
```

---

# Forbidden

```text id="q5m8wr"
database logic
inside domain/
```

---

# Critical Rule

```text id="x7m1qp"
domain/
must remain pure
```

---

# 8. Application Layer Structure

# Purpose

Contains use-case orchestration.

---

# Standard Structure

```text id="m2v8ld"
application/
│
├── use-cases/
├── commands/
├── queries/
├── services/
├── handlers/
├── dto/
└── mappers/
```

---

# Responsibilities

| Package   | Responsibility             |
| --------- | -------------------------- |
| use-cases | Application orchestration  |
| commands  | State mutation requests    |
| queries   | Read requests              |
| handlers  | Command/query handling     |
| dto       | Application contracts      |
| mappers   | Controlled transformations |

---

# Forbidden

```text id="u4m7wr"
business invariants
inside application/
```

---

# Critical Rule

```text id="f8m1ld"
application/
coordinates
it does not own
business rules
```

---

# 9. Ports Layer Structure

# Purpose

Contains architecture contracts.

---

# Standard Structure

```text id="m6x2qp"
ports/
│
├── inbound/
└── outbound/
```

---

# Responsibilities

| Package  | Responsibility              |
| -------- | --------------------------- |
| inbound  | Use-case contracts          |
| outbound | Infrastructure abstractions |

---

# Examples

```text id="x1m9wr"
UserRepositoryPort
EventPublisherPort
PaymentProviderPort
```

---

# Forbidden

```text id="p7m4ld"
provider implementations
inside ports/
```

---

# Critical Rule

```text id="v5m8qp"
ports
define contracts
not implementations
```

---

# 10. Adapters Layer Structure

# Purpose

Contains infrastructure translators.

---

# Standard Structure

```text id="q3m1wr"
adapters/
│
├── inbound/
│   ├── rest/
│   ├── messaging/
│   └── graphql/
│
└── outbound/
    ├── persistence/
    ├── messaging/
    ├── cache/
    └── providers/
```

---

# Responsibilities

| Adapter Type | Responsibility               |
| ------------ | ---------------------------- |
| inbound      | External request translation |
| outbound     | Infrastructure integration   |

---

# Forbidden

```text id="k9m7qp"
core business rules
inside adapters/
```

---

# Critical Rule

```text id="u4m7wr"
adapters
translate
they do not own
business logic
```

---

# 11. Infrastructure Layer Structure

# Purpose

Contains technical runtime configuration.

---

# Standard Structure

```text id="x8m4qp"
infrastructure/
│
├── configuration/
├── security/
├── messaging/
├── persistence/
├── observability/
└── cache/
```

---

# Responsibilities

| Package       | Responsibility        |
| ------------- | --------------------- |
| configuration | Runtime configuration |
| security      | Security bootstrap    |
| messaging     | Kafka configuration   |
| persistence   | DB configuration      |
| observability | Tracing/metrics       |
| cache         | Redis configuration   |

---

# Forbidden

```text id="r6m2ld"
business orchestration
inside infrastructure/
```

---

# 12. Bootstrap Layer Structure

# Purpose

Contains bounded context initialization.

---

# Standard Structure

```text id="y2m8wr"
bootstrap/
│
├── configuration/
├── registration/
└── initialization/
```

---

# Responsibilities

| Responsibility        | Allowed |
| --------------------- | ------- |
| Bean registration     | Yes     |
| Module initialization | Yes     |
| Wiring                | Yes     |

---

# Forbidden

```text id="m1x7qp"
business logic
inside bootstrap/
```

---

# 13. Naming Convention Rules

# Package Naming Rules

All package names MUST use:

```text id="u8m4ld"
lowercase
kebab-case
```

for modules/directories.

---

# Java Package Rules

Java packages MUST use:

```text id="k3m1wr"
lowercase
dot notation
```

---

# Examples

## Correct

```text id="x5m8qp"
com.codecore.billing.domain
```

---

## Incorrect

```text id="u4m7wr"
com.codecore.Billing.Domain
```

---

# 14. Module Isolation Rules

Modules MUST remain isolated.

---

# Forbidden

```text id="m9x7qp"
cross-module internal package access
```

---

# Forbidden

```text id="r6m2ld"
shared internal mutable state
```

---

# Allowed

| Communication Type       | Allowed    |
| ------------------------ | ---------- |
| Events                   | Yes        |
| Explicit APIs            | Yes        |
| Shared kernel primitives | Controlled |

---

# Critical Rule

```text id="x8m4qp"
modules
must communicate
through contracts
```

---

# 15. Reactive Structure Rules

Reactive logic MUST remain:

* explicit
* isolated
* traceable

---

# Preferred Locations

| Concern                 | Recommended Location |
| ----------------------- | -------------------- |
| Reactive orchestration  | application/         |
| Reactive adapters       | adapters/            |
| Reactive infrastructure | infrastructure/      |

---

# Forbidden

```text id="f4m1wr"
reactive spaghetti flows
across random layers
```

---

# 16. Event-Driven Structure Rules

EDA infrastructure MUST remain isolated.

---

# Preferred Locations

| Concern           | Recommended Location        |
| ----------------- | --------------------------- |
| Event definitions | domain/events               |
| Kafka consumers   | adapters/inbound/messaging  |
| Kafka producers   | adapters/outbound/messaging |

---

# Forbidden

```text id="m7x2qp"
Kafka logic
inside aggregates
```

---

# 17. Testing Structure Rules

Tests MUST mirror production structure.

---

# Standard Structure

```text id="u3m8wr"
src/
├── main/
└── test/
```

---

# Recommended Test Organization

```text id="k5m1ld"
test/
│
├── domain/
├── application/
├── adapters/
└── integration/
```

---

# Critical Rule

```text id="v2m7qp"
tests
must validate
architectural boundaries
```

---

# 18. Package Anti-Patterns

# Anti-Pattern 1

```text id="x9m4wr"
Layerless Chaos
```

No architectural separation.

---

# Anti-Pattern 2

```text id="q4m8qp"
Generic Shared Utils Explosion
```

Massive shared helper packages.

---

# Anti-Pattern 3

```text id="u1m7wr"
Infrastructure Leakage
```

Framework logic inside domain.

---

# Anti-Pattern 4

```text id="m6x2qp"
Cross-Module Internal Access
```

Modules bypassing contracts.

---

# Anti-Pattern 5

```text id="r8m1ld"
God Packages
```

Huge mixed-responsibility folders.

---

# 19. Non-Negotiable Rules

# Rule 1

```text id="x3m7qp"
folder structure
must reflect architecture
```

---

# Rule 2

```text id="y7m1ld"
business logic
must remain
inside domain/
```

---

# Rule 3

```text id="p4m8qp"
modules
must remain isolated
```

---

# Rule 4

```text id="u7m2ld"
infrastructure
must remain separated
from domain logic
```

---

# Rule 5

```text id="m4v8wr"
shared modules
must remain minimal
```

---

# 20. Final Statement

Package structure is considered an architectural governance mechanism of the CodeCore platform.

All modules, bounded contexts, adapters, infrastructure components, and shared libraries MUST preserve:

* explicit layering
* bounded context isolation
* domain purity
* infrastructure separation
* reactive organization
* event-driven boundaries
* tenant-safe modularity
* scalable maintainability

Package structure consistency is considered foundational to the long-term evolution and maintainability of CodeCore.

```
```
