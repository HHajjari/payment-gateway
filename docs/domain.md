# Payment Gateway Domain

## Purpose

This repository is a year-long backend engineering project built around a simplified, production-oriented **card payment gateway**.

The gateway sits between a merchant and an external card processor. Its job is not merely to forward HTTP requests: it must make payment processing correct, reliable, secure, observable, and resilient when networks and dependencies fail.

The project uses **synthetic card/payment data only**. It does not process real money or connect to real card networks.

## Product in Simple Terms

A customer buys something from an online merchant. The merchant asks our gateway to process the payment. Our gateway communicates with a simulated external card processor and keeps track of the payment lifecycle.

```text
Customer
   |
   v
Merchant / Online Shop
   |
   | Payment API
   v
Our Payment Gateway
   |
   | Processor API
   v
External Processor Simulator
```

The **merchant** is our direct customer. The merchant's shopper/cardholder is not our direct customer.

## Core Actors

### Customer / Cardholder

The person buying goods or services from the merchant.

### Merchant

A business integrating with our payment API. A merchant creates payments, authorizes them, captures funds, issues refunds, retrieves payment information, and receives asynchronous notifications.

### Payment Gateway

The system we are building. It owns payment orchestration, lifecycle rules, idempotency, persistence, processor integration, events, webhooks, reconciliation, security, and later ledger/accounting concepts.

### External Card Processor

A separate simulator service that behaves like an external payment processor/acquirer. It will deliberately support successful and unsuccessful outcomes so that the gateway must handle distributed-system failures correctly.

## Core Payment Lifecycle

The initial conceptual lifecycle is:

```text
CREATED
   |
   | authorize
   v
AUTHORIZED --------> DECLINED
   |
   | capture
   v
CAPTURED
   |
   | refund
   v
PARTIALLY_REFUNDED
   |
   | remaining refund
   v
REFUNDED
```

The real model will evolve as requirements are introduced. Later we will consider states for asynchronous/ambiguous outcomes, authorization expiry, cancellation/reversal, multiple or partial captures, settlement, disputes, and reconciliation.

Do not treat this diagram as the final Java enum.

## Important Operations

The gateway will progressively support:

1. Create a payment.
2. Retrieve a payment.
3. Search/list a merchant's payments.
4. Authorize a payment.
5. Capture an authorized payment.
6. Support partial/multiple capture where the roadmap introduces it.
7. Cancel/void an authorization where applicable.
8. Refund a captured payment.
9. Support partial refunds.
10. Safely handle retries using idempotency.
11. Publish payment lifecycle events.
12. Deliver signed webhooks to merchants.
13. Reconcile gateway records against processor records.
14. Record financial movements using immutable ledger concepts.
15. Handle disputes/chargeback concepts in later payment-domain exercises.

## Core Domain Concepts

The model will evolve rather than being designed completely on day one. Important concepts include:

- **Merchant** — business using the gateway.
- **Payment** — merchant's intent to collect a monetary amount.
- **Money** — amount plus currency, modeled without floating-point arithmetic.
- **Payment Method** — synthetic card information or later a token representing it.
- **Payment Attempt** — an attempt to process an operation with an external processor.
- **Authorization** — processor approval/reservation of an amount.
- **Capture** — collection of an authorized amount.
- **Refund** — returning some or all captured funds.
- **Processor Transaction** — external processor's representation/reference for an operation.
- **Webhook Endpoint / Delivery** — merchant notification configuration and delivery attempts.
- **Ledger Entry** — later immutable accounting representation of financial movements.
- **Reconciliation Record** — later record of discrepancies between internal and processor state.

## Key Domain Rules

Examples of invariants we will eventually enforce include:

- A payment belongs to exactly one merchant.
- Monetary amounts must not use binary floating-point arithmetic.
- Currency is explicit.
- A merchant cannot access another merchant's payments.
- A payment cannot be captured unless the domain permits capture.
- Total captured amount cannot exceed the amount available for capture.
- Total refunded amount cannot exceed the captured amount available for refund.
- Repeated requests must not accidentally create repeated financial effects.
- External timeouts do not automatically mean an operation failed.
- Sensitive card data must not leak into logs, traces, events, or durable storage.

The exact invariants will be refined during implementation assignments.

## Card Data for the Learning Environment

To make the simulator realistic, early exercises may send **synthetic card fields** to the processor simulator.

Example shape:

```json
{
  "number": "synthetic-test-number",
  "expiryMonth": 12,
  "expiryYear": 2030,
  "cvv": "synthetic-test-value",
  "cardholderName": "Test Customer"
}
```

Rules:

- Never use real cardholder data.
- Never commit real credentials/card information to the repository.
- CVV must never be persisted.
- Raw card data must not appear in application logs, metrics, traces, Kafka events, or error messages.
- Later phases will introduce tokenization/masking so the gateway persists only safe references and metadata.

This project is an educational simulator and must not be represented as PCI-compliant payment infrastructure.

## Processor Simulator

The processor simulator will be a separate deployable service and therefore a real network dependency from the gateway's perspective.

```text
Payment Gateway
      |
      | HTTP (and later a legacy integration exercise)
      v
Processor Simulator
      |
      v
Processor-owned state
```

The gateway must not bypass the network boundary by directly calling simulator Java classes.

The simulator will progressively support operations such as:

- authorization
- capture
- cancellation/reversal
- refund
- transaction lookup/status
- reconciliation/reporting data

It will also support deterministic failure behavior such as:

- approved
- declined
- HTTP/server error
- slow response
- timeout
- malformed/unexpected response
- duplicate request handling
- successful processing followed by a lost response
- unknown/ambiguous outcome

Later we can add probabilistic failure profiles for load and resilience testing.

## Why Ambiguous Outcomes Matter

A timeout is not equivalent to failure.

```text
Gateway                 Processor
   |                         |
   |---- authorize --------->|
   |                         | authorization succeeds
   |                         |
   X<---- response lost -----|
```

The processor may have committed the operation while the gateway received no response. Retrying blindly can create duplicate financial effects.

This problem drives several major topics in the roadmap: idempotency, retries, transaction boundaries, reconciliation, observability, and distributed-system reasoning.

## Backing Systems

Backing systems are introduced only when the requirements justify them.

### PostgreSQL

Primary durable transactional store for merchants, payments, attempts, idempotency state, outbox records, webhook deliveries, reconciliation data, and later ledger data as appropriate.

### Redis

Introduced for appropriate ephemeral/distributed concerns such as caching and distributed rate limiting. PostgreSQL remains the durable source of truth for payment state unless an explicit design exercise changes that assumption.

### Kafka

Introduced for asynchronous payment-domain events and consumers. It creates exercises around delivery semantics, ordering, duplicates, retries, schema evolution, and the database/event dual-write problem.

### Search / Reporting Store

A later exercise may introduce Elasticsearch or a similar search-oriented system so operational search/reporting workloads do not have to be solved exclusively through the transactional database.

## External Dependencies

The important external dependencies will include:

- processor simulator
- merchant webhook endpoints
- authentication/identity components where introduced
- cloud-managed infrastructure in later phases

External dependencies should be assumed to fail, become slow, return unexpected data, or produce ambiguous outcomes.

## Failure Injection

Failure simulation evolves in stages:

### Application failures

The processor simulator deliberately returns declines, errors, delays, malformed responses, and ambiguous outcomes.

### Network/infrastructure failures

Later we introduce tools such as Toxiproxy and container-level failure experiments to add latency, connection resets, unavailable dependencies, and resource exhaustion.

### Distributed production-style failures

Later exercises include Kafka/Redis/PostgreSQL failures, consumer crashes, Kubernetes pod termination, rolling deployments, saturation, retry storms, and partial outages.

## European Payment Concepts

Because the project is intended to provide serious payment-platform experience, later domain phases will cover concepts including:

- 3-D Secure / 3DS2
- PSD2 / Strong Customer Authentication concepts
- authentication versus authorization
- authorization expiry
- partial/multiple captures
- reversals/cancellations
- settlement
- reconciliation
- disputes/chargebacks

These are simulated concepts; the project does not connect to actual schemes or financial institutions.

## Explicitly Out of Scope

Unless the roadmap is deliberately extended, we are not building:

- a real bank
- a real card network
- real Visa/Mastercard integrations
- real money movement
- PCI-certified infrastructure
- merchant onboarding/KYC as a complete product
- a full consumer checkout frontend
- a complete fraud-detection platform
- a complete accounting/ERP platform

The purpose is to build a backend engineering environment rich enough to practice Senior/Staff-level Java and distributed-systems problems.

## Long-Term Architecture Direction

The architecture should earn complexity over time rather than starting as microservices.

```text
Merchant
   |
   v
Payment API
   |
Payment Domain / Orchestration
   |                \
   v                 v
PostgreSQL       Processor Simulator
   |
Outbox
   |
Kafka ----> Webhooks / Async Workers / Ledger projections

Redis: selected cache/rate-limit use cases
Observability: metrics + logs + traces
Infrastructure: Docker -> Kubernetes -> Cloud -> Terraform
```

This is a direction, not a Week 1 implementation prescription.

## Engineering Principle

The central question for the entire project is:

> How do we make payment processing correct, reliable, secure, observable, scalable, and understandable when dependencies and networks fail?

Technology choices exist to answer that question; they are not goals by themselves.
