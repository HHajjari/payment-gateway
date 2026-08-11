# 52-Week Java Backend Engineering Roadmap

## Mission

Build one production-oriented **card payment gateway** throughout the year while progressing from mid-level Java/Spring development toward strong Senior backend engineering and Staff-level technical reasoning.

The roadmap is deliberately cumulative. Each week changes or extends the same system. Later requirements will expose weaknesses in earlier designs and require migrations, refactoring, operational investigation, and architectural tradeoffs.

The roadmap is also payment-platform oriented, with additional emphasis on skills relevant to large European payment companies: Java, explicit SQL, PostgreSQL, distributed systems, payment lifecycle depth, 3DS/SCA, messaging, reconciliation, accounting, production debugging, reliability, and technical leadership.

## Working Rules

For every week:

1. Study the required concepts.
2. Design before implementing when architecture is involved.
3. Implement the assignment yourself.
4. Define and handle failure scenarios.
5. Implement required tests.
6. Meet explicit acceptance criteria.
7. Submit code/design for review.
8. A week is not complete merely because seven days passed.
9. Working code with unresolved correctness, concurrency, security, or reliability problems may be marked **Needs Revision**.
10. Complete solutions are not provided immediately unless explicitly requested.

## Progress

- Current week: **Week 1**
- Status: **Not started**
- Completed: **0 / 52**

---

# Phase 1 — Payment Domain and Java Foundations
## Weeks 1–8

### Week 1 — Payment Domain & Spring Boot Foundation

**Study:** Spring Boot architecture, dependency injection, configuration, domain modeling, DTO/domain separation, package/module boundaries, payment authorization/capture/refund terminology, HTTP fundamentals.

**Build:** Bootstrap the payment gateway and model the first version of Merchant, Payment, Money, and synthetic Payment Method concepts. Implement basic payment creation/retrieval without overengineering the final architecture.

**Challenge:** Define clear domain boundaries while knowing requirements will evolve.

**Tests:** Domain unit tests, validation tests, basic API tests.

**Deliverable:** Running Spring Boot application plus first architecture/domain notes.

### Week 2 — PostgreSQL & Financial Data Modeling

**Study:** PostgreSQL tables, constraints, indexes, normalization, MVCC, transactions, Flyway, JPA persistence context, monetary representation.

**Build:** Persist merchants, payments, and payment attempts using PostgreSQL and migrations.

**Challenge:** Correctly represent money/currency and database invariants.

**Tests:** Testcontainers-based repository/integration tests and constraint tests.

### Week 3 — Transactions & Payment State Machine

**Study:** ACID, Spring transactions, propagation, isolation, optimistic/pessimistic locking, lost updates.

**Build:** Implement controlled payment state transitions and transaction boundaries.

**Challenge:** Concurrent modifications and illegal transitions.

**Tests:** State-machine tests plus concurrent update integration tests.

### Week 4 — Advanced Java Domain Modeling

**Study:** records, sealed types, generics, immutability, collections, equality, exceptions, Optional, Streams, value objects.

**Build:** Strengthen the domain model so invalid values/states are harder to represent.

**Challenge:** Decide where primitives, records, entities, aggregates, and domain services belong.

### Week 5 — External Processor Simulator & Java Concurrency

**Study:** Java Memory Model, threads, synchronization, atomics, locks, executors, CompletableFuture, virtual threads, HTTP-client behavior.

**Build:** Create a separate **processor-simulator** Spring Boot application. Gateway-to-processor communication must cross an HTTP boundary. Implement deterministic authorization success/decline behavior using synthetic card data.

**Challenge:** Concurrent authorization requests and isolation between the gateway and external processor.

**Tests:** Processor contract/integration tests and concurrency tests.

### Week 6 — Production API Design

**Study:** REST, HTTP semantics, resource modeling, validation, status codes, error contracts, pagination, API versioning, OpenAPI.

**Build:** Merchant-facing create/retrieve/authorize/capture/cancel/refund API shape.

**Challenge:** Design an API another company could safely integrate with.

### Week 7 — Testing Strategy

**Study:** JUnit 5, Mockito, Testcontainers, test pyramid, component tests, contract tests, deterministic testing, fixtures.

**Build:** Establish the project testing strategy and improve coverage around domain, database, and processor integration.

**Challenge:** Decide what should and should not be mocked.

### Week 8 — Idempotency & Duplicate Financial Requests

**Study:** at-least-once behavior, idempotency keys, request fingerprints, replay semantics, races.

**Build:** Idempotent create and money-moving operations; processor simulator also receives/understands stable operation references.

**Failure:** Merchant retries the same operation repeatedly after timeouts.

**Acceptance focus:** One logical financial effect.

---

# Phase 2 — PostgreSQL, SQL, JVM & Performance
## Weeks 9–15

