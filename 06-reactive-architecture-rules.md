# Reactive Architecture Rules

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Reactive Architecture Rules for CodeCore.

Its objectives are:

* standardize reactive development
* preserve non-blocking execution
* prevent imperative-reactive mixing
* enforce Reactor Context propagation
* ensure scalability under load
* maintain reactive consistency
* guide AI-assisted development
* protect performance and memory safety

This specification is mandatory for:

* WebFlux development
* Reactor pipelines
* service implementations
* repository implementations
* reactive security
* reactive transactions
* reactive integrations
* AI-generated code

---

# 2. REACTIVE PHILOSOPHY

---

## 2.1 Official Reactive Standard

CodeCore officially adopts:

```text id="1react1"
Reactive Programming
```

using:

* Spring WebFlux
* Project Reactor
* R2DBC
* Reactive Redis
* Non-blocking I/O

---

## 2.2 Core Principle

Reactive architecture exists to:

```text id="2react2"
maximize scalability and resource efficiency
through non-blocking asynchronous execution.
```

---

## 2.3 Non Blocking Principle

The platform MUST remain:

* non-blocking
* asynchronous
* backpressure-aware
* event-driven

---

# 3. REACTIVE EXECUTION RULES

---

# 3.1 Blocking Operations Forbidden

Blocking operations inside reactive flows are STRICTLY forbidden.

---

## Forbidden Operations

```text id="3react3"
.block()
.blockFirst()
.blockLast()
Thread.sleep()
Future.get()
JDBC calls
Synchronous HTTP clients
```

inside reactive execution paths.

---

# 3.2 Imperative Leakage Forbidden

Imperative patterns MUST NOT leak into:

* reactive services
* reactive repositories
* reactive workflows

---

# 3.3 Reactive End-to-End Principle

Reactive chains MUST remain reactive:

* from controller
* to service
* to repository
* to infrastructure

---

## Forbidden

```text id="4react4"
Reactive Controller
→ Blocking Service
→ Reactive Repository
```

---

## Correct

```text id="5react5"
Reactive Controller
→ Reactive Service
→ Reactive Repository
```

---

# 4. REACTOR TYPES RULES

---

# 4.1 Official Reactive Types

Preferred Reactor types:

```text id="6react6"
Mono<T>
Flux<T>
```

---

# 4.2 Forbidden Reactive Alternatives

Forbidden inside core reactive contracts:

```text id="7react7"
CompletableFuture
Future
Optional
List
Set
```

unless explicitly justified.

---

# 4.3 Mono Usage Principle

Use:

```text id="8react8"
Mono<T>
```

for:

* zero-or-one responses
* single async operations

---

# 4.4 Flux Usage Principle

Use:

```text id="9react9"
Flux<T>
```

for:

* streams
* collections
* continuous events
* paginated reactive flows

---

# 5. REACTOR CONTEXT RULES

---

# 5.1 Reactor Context Philosophy

Reactor Context is the official mechanism for:

* tenant propagation
* security propagation
* tracing propagation
* correlation propagation

---

# 5.2 Mandatory Context Propagation

The following MUST propagate through reactive chains:

* tenant context
* authenticated user
* correlation ID
* trace ID
* security metadata

---

# 5.3 ThreadLocal Forbidden

ThreadLocal-based context propagation is forbidden in reactive flows.

---

## Forbidden

```text id="10react10"
ThreadLocal TenantContext
SecurityContextHolder imperative usage
```

inside reactive pipelines.

---

# 5.4 Context Access Principle

Reactive services SHOULD retrieve contextual information from:

* Reactor Context
* reactive security context

NOT static thread-bound storage.

---

# 6. REACTIVE SERVICE RULES

---

# 6.1 Fully Reactive Services

All application services MUST remain:

* non-blocking
* Reactor-compatible
* asynchronous

---

# 6.2 Reactive Composition Principle

Services SHOULD compose flows using:

