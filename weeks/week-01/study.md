# Week 1 Study Guide

## Goal

Before writing the main implementation, understand the payment domain and the architectural boundaries well enough that your Spring Boot code does not collapse into controllers, services, entities, and DTOs that all know too much about each other.

This week is not about memorizing definitions. You should be able to explain the concepts below in your own words and use them to justify your design.

---

# 1. Payment Domain Fundamentals

## Topics to Read

- merchant
- customer/cardholder
- payment
- payment method
- authorization
- capture
- cancellation/void/reversal
- refund
- partial refund
- payment status/state transition
- payment attempt

## What You Should Understand

A **merchant** is the business integrating with our gateway. The shopper/cardholder buys from the merchant; the merchant is our direct API customer.

A **payment** represents the merchant's intent to collect money. Creating a payment does not necessarily mean money has been collected.

**Authorization** asks the external processor whether a monetary amount can be approved/reserved.

**Capture** collects an amount that was previously authorized. Authorization and capture can be separate because merchants often want to verify/approve funds before actually collecting them—for example, capture only after an item ships.

A **cancellation/void/reversal** releases or cancels an authorization before capture, depending on processor/payment semantics.

A **refund** returns previously captured funds. A refund can be full or partial.

A **payment attempt** represents an individual attempt to perform an operation with an external processor. Later this becomes important because a single logical payment can involve retries or ambiguous processor outcomes.

## Depth Expected This Week

You do not need to understand card-network internals yet. You should be able to describe a basic lifecycle such as:

```text
CREATED
   |
   | authorize
   v
AUTHORIZED
   |
   | capture
   v
CAPTURED
   |
   | refund
   v
REFUNDED
```

You should also understand why this is only an initial model and not necessarily the final state machine.

---

# 2. Domain Modeling

## Topics to Read

- entity
- value object
- aggregate
- aggregate root
- invariant
- domain service
- application service
- DTO vs domain model
- immutability
- rich domain model vs anemic model

## What You Should Understand

### Entity

An entity has identity that matters over time. Two `Payment` objects with identical values can still represent different payments because they have different identities.

### Value Object

A value object is primarily defined by its value rather than identity. `Money(amount, currency)` is a strong candidate because two values representing the same amount and currency can normally be considered equivalent.

### Aggregate / Aggregate Root

An aggregate is a consistency boundary around related domain objects. The aggregate root is the object through which outside code modifies that aggregate.

You do not need to force DDD terminology into every class this week. The important question is: **where should invariants be protected?**

### Invariant

An invariant is something that must always remain true for a valid domain object.

Examples you should think about:

- amount cannot accidentally be invalid
- currency must be present/valid
- payment must belong to a merchant
- sensitive card data must not become durable state by accident

### Application Service vs Domain Logic

An application service coordinates a use case:

```text
receive command
    -> load/check dependencies
    -> ask domain object to perform behavior
    -> save result
    -> return result
```

It should not become the place where every business rule is encoded.

### DTO vs Domain Model

An API request DTO represents the external HTTP contract. The domain model represents business concepts and rules.

They may look similar, especially early on, but they serve different purposes and should not be treated as automatically identical.

## Questions to Ask While Reading

- If PostgreSQL is replaced, should the `Payment` business rules change?
- If the HTTP API changes, should the core `Money` rules change?
- Which class should stop invalid payment state from being created?

---

# 3. Money Modeling in Java

## Topics to Read

- why `double` and `float` are unsafe for financial amounts
- decimal arithmetic
- `BigDecimal`
- representing money in minor units with integer types
- `java.util.Currency`
- currency-specific fraction digits
- rounding modes
- equality issues with `BigDecimal`

## What You Should Understand

Binary floating-point cannot exactly represent many decimal values. That makes it a poor choice for financial values where exact arithmetic matters.

Two common approaches are:

### Minor Units

```text
€12.34 -> 1234 cents
```

represented as an integer/long plus currency.

### Decimal Representation

Represent the decimal value using `BigDecimal` plus currency.

For Week 1, you must choose one and explain your tradeoff. I will review the decision rather than prescribing the answer.

If you choose `BigDecimal`, specifically understand:

- scale
- rounding
- why `equals()` and `compareTo()` behave differently

---

# 4. Spring Boot Architecture

## Topics to Read

- dependency injection / inversion of control
- Spring beans
- constructor injection
- `@RestController`
- `@Service` / component stereotypes
- configuration properties at a high level
- validation with Jakarta Bean Validation
- exception handling with `@ControllerAdvice`
- why business logic should not live in controllers

## What You Should Understand

Spring should wire your application together; your domain should not need Spring in order to express basic business rules.

Prefer dependencies flowing inward conceptually:

```text
HTTP / Spring
     |
     v
Application use cases
     |
     v
Domain
```

Infrastructure should support the domain, rather than the domain becoming dependent on HTTP/database details.

For Week 1, be able to explain what belongs in:

- controller
- application/use-case service
- domain model
- repository/storage abstraction
- in-memory implementation

---

# 5. Dependency Inversion and Repository Boundaries

## Topics to Read

- dependency inversion principle
- ports and adapters / hexagonal architecture at a basic level
- repository abstraction
- interface vs implementation
- avoiding framework leakage

## What You Should Understand

