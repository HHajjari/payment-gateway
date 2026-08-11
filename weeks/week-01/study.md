# Week 1 Study Guide

## Concepts to Learn

### Payment Domain

Understand in plain language:

- merchant
- cardholder/customer
- payment
- payment method
- authorization
- capture
- cancellation/void
- refund
- partial refund
- payment status/state transition

### Domain Modeling

Study:

- entity vs value object
- aggregate and aggregate root
- invariants
- domain service vs application service
- DTO vs domain model
- immutability
- value objects for IDs and Money

### Spring Boot Foundation

Study:

- dependency injection
- configuration
- controller/service boundaries
- bean lifecycle at a high level
- request validation
- exception-to-HTTP error mapping

### HTTP/API Fundamentals

Study:

- POST vs GET
- 200 vs 201
- 400 vs 404 vs 409
- request/response DTOs
- URI resource design

## Questions You Must Be Able to Answer

Write your answers below each question before Week 1 review.

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