### Week 9 — Explicit SQL & Query Engineering

**Study:** EXPLAIN/EXPLAIN ANALYZE, query planner, indexes, statistics, joins, composite/covering indexes, sequential scans.

**Build:** Implement high-value payment queries using explicit SQL via JdbcTemplate/MyBatis-style mapping rather than relying entirely on ORM-generated SQL.

**Challenge:** Understand and control the SQL executed in production.

### Week 10 — Pagination & Operational Search

**Study:** offset vs keyset pagination, stable ordering, filtering strategies.

**Build:** Merchant payment-history/search API over a large generated dataset.

**Challenge:** Maintain predictable latency as data grows.

### Week 11 — Connection Pools & Database Saturation

**Study:** HikariCP, connection costs, pool sizing, contention, timeouts, database capacity.

**Build:** Load test the gateway and intentionally exhaust database resources.

**Challenge:** Diagnose pool exhaustion rather than blindly increasing pool size.

### Week 12 — JVM Internals

**Study:** heap/stack, allocation, object headers, class loading, JIT, escape analysis, GC roots.

**Build:** Profile the gateway under load and identify allocation/CPU hotspots.

### Week 13 — Garbage Collection

**Study:** G1GC, ZGC, pause vs throughput, heap sizing, GC logs.

**Build:** Compare JVM configurations under repeatable load.

**Deliverable:** Performance experiment report.

### Week 14 — Linux & JVM Production Debugging

**Study:** processes, threads, file descriptors, sockets, DNS, TLS basics, CPU/IO pressure, `ps`, `top`, `ss`, `lsof`, `jstack`, heap dumps, `/proc`, container limits.

**Build:** Diagnose deliberately introduced runtime failures using operational evidence rather than IDE debugging.

### Week 15 — Performance Engineering & Capacity Baseline

**Study:** throughput, latency percentiles, Little's Law, coordinated omission, benchmarking methodology.

**Build:** Establish baseline authorization SLO-oriented performance numbers and identify the first bottleneck.

---

# Phase 3 — Redis & Distributed State
## Weeks 16–19

### Week 16 — Redis Fundamentals

**Study:** data structures, TTL, eviction, persistence, failure characteristics.

**Build:** Introduce Redis only for a justified temporary/distributed use case.

### Week 17 — Distributed Rate Limiting

**Study:** token bucket, sliding windows, distributed counters, atomic Redis operations.

**Build:** Per-merchant distributed API rate limiting across multiple gateway instances.

### Week 18 — Caching & Invalidation

**Study:** cache-aside, stale reads, invalidation, cache stampede.

**Build:** Cache merchant configuration with explicit freshness semantics.

### Week 19 — Redis Failure & Graceful Degradation

**Build:** Make Redis unavailable while payment traffic continues.

**Challenge:** Decide which gateway capabilities must continue and which may degrade.

---

# Phase 4 — Kafka & Event-Driven Architecture
## Weeks 20–27

### Week 20 — Kafka Fundamentals

**Study:** brokers, partitions, offsets, consumer groups, replication.

**Build:** Publish payment lifecycle events.

### Week 21 — Event Contracts & Schema Evolution

**Study:** event envelopes, compatibility, schema evolution, domain events vs integration events.

**Build:** Versioned contracts for authorization/capture/refund events.

### Week 22 — Transactional Outbox

**Study:** database/message dual-write problem.

**Build:** Transactional outbox and reliable event publication.

**Failure:** PostgreSQL commits while Kafka publication fails.

### Week 23 — Consumers & Idempotency

**Study:** delivery semantics and consumer-side deduplication.

**Build:** Idempotent payment-event consumer.

### Week 24 — Retries, Backoff & Dead Letters

**Study:** exponential backoff, jitter, poison messages, retry ownership.

**Build:** Failure-handling policy for asynchronous consumers.

### Week 25 — Ordering & Partition Strategy

**Study:** Kafka ordering guarantees, keys, partitioning, rebalancing.

**Challenge:** Preserve meaningful ordering for operations on the same payment without pretending Kafka offers global ordering.

### Week 26 — Merchant Webhooks

**Build:** Signed asynchronous webhooks, retry policy, delivery history, idempotent delivery identifiers.

**Failure:** Merchant processes webhook but acknowledgement is lost.

### Week 27 — Kafka Failure Lab & Queue Comparison

**Study:** Kafka vs traditional queues; RabbitMQ concepts/exchange-routing/acknowledgement semantics.

**Build/Experiment:** Kafka outage, slow consumers, crash, duplicates, malformed events, rebalance. Produce a design note explaining where Kafka and RabbitMQ-style queues differ and when each model fits.

---

# Phase 5 — Payment Processing & Distributed Systems
## Weeks 28–36

### Week 28 — Advanced Processor Integration