* flatMap
* map
* zip
* concatMap
* switchIfEmpty
* filter

---

# 6.3 Side Effect Isolation

Side effects SHOULD be isolated carefully.

---

## Forbidden Abuse

Excessive use of:

```text id="11react11"
doOnNext()
doOnSuccess()
subscribe()
```

for business orchestration.

---

# 6.4 Subscription Ownership

Framework layers SHOULD own subscriptions.

Manual subscriptions are discouraged.

---

## Forbidden

```text id="12react12"
service.subscribe()
```

inside business logic.

---

# 7. REACTIVE REPOSITORY RULES

---

# 7.1 Official Persistence Standard

CodeCore repositories MUST support:

* R2DBC
* non-blocking persistence
* reactive streams

---

# 7.2 JDBC Forbidden

Traditional blocking JDBC is forbidden inside reactive flows.

---

# 7.3 Repository Return Types

Repositories MUST return:

* Mono
* Flux

---

# 7.4 Reactive Query Principle

Queries SHOULD remain:

* lightweight
* stream-oriented
* backpressure-aware

---

# 8. BACKPRESSURE RULES

---

# 8.1 Backpressure Awareness

Reactive pipelines MUST remain:

* backpressure-safe
* resource-conscious
* scalable under load

---

# 8.2 Unbounded Streams Forbidden

Unbounded uncontrolled streams are forbidden.

---

# 8.3 Large Dataset Handling

Large datasets SHOULD use:

* chunking
* pagination
* streaming
* windowing

---

# 8.4 Memory Explosion Prevention

Reactive flows MUST avoid:

* collecting massive datasets into memory
* oversized buffering
* uncontrolled parallelization

---

# 9. SCHEDULER RULES

---

# 9.1 Scheduler Ownership Principle

Schedulers MUST be used intentionally.

---

# 9.2 publishOn / subscribeOn Abuse

Unnecessary scheduler switching is discouraged.

---

# 9.3 Blocking Isolation Strategy

If blocking code is unavoidable:

* isolate it explicitly
* use boundedElastic
* document justification

---

## Example

```text id="13react13"
Mono.fromCallable(...)
.subscribeOn(Schedulers.boundedElastic())
```

---

# 9.4 Scheduler Predictability

Reactive execution MUST remain:

* predictable
* traceable
* observable

---

# 10. REACTIVE TRANSACTION RULES

---

# 10.1 Official Transaction Strategy

Transactions MUST support:

* reactive transaction management
* Reactor Context propagation
* non-blocking execution

---

# 10.2 Imperative Transactions Forbidden

Imperative transaction management inside reactive flows is forbidden.

---

# 10.3 Transaction Scope Principle

Reactive transactions SHOULD remain:

* short-lived
* aggregate-oriented
* lightweight

---

# 10.4 Long Transactions Forbidden

Long-running reactive transactions are forbidden.

---

# 11. REACTIVE ERROR HANDLING RULES

---

# 11.1 Explicit Error Handling

Reactive flows MUST handle:

* domain errors
* infrastructure errors
* validation failures
* security failures

explicitly.

---

# 11.2 Error Swallowing Forbidden

Forbidden:

```text id="14react14"
onErrorResume(e -> Mono.empty())
```

without explicit justification.

---

# 11.3 Reactive Exception Translation

Infrastructure exceptions SHOULD be translated into:

* domain-safe exceptions
* application-safe exceptions

---

# 11.4 Fail Fast Principle

Reactive pipelines SHOULD fail:

* explicitly
* predictably
* traceably

---

# 12. REACTIVE EVENT RULES

---

# 12.1 Reactive Event Publication

Event publication SHOULD remain:

* asynchronous
* non-blocking
* context-aware

---

# 12.2 Event Stream Safety

Reactive event streams MUST:

* preserve tenant isolation
* preserve traceability
* avoid uncontrolled fan-out

---