This week we may use in-memory storage. Next week PostgreSQL arrives.

A useful design goal is:

```text
Application
    |
    v
PaymentRepository interface
    ^
    |
InMemoryPaymentRepository
```

Then Week 2 can potentially introduce:

```text
PostgresPaymentRepository
```

without rewriting the business use case.

Do not create interfaces merely because interfaces are considered "clean architecture." Create a boundary when it protects the application/domain from a detail that is expected to vary.

---

# 6. HTTP and REST Fundamentals

## Topics to Read

- resource-oriented API design
- POST vs GET
- `201 Created`
- `200 OK`
- `400 Bad Request`
- `404 Not Found`
- `409 Conflict`
- request/response DTOs
- validation errors
- stable error contracts
- URI naming

## What You Should Understand

For Week 1, consider endpoints conceptually like:

```text
POST /merchants
POST /payments
GET  /payments/{paymentId}
```

Do not copy these blindly if your design leads to a justified alternative.

You should understand why successful resource creation commonly returns `201`, why invalid input is different from a missing resource, and why Java exceptions should not accidentally determine your public API contract.

---

# 7. Input Validation vs Domain Validation

## Topics to Read

- syntactic validation
- semantic/business validation
- Jakarta Bean Validation
- defensive domain construction

## What You Should Understand

There are different kinds of validation.

Example:

```text
amount field missing
```

is primarily request/input validation.

But:

```text
payment amount violates a domain invariant
```

must also be impossible to create inside the domain even if someone bypasses the controller.

You should avoid depending exclusively on `@Valid` to keep your domain correct.

---

# 8. Card Data and Sensitive Information

## Topics to Read

- PAN/card number terminology
- CVV/CVC terminology
- masking
- tokenization concept
- sensitive-data logging risks
- Java record/class `toString()` leakage
- request logging
- exception logging

## What You Should Understand

Our project uses synthetic data only, but we intentionally treat it as sensitive so you practice safe engineering habits.

Raw card fields can leak through:

- application logs
- automatic request logging
- DTO `toString()` methods
- exception messages
- tracing attributes
- test output
- Kafka events later
- database persistence later

CVV must not become durable state.

Think carefully about whether your `Payment` domain object should contain the raw request card fields at all.

---

# 9. IDs and Identity

## Topics to Read

- UUID
- database-generated IDs vs application-generated IDs
- opaque public identifiers
- typed IDs/value objects

## What You Should Understand

We need IDs for at least:

- merchant
- payment

Think about why generating a payment ID in the application may become useful before calling storage or an external processor.

You do not need to solve globally distributed ID generation yet.

---

# 10. Immutability and State Transitions

## Topics to Read

- immutable value objects
- encapsulation
- avoiding public setters
- state transition methods
- constructors/factory methods

## What You Should Understand

Compare:

```java
payment.setStatus(CAPTURED);
```

with a domain operation conceptually like:

```java
payment.capture(...);
```

The second form creates a place to protect business rules.

Do not implement the full capture workflow this week, but understand why uncontrolled setters become dangerous as the domain grows.

---

# Recommended Study Order

Use this order before implementing the main solution:

1. Payment lifecycle terminology.
2. Entity vs value object and invariants.
3. Money representation in Java.
4. DTO vs domain model.
5. Controller/application/domain responsibilities.
6. Dependency inversion and repository boundaries.
7. HTTP semantics and validation.
8. Sensitive card-data handling.
9. ID strategy.
10. Immutability/state-transition design.

You do not need to spend equal time on each topic. Payment domain, Money, domain boundaries, and Spring layering are the highest priority for Week 1.

---

# What You Should Know Before Coding

Before starting the implementation, you should be able to explain the following without copying definitions:

- who the merchant is and who the cardholder is
- what a Payment represents
- authorization vs capture
- why `Payment` has identity
- why `Money` can be modeled as a value object
- why `double` is inappropriate for money
- what belongs in a controller
- what belongs in an application service
- what belongs in the domain
- why the in-memory store is infrastructure, not business logic
- why DTOs and domain objects are different concepts
- where synthetic card details could leak
- why CVV should never be persisted
- what you expect to replace when PostgreSQL is added in Week 2

---

# Questions You Must Answer

Write your own answer underneath each question. Do not use generated answers verbatim; these are part of the Week 1 review.

### 1. What is the difference between authorization and capture?

_TODO_

### 2. Why can authorization and capture be separate operations?

_TODO_

### 3. What makes `Payment` an entity?

_TODO_

### 4. What makes `Money` a good value-object candidate?

_TODO_

### 5. Why is floating-point arithmetic dangerous for monetary amounts?

_TODO_

### 6. Which payment rules should remain true regardless of whether storage is memory, PostgreSQL, or another database?

_TODO_

### 7. What information is sensitive in a card request, and where could it leak?

_TODO_

### 8. What is the difference between an API DTO and the domain model?

_TODO_

### 9. Which dependency should point toward which: domain -> infrastructure or infrastructure -> domain? Why?

_TODO_

### 10. Which parts of Week 1 should survive unchanged when PostgreSQL is introduced?

_TODO_

---

# End-of-Study Self Check

Do not begin the main implementation until you can confidently discuss at least **8/10** questions above.

You do not need perfect textbook language. You need correct reasoning.