**Study:** external dependency contracts, timeout budgets, ambiguous outcomes.

**Build:** Upgrade processor simulator with slow responses, server errors, timeouts, unknown outcomes, stable processor transaction references, and transaction-status lookup.

**Key lesson:** Timeout does not mean failure.

### Week 29 — Resilience Patterns

**Study:** timeout, retry, exponential backoff, circuit breaker, bulkhead, backpressure.

**Build:** Harden processor integration with Resilience4j where justified.

**Challenge:** Prevent retry storms and duplicate effects.

### Week 30 — Network Failure Injection

**Study:** partial failure and network uncertainty.

**Build:** Introduce Toxiproxy or equivalent controlled network faults between gateway and processor.

**Experiments:** latency, connection reset, unavailable dependency, intermittent failure.

### Week 31 — Distributed Consistency & Saga Reasoning

**Study:** strong/eventual consistency, CAP limitations, distributed transactions, sagas, compensation.

**Build:** Design and implement one multi-step workflow crossing component/service boundaries.

### Week 32 — Payment Lifecycle Depth

**Study:** authorization expiry, reversals, partial/multiple captures, asynchronous states, processor references.

**Build:** Extend the payment domain beyond a simplistic CREATED/AUTHORIZED/CAPTURED model.

**Challenge:** Preserve invariants through partial operations.

### Week 33 — 3-D Secure, PSD2 & SCA Simulation

**Study:** 3DS2 concepts, authentication vs authorization, PSD2/SCA concepts, challenge/frictionless flows.

**Build:** Simulate a payment that requires an authentication step before authorization.

**Scope:** Educational simulation only; no real scheme integration.

### Week 34 — Reconciliation

**Study:** internal vs processor source-of-truth boundaries, reconciliation jobs, discrepancy classification.

**Build:** Processor reporting/status API plus reconciliation process that discovers divergent payment states.

### Week 35 — Ledger, Settlement & Accounting Foundations

**Study:** double-entry accounting concepts, immutable financial records, balances, debit/credit, settlement, fees, booking/value concepts.

**Build:** Simplified immutable ledger and settlement-oriented records.

**Challenge:** A mutable `payment.status` must not be mistaken for accounting truth.

### Week 36 — Disputes, Chargebacks & Payment Failure Lab

**Study:** dispute/chargeback lifecycle concepts and operational handling.

**Build:** Simulate a simplified dispute flow and run a distributed failure exercise covering ambiguous processor outcomes, duplicates, crashes, and reconciliation.

**Deliverable:** Incident analysis.

---

# Phase 6 — Security & Legacy Integration
## Weeks 37–40

### Week 37 — Authentication & Authorization

**Study:** OAuth2/OIDC concepts, JWT/API credentials, scopes, RBAC/resource ownership, least privilege.

**Build:** Merchant authentication and strict tenant/resource isolation.

### Week 38 — Payment Data Security

**Study:** PCI DSS concepts, tokenization, encryption, TLS, secret management, log redaction.

**Build:** Audit synthetic card-data flow. Ensure sensitive fields cannot leak into logs, traces, events, exceptions, or durable storage. Introduce safer tokenized flows.

### Week 39 — Threat Modeling

**Study:** STRIDE, abuse cases, replay, enumeration, webhook forgery, credential theft, privilege escalation.

**Build:** Threat model plus mitigations and security tests.

### Week 40 — Legacy SOAP Processor Integration

**Study:** SOAP/WSDL/XML, fault handling, schema contracts, legacy integration boundaries.

**Build:** Add a small legacy SOAP-style processor integration/simulator path and compare its operational/development tradeoffs with REST.

---

# Phase 7 — Observability, Search & Reliability
## Weeks 41–44

### Week 41 — Metrics, Logs & Traces

**Study:** Micrometer, Prometheus, structured logging, correlation IDs, OpenTelemetry, RED/USE methods.

**Build:** End-to-end observability across gateway, processor calls, Kafka, and webhooks.

### Week 42 — Elasticsearch / Operational Search

**Study:** inverted indexes, mappings, denormalized search documents, eventual consistency, OLTP vs search workloads.

**Build:** Project payment operational data into Elasticsearch (or equivalent) for search/support use cases.

**Challenge:** PostgreSQL remains transactional truth while search is eventually consistent.

### Week 43 — SLOs, Alerting & Incident Response

**Study:** SLA/SLI/SLO, error budgets, availability, latency percentiles, symptom-based alerting.

**Build:** Define payment authorization SLOs and alerts. Run an incident using telemetry only.

### Week 44 — Distributed SQL / CockroachDB Study

**Study:** distributed SQL, consensus/replication concepts, serializable transactions, contention, locality, tradeoffs versus single-primary PostgreSQL.

**Build/Experiment:** Small CockroachDB-oriented experiment or design exercise comparing transaction and operational assumptions with PostgreSQL.