# 12.3 Event Backpressure

Event processing MUST remain:

* backpressure-aware
* resource-safe

---

# 13. REACTIVE SECURITY RULES

---

# 13.1 Reactive Security Standard

Security MUST integrate with:

* Reactive Security Context
* Reactor Context

---

# 13.2 Imperative Security Context Forbidden

Forbidden:

```text id="15react15"
SecurityContextHolder.getContext()
```

inside reactive chains.

---

# 13.3 Reactive Authentication Propagation

Authentication MUST propagate:

* reactively
* asynchronously
* context-safely

---

# 14. MULTITENANCY RULES

---

# 14.1 Tenant Context Propagation

Tenant information MUST propagate through:

* Reactor Context

---

# 14.2 Tenant Loss Forbidden

Reactive chains MUST NEVER lose:

* tenant ownership context

---

# 14.3 Cross Tenant Reactive Leakage

Cross-tenant data leakage through reactive pipelines is forbidden.

---

# 15. OBSERVABILITY RULES

---

# 15.1 Reactive Traceability

Reactive flows MUST support:

* tracing
* metrics
* correlation IDs
* tenant-aware observability

---

# 15.2 Correlation Propagation

Correlation IDs MUST propagate through:

* reactive chains
* async operations
* event pipelines

---

# 15.3 Reactive Logging Rules

Reactive logs SHOULD remain:

* structured
* traceable
* tenant-aware

---

# 16. PERFORMANCE RULES

---

# 16.1 Scalability Principle

Reactive design MUST prioritize:

* scalability
* low thread consumption
* efficient resource usage

---

# 16.2 Thread Blocking Protection

Blocking event-loop threads is forbidden.

---

# 16.3 Heavy Computation Isolation

Heavy CPU operations SHOULD be:

* isolated
* controlled
* explicitly scheduled

---

# 17. TESTING RULES

---

# 17.1 Reactive Testing Standard

Reactive flows MUST use:

* StepVerifier
* reactive integration tests
* non-blocking validation

---

# 17.2 Blocking Tests Forbidden

Blocking assertions inside reactive tests are discouraged.

---

# 17.3 Context Validation

Tests SHOULD validate:

* tenant propagation
* security propagation
* Reactor Context integrity

---

# 18. FORBIDDEN REACTIVE ANTI-PATTERNS

---

# Forbidden

* .block()
* Thread.sleep()
* Blocking JDBC
* Imperative transaction leakage
* Manual uncontrolled subscriptions
* ThreadLocal reliance
* Massive collectList abuse
* Unbounded Flux streams
* Hidden side effects
* Context loss
* Event loop blocking
* Mixed imperative/reactive architecture

---

# 19. AI IMPLEMENTATION RULES

All AI-generated reactive code MUST:

* remain fully non-blocking
* preserve Reactor Context
* preserve tenant propagation
* preserve security propagation
* avoid blocking APIs
* avoid uncontrolled subscriptions
* preserve backpressure safety
* avoid memory explosion risks
* preserve reactive transaction integrity
* remain observable and traceable

---

# 20. REACTIVE DESIGN CHECKLIST

Before implementing reactive logic verify:

* Is the flow fully non-blocking?
* Is Reactor Context preserved?
* Is tenant context propagated?
* Is security context propagated?
* Are blocking APIs avoided?
* Is backpressure considered?
* Are subscriptions framework-owned?
* Is memory usage controlled?
* Are transactions reactive-safe?
* Is observability preserved?
* Are side effects controlled?
* Is event-loop blocking avoided?
* Are large datasets streamed safely?
* Is error handling explicit?
* Is the architecture consistently reactive?

---

# 21. CODECORE OFFICIAL REACTIVE PHILOSOPHY

```text id="16react16"
Reactive architecture exists to achieve
high scalability, efficient resource usage
and resilient asynchronous execution without
blocking system resources or breaking context integrity.
```
