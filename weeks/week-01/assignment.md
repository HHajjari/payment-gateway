# Week 1 Assignment

## Objective

Create the first clean version of the payment gateway as a Spring Boot application while keeping the domain model independent from infrastructure concerns.

## Functional Scope

For Week 1, implement only:

1. Create a merchant.
2. Create a payment for a merchant.
3. Retrieve a payment by ID.
4. Represent the initial payment lifecycle in the domain.

Do **not** implement PostgreSQL, Redis, Kafka, Docker, Kubernetes, authentication, real processor communication, or real card processing this week.

## Payment Creation

A payment should conceptually contain:

- gateway-generated payment ID
- merchant ID
- amount
- currency
- synthetic payment method/card information or a safe representation chosen in your design
- current payment status
- creation timestamp

You must decide the exact Java types and boundaries.

## Constraints

- Do not represent money using `double` or `float`.
- Domain objects must not depend on Spring MVC or persistence annotations unless you can strongly justify it.
- Controllers must not contain business rules.
- Avoid unnecessary abstractions and premature microservices.
- Use synthetic card data only.
- Do not persist CVV or log sensitive card fields.
- Invalid input must be rejected intentionally rather than failing accidentally.

## Architecture Deliverable

Before coding the implementation, write your proposal in `architecture.md` covering:

- packages/modules
- main domain objects
- responsibilities of controller/application/domain layers
- how payment IDs are generated
- how data is stored temporarily during Week 1
- how you will prevent sensitive card data from being retained accidentally

## Implementation Deliverable

A running Spring Boot service with merchant/payment creation and payment retrieval endpoints, backed only by the simplest Week 1 storage mechanism you can justify.

## Senior-Engineer Questions

Be prepared to answer:

1. Why did you choose your representation for Money?
2. What makes Payment an entity rather than a value object?
3. Which invariants belong inside the domain model?
4. Why is your controller not responsible for those invariants?
5. What parts of your design will likely change when PostgreSQL arrives in Week 2?
6. Which parts should *not* need to change when PostgreSQL arrives?
7. Where could sensitive card information accidentally leak?