---

# Phase 8 — Containers, Kubernetes, Cloud & Infrastructure
## Weeks 45–48

### Week 45 — Docker & Local Production Topology

**Study:** images, layers, multi-stage builds, networking, security, resource constraints.

**Build:** Containerize gateway, processor simulator, and required backing services.

### Week 46 — Kubernetes & Zero-Downtime Operation

**Study:** Deployments, Services, ConfigMaps, Secrets, probes, requests/limits, rolling updates, graceful shutdown, autoscaling.

**Build:** Deploy the platform and perform rolling changes while payment traffic is active.

### Week 47 — Cloud Architecture & Terraform

**Study:** networking, managed database/cache/messaging, IAM, secret management, Terraform state/modules/environments.

**Build:** Design and provision an appropriate cloud environment with Terraform, keeping cost controlled.

### Week 48 — CI/CD, Migrations & Release Engineering

**Study:** pipelines, deployment strategies, rollback/roll-forward, database migration safety, backward compatibility.

**Build:** Automated build/test/deploy pipeline and a zero-downtime schema/application migration exercise.

---

# Phase 9 — Senior/Staff System Design & Technical Leadership
## Weeks 49–51

### Week 49 — Scale 100× & Capacity Planning

**Requirement:** Gateway must handle 100× baseline traffic.

**Analyze:** application instances, PostgreSQL, Redis, Kafka partitions, processor limits, connection pools, storage, network, cost.

**Deliverable:** Capacity model and scaling proposal before code changes.

### Week 50 — Multi-Region & Disaster Recovery

**Requirement:** A regional outage must not completely stop payment processing.

**Study/Design:** active-active vs active-passive, replication, consistency, failover, RPO/RTO, split brain, regional processor dependencies.

**Deliverable:** Architecture proposal and failure model; implementation only where educationally justified.

### Week 51 — Architecture Review & Senior Leadership Simulation

**Deliverables:** architecture diagrams, ADRs, RFC, capacity estimates, security model, SLOs, data model, deployment model, known limitations, migration strategy.

**Leadership exercises:**

- defend architecture in review
- review a fictional engineer's design/change
- prioritize technical debt
- write a stakeholder-friendly technical summary
- propose a migration with risks/rollback
- mentor through a design problem rather than simply rewriting it

---

# Phase 10 — AI Engineering & Final Production Exercise
## Week 52

### Week 52 — AI Operations Assistant + Final Incident

**Study:** LLM APIs, structured outputs, tool calling, embeddings/RAG concepts, evaluation, hallucination, prompt injection, latency/cost, AI observability.

**Build:** Add an AI-assisted operational capability using sanitized operational data. AI must not autonomously make financial decisions.

**Final incident:** Authorization success rate falls while latency increases. Investigate incomplete telemetry, form hypotheses, mitigate customer impact, determine root cause, verify payment correctness, implement/propose permanent remediation, and write a postmortem.

**Final review:** Defend the complete system as if presenting to a senior architecture panel.

---

# Recurring Senior-Level Expectations

These are not isolated weeks. They apply throughout the roadmap.

## Architecture Decisions

Important decisions should be documented with context, alternatives, decision, consequences, and conditions that would cause reconsideration.

## Failure First

For every new dependency ask:

- What if it is unavailable?
- What if it is slow?
- What if the request succeeds but the response is lost?
- What if the same request/event arrives twice?
- What if responses arrive out of order?
- What if only part of the workflow succeeds?

## Production Debugging

Do not rely exclusively on IDE debugging. Later phases require diagnosis from logs, metrics, traces, thread dumps, database evidence, broker state, and infrastructure telemetry.

## Communication

Architecture must be explainable to engineers and non-specialist stakeholders. Later reviews will assess not only correctness but clarity and decision quality.

## No Premature Microservices

The project starts with the simplest architecture that satisfies the current requirements. Service boundaries are introduced only when there is a concrete reason.

---

# Target End State

By completion, the repository should contain evidence of work across:

- advanced Java and Spring
- JVM/performance engineering
- PostgreSQL and explicit SQL
- Redis
- Kafka and queueing concepts
- event-driven architecture
- distributed systems and resilience
- payment lifecycle/domain depth
- reconciliation and ledger/accounting foundations
- 3DS/SCA concepts
- security
- SOAP/legacy integration
- Elasticsearch/search
- Linux production debugging
- Docker/Kubernetes
- cloud/Terraform
- CI/CD and migrations
- observability/SLOs/incidents
- capacity planning and multi-region design
- AI engineering
- architecture and technical-leadership communication

The graduation standard is not simply reaching Week 52. The goal is to be able to **design, implement, test, operate, troubleshoot, and defend a sophisticated Java distributed backend without having the architecture prescribed in advance**.
