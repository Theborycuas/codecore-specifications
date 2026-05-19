````md id="4x7qpm"
# 06-naming-conventions.md

# 1. Introduction

This document defines the official Naming Conventions Standard of the CodeCore platform.

The purpose of this standard is to establish:

- consistent naming rules
- domain language consistency
- bounded context terminology
- API naming standards
- event naming standards
- aggregate naming rules
- package naming standards
- reactive naming conventions

This document follows:

- ADR-005 Domain-Driven Design Strategy
- ADR-004 Hexagonal Architecture
- ADR-002 Event-Driven Architecture

---

# 2. Purpose

Naming conventions exist to enforce:

```text id="x7m2qp"
architectural clarity
+
ubiquitous language
+
long-term maintainability
````

---

# Critical Principle

```text id="m4v8wr"
naming
is architecture
```

---

# 3. Ubiquitous Language Rules

All names MUST follow:

```text id="u8m1ld"
business language
```

instead of technical jargon.

---

# Correct Examples

| Correct         | Incorrect        |
| --------------- | ---------------- |
| Invoice         | BillingData      |
| Subscription    | PlanThing        |
| PaymentCaptured | ExecuteMoneyFlow |

---

# Forbidden

```text id="k5m7qp"
technical naming
for business concepts
```

---

# Critical Rule

```text id="f2m8ld"
same term
=
same meaning
across the platform
```

---

# 4. Module Naming Rules

Modules MUST use:

```text id="r9m4wr"
kebab-case
```

---

# Correct Examples

```text id="u3m1qp"
01-identity-access-management
10-billing-management
11-payment-management
```

---

# Incorrect Examples

```text id="m8x4qp"
BillingModule
payment_management
paymentManagement
```

---

# Mandatory Rule

```text id="x1m7wr"
module names
must represent
bounded contexts
```

---

# 5. Package Naming Rules

Java packages MUST use:

```text id="v6m2qp"
lowercase
dot notation
```

---

# Correct Examples

```text id="u9m4ld"
com.codecore.billing.domain
com.codecore.payment.application
```

---

# Incorrect Examples

```text id="q7m4wr"
com.codecore.Billing.Domain
com.codecore.paymentManagement
```

---

# Forbidden

```text id="m9x2qp"
generic package names
```

---

# Incorrect Generic Examples

```text id="f2m7wr"
common
utils
helpers
manager
service
```

---

# 6. Aggregate Naming Rules

Aggregates MUST use:

```text id="x5m1ld"
business entity names
```

---

# Correct Examples

```text id="u7m8qp"
Invoice
Subscription
Payment
User
```

---

# Incorrect Examples

```text id="m6x7wr"
InvoiceManager
SubscriptionProcessor
PaymentHandler
```

---

# Forbidden

```text id="u1m4ld"
technical suffixes
for aggregates
```

---

# 7. Entity Naming Rules

Entities MUST represent:

```text id="v8m2qp"
identity-based business concepts
```

---

# Correct Examples

| Entity       |
| ------------ |
| User         |
| Invoice      |
| Organization |
| Subscription |

---

# Forbidden

```text id="q5m8wr"
Entity suffixes
inside domain language
```

---

# Incorrect Examples

```text id="x7m1qp"
UserEntity
InvoiceEntity
SubscriptionEntity
```

---

# Exception

Infrastructure persistence models MAY use:

```text id="m2v8ld"
Entity
```

suffixes when explicitly required.

---

# 8. Value Object Naming Rules

Value Objects MUST use:

```text id="u4m7wr"
business value terminology
```

---

# Correct Examples

```text id="f8m1ld"
Money
Email
Address
TenantId
CorrelationId
```

---

# Incorrect Examples

```text id="m6x2qp"
MoneyDTO
AddressData
EmailObject
```

---

# Critical Rule

```text id="x1m9wr"
value objects
must sound
like business concepts
```

---

# 9. Event Naming Rules

Events MUST follow:

```text id="p7m4ld"
<Entity><PastTenseVerb>
```

---

# Correct Examples

```text id="v5m8qp"
UserRegistered
PaymentCaptured
InvoiceGenerated
SubscriptionExpired
```

---

# Incorrect Examples

```text id="q3m1wr"
RegisterUser
CapturePayment
DoInvoice
```

---

# Forbidden

```text id="k9m7qp"
command-style event names
```

---

# Critical Rule

```text id="u4m7wr"
events
represent completed facts
```

---

# 10. Repository Naming Rules

Repositories MUST use:

```text id="x8m4qp"
<Aggregate>Repository
```

---

# Correct Examples

```text id="r6m2ld"
UserRepository
InvoiceRepository
PaymentRepository
```

---

# Incorrect Examples

```text id="y2m8wr"
UserDAO
PaymentPersistenceManager
InvoiceDataThing
```

---

# Critical Rule

```text id="m1x7qp"
repositories
belong to aggregates
```

---

# 11. Port Naming Rules

Ports MUST explicitly describe:

```text id="u8m4ld"
business capability
or infrastructure contract
```

---

# Correct Examples

```text id="k3m1wr"
PaymentProviderPort
EmailSenderPort
UserRepositoryPort
```

---

# Incorrect Examples

```text id="x5m8qp"
ProviderUtils
ExternalManager
GenericService
```

---

# Mandatory Rule

```text id="u4m7wr"
ports
must describe contracts
not implementations
```

---

# 12. Adapter Naming Rules

Adapters MUST describe:

* transport
* provider
* infrastructure responsibility

---

# Correct Examples

```text id="m9x7qp"
StripePaymentAdapter
KafkaUserRegisteredConsumer
PostgresInvoiceRepositoryAdapter
```

---

# Incorrect Examples

```text id="r6m2ld"
PaymentHelper
MessageProcessor
DataThing
```

---

# Critical Rule

```text id="x8m4qp"
adapter names
must reveal infrastructure responsibility
```

---

# 13. Service Naming Rules

Application services MUST describe:

```text id="f4m1wr"
use-case orchestration
```

---

# Correct Examples

```text id="m7x2qp"
RegisterUserUseCase
GenerateInvoiceUseCase
CapturePaymentUseCase
```

---

# Incorrect Examples

```text id="u3m8wr"
UserManager
PaymentEngine
InvoiceProcessor
```

---

# Forbidden

```text id="k5m1ld"
generic service names
```

---

# 14. DTO Naming Rules

DTOs MUST use explicit suffixes.

---

# Mandatory Suffixes

| Type         | Suffix   |
| ------------ | -------- |
| Request DTO  | Request  |
| Response DTO | Response |
| Internal DTO | DTO      |

---

# Correct Examples

```text id="v2m7qp"
RegisterUserRequest
PaymentResponse
InvoiceSummaryDTO
```

---

# Incorrect Examples

```text id="x9m4wr"
UserData
PaymentObject
InvoiceBean
```

---

# 15. API Naming Rules

REST APIs MUST use:

```text id="q4m8qp"
resource-oriented naming
```

---

# Correct Examples

```text id="u1m7wr"
/api/v1/users
/api/v1/invoices
/api/v1/subscriptions
```

---

# Incorrect Examples

```text id="m6x2qp"
/api/doPayment
/api/runInvoice
/api/userManager
```

---

# Forbidden

```text id="r8m1ld"
verb-heavy REST endpoints
```

---

# 16. Kafka Topic Naming Rules

Topics MUST follow:

```text id="x3m7qp"
<context>.<aggregate>.<event>
```

---

# Correct Examples

```text id="y7m1ld"
iam.user.registered
billing.invoice.generated
payment.transaction.captured
```

---

# Incorrect Examples

```text id="p4m8qp"
user-events
billing-topic
payment-stream
```

---

# Critical Rule

```text id="u7m2ld"
topic names
must reveal ownership
```

---

# 17. Database Naming Rules

Database objects MUST use:

```text id="m4v8wr"
snake_case
```

---

# Correct Examples

```text id="x7m2qp"
tenant_id
invoice_id
payment_transaction
```

---

# Incorrect Examples

```text id="u8m1ld"
tenantId
InvoiceTable
PAYMENT_DATA
```

---

# Mandatory Rule

```text id="k5m7qp"
database names
must remain infrastructure-oriented
```

---

# 18. Reactive Naming Rules

Reactive methods SHOULD reveal:

```text id="f2m8ld"
async/reactive intent
```

---

# Preferred Patterns

| Pattern        | Recommended |
| -------------- | ----------- |
| findById       | Yes         |
| streamInvoices | Yes         |
| publishEvent   | Yes         |

---

# Avoid

```text id="r9m4wr"
Async suffix explosion
```

---

# Incorrect Examples

```text id="u3m1qp"
findUserAsyncReactiveOperation
```

---

# Critical Rule

```text id="m8x4qp"
reactive behavior
should be inferred
from return types
```

---

# 19. Boolean Naming Rules

Boolean fields MUST read naturally.

---

# Correct Examples

```text id="x1m7wr"
isActive
hasAccess
canRetry
```

---

# Incorrect Examples

```text id="v6m2qp"
activeFlag
accessThing
retryValue
```

---

# 20. Test Naming Rules

Tests MUST describe:

```text id="u9m4ld"
behavior
```

---

# Correct Examples

```text id="q7m4wr"
shouldRejectExpiredSubscription()
shouldPublishPaymentCapturedEvent()
```

---

# Incorrect Examples

```text id="m9x2qp"
test1()
validate()
serviceTest()
```

---

# 21. Naming Anti-Patterns

# Anti-Pattern 1

```text id="f2m7wr"
Generic Names
```

Names without business meaning.

---

# Anti-Pattern 2

```text id="x5m1ld"
Technical Domain Leakage
```

Infrastructure names inside business models.

---

# Anti-Pattern 3

```text id="u7m8qp"
Abbreviation Hell
```

Unreadable shortened names.

---

# Anti-Pattern 4

```text id="m6x7wr"
Suffix Explosion
```

ManagerServiceHelperFactoryProcessor.

---

# Anti-Pattern 5

```text id="u1m4ld"
Inconsistent Business Vocabulary
```

Multiple names for the same concept.

---

# 22. Non-Negotiable Rules

# Rule 1

```text id="v8m2qp"
business language
defines naming
```

---

# Rule 2

```text id="q5m8wr"
events
must use past tense
```

---

# Rule 3

```text id="x7m1qp"
module names
must reflect
bounded contexts
```

---

# Rule 4

```text id="m2v8ld"
generic names
are forbidden
```

---

# Rule 5

```text id="u4m7wr"
same concept
must use
same terminology
```

---

# 23. Final Statement

Naming conventions are considered an architectural governance mechanism of the CodeCore platform.

All modules, APIs, events, aggregates, repositories, adapters, DTOs, topics, and distributed workflows MUST preserve:

* ubiquitous language
* explicit ownership
* business clarity
* architectural consistency
* reactive readability
* event-driven semantics
* tenant-aware traceability
* scalable maintainability

Naming consistency is considered foundational to the long-term clarity and evolution of CodeCore.

```
```
