# Value Object Standards

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Value Object Standards for CodeCore.

Its objectives are:

* standardize value modeling
* prevent unnecessary entities
* preserve domain expressiveness
* enforce immutability
* reduce accidental complexity
* improve domain clarity
* guide AI-assisted development
* maintain reactive-friendly domain design

This specification is mandatory for:

* domain modeling
* aggregate design
* entity design
* API modeling
* persistence modeling
* AI-generated implementations

---

# 2. VALUE OBJECT PHILOSOPHY

---

## 2.1 Official Definition

A Value Object is:

```text id="1jlwm3"
An immutable domain concept
defined entirely by its attributes
rather than identity.
```

---

## 2.2 Core Principle

Value Objects represent:

* meaning
* constraints
* descriptive concepts
* validation boundaries

NOT identity.

---

## 2.3 Identity Rule

Value Objects DO NOT possess:

* global identity
* lifecycle independence
* independent persistence ownership

---

# 3. VALUE OBJECT DESIGN PRINCIPLES

---

# 3.1 Immutability Principle

All Value Objects MUST be immutable.

After creation:

* no mutation allowed
* no state transitions allowed

---

## Forbidden

```text id="2jlwm4"
address.setCity()
money.setAmount()
email.change()
```

---

# 3.2 Equality by Value Principle

Value Objects MUST compare:

* by attributes
* by content
* by semantic equality

NOT by identity.

---

## Correct

```text id="3jlwm5"
emailA.equals(emailB)
```

when values match.

---

# 3.3 Self Validation Principle

Value Objects MUST validate themselves during creation.

Invalid Value Objects MUST NEVER exist.

---

## Correct

```text id="4jlwm6"
new EmailAddress(\"invalid\")
→ throws validation exception
```

---

# 3.4 Explicit Meaning Principle

Value Objects SHOULD express:

* domain meaning
* business intent
* semantic clarity

---

## Preferred

```text id="5jlwm7"
EmailAddress
PhoneNumber
DateRange
Money
FullName
```

---

## Avoid

```text id="6jlwm8"
String
Object
Map<String, Object>
```

for meaningful concepts.

---

# 4. WHEN TO USE VALUE OBJECTS

---

# 4.1 Use Value Objects When

Use a Value Object if:

* identity does not matter
* immutability is desired
* concept represents descriptive data
* equality is value-based
* concept encapsulates validation rules

---

# 4.2 Typical Value Object Candidates

Common examples:

```text id="7jlwm9"
EmailAddress
PhoneNumber
Address
Money
DateRange
FullName
Percentage
DocumentNumber
GeoLocation
TimeRange
PasswordHash
TenantIdentifier
```

---

# 4.3 Avoid Primitive Obsession

Primitive obsession is forbidden.

---

## Forbidden

```text id="8jlwm0"
String email
String phone
BigDecimal amount
```

when domain meaning exists.

---

## Preferred

```text id="9jlwm1"
EmailAddress email
PhoneNumber phone
Money amount
```

---

# 5. VALUE OBJECT STRUCTURE RULES

---

# 5.1 Small and Focused Principle

Value Objects MUST remain:

* cohesive
* lightweight
* focused

---

# 5.2 Explicit Construction Principle

Construction MUST:

* validate invariants
* guarantee valid state

---

# 5.3 No Empty States

Invalid or meaningless Value Objects MUST NOT exist.

---

## Forbidden

```text id="1jlwm2"
new EmailAddress(null)
new Money(null)
```

unless explicitly allowed by design.

---

# 5.4 Constructor Validation Rule

Validation MUST occur:

* immediately
* during creation
* before persistence

---

# 6. VALUE OBJECT PERSISTENCE RULES

---

# 6.1 Persistence Independence

Value Objects SHOULD NOT:

* own persistence lifecycle
* have repositories
* have standalone tables by default

---

# 6.2 Embedded Persistence Principle

Value Objects SHOULD be embedded:

* inside entities
* inside aggregates

---

# 6.3 Table Creation Rule

Creating standalone tables for Value Objects is discouraged unless:

* operationally justified
* query-heavy
* analytically required

---

# 6.4 Serialization Compatibility

Value Objects MUST remain:

* serialization-safe
* reactive-friendly
* lightweight

---

# 7. VALUE OBJECT BEHAVIOR RULES

---

# 7.1 Behavioral Encapsulation

Value Objects MAY contain:

* validation
* formatting
* calculations
* normalization
* semantic operations

---

## Correct

```text id="2jlwm3"
money.add()
dateRange.overlaps()
email.normalized()
```

---

# 7.2 Forbidden Responsibilities

Value Objects MUST NOT:

* access repositories
* access infrastructure
* orchestrate workflows
* depend on frameworks
* own transactions

---

# 7.3 Pure Logic Principle

Value Object logic SHOULD remain:

* deterministic
* side-effect free
* infrastructure independent

---

# 8. VALUE OBJECT IMMUTABILITY RULES

---

# 8.1 Immutable Fields

All internal fields SHOULD be:

* final
* immutable
* protected from mutation

---

# 8.2 Defensive Copying

Mutable collections inside Value Objects are discouraged.

If necessary:

* use defensive copies
* expose immutable views

---

# 8.3 No Setter Rule

Value Objects MUST NOT expose:

* public setters
* mutable internals

---

# 9. VALUE OBJECT NAMING RULES

---

# 9.1 Naming Style

Value Objects MUST:

* use singular nouns
* express domain meaning
* avoid technical ambiguity

---

## Correct

```text id="3jlwm4"
EmailAddress
PhoneNumber
Money
GeoLocation
DateRange
```

---

## Forbidden

```text id="4jlwm5"
DataObject
CommonValue
StringWrapper
```

---

# 9.2 Explicit Semantic Naming

Names SHOULD reflect:

* business meaning
* domain intent
* validation semantics

---

# 10. VALUE OBJECT VALIDATION RULES

---

# 10.1 Internal Validation Ownership

Validation MUST belong to the Value Object itself.

---

## Correct

```text id="5jlwm6"
EmailAddress validates email format internally
```

---

## Forbidden

```text id="6jlwm7"
External services validating EmailAddress integrity
```

---

# 10.2 Invariant Protection

Value Objects MUST guarantee:

* valid state
* invariant protection
* semantic consistency

---

# 10.3 Fail Fast Principle

Invalid construction MUST fail immediately.

---

# 11. VALUE OBJECT REUSABILITY RULES

---

# 11.1 Reusability Principle

Value Objects SHOULD remain:

* reusable
* business-agnostic
* context-safe

---

# 11.2 Shared Semantic Concepts

Universal concepts SHOULD become shared Value Objects.

---

## Examples

```text id="7jlwm8"
EmailAddress
Address
PhoneNumber
Money
DateRange
```

---

# 11.3 Business Specific Value Objects

Business modules MAY define:

* business-specific Value Objects

ONLY inside their bounded contexts.

---

# 12. REACTIVE DESIGN RULES

---

# 12.1 Lightweight Principle

Value Objects MUST remain:

* lightweight
* allocation-friendly
* reactive-compatible

---

# 12.2 Non Blocking Rule

Value Objects MUST NEVER:

* perform I/O
* block execution
* access databases
* call APIs

---

# 12.3 Pure Functional Compatibility

Value Objects SHOULD behave well in:

* reactive pipelines
* functional transformations
* asynchronous execution

---

# 13. MULTITENANCY RULES

---

# 13.1 Tenant Awareness Rule

Value Objects SHOULD NOT:

* directly own tenant lifecycle

unless explicitly modeling tenant identifiers.

---

# 13.2 Tenant Identifier Value Object

Tenant identifiers MAY be represented as Value Objects.

Example:

```text id="8jlwm9"
TenantIdentifier
```

---

# 14. FORBIDDEN VALUE OBJECT ANTI-PATTERNS

---

# Forbidden

* Mutable Value Objects
* Value Objects with repositories
* Infrastructure-aware Value Objects
* Framework-coupled Value Objects
* Entity-like Value Objects
* God Value Objects
* Anemic primitive wrappers without meaning
* Side-effect driven Value Objects
* Stateful lifecycle Value Objects

---

# 15. AI IMPLEMENTATION RULES

All AI-generated Value Objects MUST:

* be immutable
* self-validate
* avoid infrastructure coupling
* avoid repositories
* avoid unnecessary persistence
* preserve semantic clarity
* remain lightweight
* avoid primitive obsession
* remain reactive-friendly

---

# 16. VALUE OBJECT DESIGN CHECKLIST

Before implementing a Value Object verify:

* Does identity matter?
* Could immutability improve safety?
* Does the concept encapsulate meaning?
* Does the object self-validate?
* Is equality value-based?
* Is primitive obsession avoided?
* Is the structure lightweight?
* Is framework coupling avoided?
* Is the object reactive-friendly?
* Is naming semantically explicit?
* Are side effects avoided?
* Is persistence ownership unnecessary?
* Is the object reusable?
* Are invariants protected?

---

# 17. CODECORE OFFICIAL VALUE OBJECT PHILOSOPHY

```text id="9jlwm0"
Value Objects exist to encapsulate
meaning, validation and semantic consistency
without identity or lifecycle complexity.
```
