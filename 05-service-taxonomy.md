# Service Taxonomy

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Service Taxonomy for CodeCore.

Its objectives are:

* standardize service responsibilities
* prevent service-layer chaos
* preserve architectural boundaries
* define service ownership clearly
* avoid business logic leakage
* guide AI-assisted development
* maintain modular consistency
* ensure reactive-safe orchestration

This specification is mandatory for:

* application services
* domain services
* orchestration layers
* infrastructure services
* module interactions
* AI-generated implementations

---

# 2. SERVICE PHILOSOPHY

---

## 2.1 Core Principle

Services exist to:

```text id="1service1"
coordinate responsibilities
that do not naturally belong
inside entities or value objects.
```

---

## 2.2 Forbidden Service Abuse

Services MUST NOT become:

* god classes
* business dumping grounds
* transaction jungles
* orchestration chaos
* utility replacements

---

# 2.3 Explicit Responsibility Principle

Every service MUST have:

* explicit purpose
* explicit ownership
* explicit boundaries

---

# 3. OFFICIAL SERVICE TAXONOMY

CodeCore officially recognizes ONLY the following service categories:

| Service Type           | Responsibility                            |
| ---------------------- | ----------------------------------------- |
| Application Service    | Use case coordination                     |
| Domain Service         | Pure domain logic                         |
| Infrastructure Service | External systems & technical capabilities |
| Orchestration Service  | Cross-module workflows                    |
| Query Service          | Specialized read operations               |
| Policy Service         | Complex business policy evaluation        |

---

# 4. APPLICATION SERVICES

---

# 4.1 Official Definition

Application Services coordinate:

* use cases
* transactions
* aggregate interaction
* workflow entry points

---

# 4.2 Responsibilities

Application Services MAY:

* load aggregates
* invoke domain behavior
* coordinate repositories
* publish events
* coordinate transactions
* invoke infrastructure abstractions

---

# 4.3 Forbidden Responsibilities

Application Services MUST NOT:

* contain persistence implementation
* contain infrastructure implementation
* contain HTTP logic
* become god services
* bypass aggregate rules

---

# 4.4 Typical Examples

```text id="2service2"
ScheduleAppointmentService
RegisterUserService
ActivateTenantService
```

---

# 4.5 Transaction Ownership

Application Services are the PRIMARY transaction boundary owners.

---

# 4.6 Event Coordination

Application Services MAY:

* publish integration events
* coordinate workflow events

after successful aggregate operations.

---

# 5. DOMAIN SERVICES

---

# 5.1 Official Definition

Domain Services contain:

* pure business/domain logic
* operations that do not belong naturally to a single entity

---

# 5.2 Core Principle

Domain Services MUST remain:

* domain-focused
* infrastructure-independent
* side-effect minimal

---

# 5.3 Responsibilities

Domain Services MAY:

* evaluate business rules
* coordinate domain calculations
* validate domain policies
* enforce cross-entity invariants

---

# 5.4 Forbidden Responsibilities

Domain Services MUST NOT:

* access databases directly
* call external APIs
* send emails
* perform HTTP operations
* manage transactions

---

# 5.5 Examples

```text id="3service3"
AppointmentConflictPolicyService
PasswordStrengthPolicyService
TenantQuotaPolicyService
```

---

# 5.6 Purity Principle

Domain Services SHOULD behave:

* deterministically
* predictably
* side-effect free whenever possible

---

# 6. INFRASTRUCTURE SERVICES

---

# 6.1 Official Definition

Infrastructure Services encapsulate:

* technical capabilities
* external integrations
* platform dependencies

---

# 6.2 Responsibilities

Infrastructure Services MAY:

* send emails
* upload files
* access Redis
* access external APIs
* integrate with cloud providers
* manage JWT generation

---

# 6.3 Forbidden Responsibilities

Infrastructure Services MUST NOT:

* contain business orchestration
* enforce domain policies
* own business workflows

---

# 6.4 Examples

```text id="4service4"
EmailSenderService
JwtTokenProvider
RedisCacheService
S3FileStorageService
```

---

# 6.5 Infrastructure Isolation Principle

Infrastructure implementations MUST remain isolated from:

* domain entities
* aggregate internals
* business-specific orchestration

---

# 7. ORCHESTRATION SERVICES

---

# 7.1 Official Definition

Orchestration Services coordinate:

* complex workflows
* cross-module interactions
* long-running operations

---

# 7.2 Responsibilities

Orchestration Services MAY:

* coordinate multiple application services
* manage workflow progression
* handle eventual consistency
* coordinate asynchronous operations

---

# 7.3 Forbidden Responsibilities

Orchestration Services MUST NOT:

* own domain invariants
* bypass aggregates
* manipulate persistence directly

---

# 7.4 Examples

```text id="5service5"
PatientOnboardingOrchestrator
AppointmentWorkflowOrchestrator
TenantProvisioningOrchestrator
```

---

# 7.5 Long Running Workflow Principle

Long-running workflows SHOULD belong to:

* orchestration services
* workflow engines

NOT application services.

---

# 8. QUERY SERVICES

---

# 8.1 Official Definition

Query Services handle:

* specialized read operations
* optimized projections
* reporting queries
* search operations

---

# 8.2 Responsibilities

Query Services MAY:

* use projections
* optimize read performance
* aggregate read models
* support pagination

---

# 8.3 Forbidden Responsibilities

Query Services MUST NOT:

* mutate domain state
* enforce business invariants
* orchestrate workflows

---

# 8.4 Examples

```text id="6service6"
AppointmentSearchService
DashboardMetricsQueryService
TenantAnalyticsQueryService
```

---

# 9. POLICY SERVICES

---

# 9.1 Official Definition

Policy Services evaluate:

* complex reusable business policies
* reusable rule systems
* configurable constraints

---

# 9.2 Responsibilities

Policy Services MAY:

* validate rules
* evaluate constraints
* determine permissions
* calculate eligibility

---

# 9.3 Examples

```text id="7service7"
PasswordPolicyService
AppointmentSchedulingPolicyService
FeatureAccessPolicyService
```

---

# 9.4 Pure Policy Principle

Policy Services SHOULD remain:

* stateless
* deterministic
* reusable

---

# 10. SERVICE BOUNDARY RULES

---

# 10.1 Single Responsibility Principle

Each Service MUST own:

* one responsibility domain
* one coordination purpose

---

# 10.2 Service Size Rule

Services MUST remain:

* cohesive
* small
* focused

---

# 10.3 God Service Anti-Pattern

Forbidden:

```text id="8service8"
UserManagementService
SystemService
CommonBusinessService
```

when they accumulate unrelated responsibilities.

---

# 11. TRANSACTION RULES

---

# 11.1 Transaction Ownership

Transactions SHOULD be coordinated by:

* Application Services

---

# 11.2 Domain Transaction Restrictions

Domain Services MUST NOT:

* control transactions
* manage transaction lifecycle

---

# 11.3 Reactive Transaction Principle

Reactive transactions MUST:

* remain non-blocking
* preserve Reactor Context
* avoid imperative leakage

---

# 12. REACTIVE SERVICE RULES

---

# 12.1 Official Reactive Standard

All services MUST support:

* reactive flows
* non-blocking execution
* Reactor compatibility

---

# 12.2 Blocking Operations Forbidden

Blocking operations inside reactive services are forbidden.

---

# 12.3 Reactive Return Types

Preferred:

```text id="9service9"
Mono<T>
Flux<T>
```

Forbidden:

```text id="10service10"
List<T>
Optional<T>
Future<T>
```

inside reactive service contracts.

---

# 12.4 Reactor Context Preservation

Services MUST preserve:

* tenant context
* security context
* tracing context

through reactive chains.

---

# 13. MULTITENANCY RULES

---

# 13.1 Tenant Enforcement

Services MUST enforce:

* tenant isolation
* tenant ownership validation
* tenant-aware operations

---

# 13.2 Cross Tenant Protection

Cross-tenant operations are forbidden unless:

* platform-level
* explicitly authorized

---

# 14. EVENT RULES

---

# 14.1 Event Ownership

Application Services MAY:

* publish integration events
* coordinate event propagation

---

# 14.2 Domain Event Separation

Services MUST distinguish:

* domain events
* integration events

---

# 14.3 Event Timing

Events MUST represent:

* completed operations
* valid state transitions

---

# 15. ERROR HANDLING RULES

---

# 15.1 Explicit Exceptions

Services MUST throw:

* meaningful exceptions
* domain-safe exceptions
* context-safe exceptions

---

# 15.2 Exception Leakage Forbidden

Infrastructure exceptions MUST NOT leak into:

* domain layer
* application contracts

---

# 15.3 Reactive Error Handling

Reactive services MUST:

* preserve error propagation
* avoid swallowed exceptions

---

# 16. OBSERVABILITY RULES

---

# 16.1 Traceability

Services SHOULD support:

* tracing
* metrics
* correlation IDs
* tenant-aware logs

---

# 16.2 Sensitive Data Protection

Services MUST avoid logging:

* passwords
* tokens
* secrets
* sensitive information

---

# 17. SERVICE NAMING RULES

---

# 17.1 Naming Style

Services MUST use:

```text id="11service11"
<BusinessPurpose>Service
```

---

## Correct

```text id="12service12"
RegisterUserService
ScheduleAppointmentService
SendNotificationService
```

---

## Forbidden

```text id="13service13"
UtilsService
CommonService
BaseManager
```

---

# 17.2 Orchestration Naming

Orchestration services SHOULD use:

```text id="14service14"
<WorkflowName>Orchestrator
```

---

# 17.3 Query Service Naming

Query services SHOULD use:

```text id="15service15"
<QueryPurpose>QueryService
```

---

# 18. FORBIDDEN SERVICE ANTI-PATTERNS

---

# Forbidden

* God services
* Transaction dumping
* Repository orchestration abuse
* Infrastructure leakage
* HTTP-aware services
* Framework-coupled domain services
* Massive orchestration services
* Shared mutable service state
* Utility service dumping
* Cross-module uncontrolled orchestration

---

# 19. AI IMPLEMENTATION RULES

All AI-generated services MUST:

* preserve service boundaries
* avoid god services
* avoid business dumping
* preserve modularity
* remain reactive-friendly
* preserve transaction boundaries
* avoid infrastructure leakage
* preserve tenant isolation
* use explicit naming
* respect aggregate ownership

---

# 20. SERVICE DESIGN CHECKLIST

Before implementing a Service verify:

* Is this truly a service responsibility?
* Which service type applies?
* Is orchestration correctly placed?
* Is business logic leaking incorrectly?
* Are transactions correctly coordinated?
* Is the service reactive-friendly?
* Is tenant isolation enforced?
* Is naming explicit?
* Is infrastructure isolated?
* Are side effects controlled?
* Is the service cohesive?
* Is Reactor Context preserved?
* Are exceptions properly handled?
* Are boundaries respected?
* Is the service small enough?

---

# 21. CODECORE OFFICIAL SERVICE PHILOSOPHY

```text id="16service16"
Services exist to coordinate responsibilities
that do not naturally belong inside aggregates,
entities or value objects, while preserving
architectural boundaries and modular integrity.
```
