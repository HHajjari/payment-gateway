# Week 1 Failure Scenarios

Week 1 is intentionally simple, but correctness and explicit failure behavior begin now.

## Required Scenarios

### Invalid Money

Examples: zero/negative amount if your creation rules forbid it, malformed currency, unsupported currency if you choose a supported set.

Document expected behavior.

_TODO_

### Unknown Merchant

A payment creation request references a merchant that does not exist.

Expected domain/application/API behavior:

_TODO_

### Unknown Payment

A retrieval request references a payment that does not exist.

Expected behavior:

_TODO_

### Invalid Card Input

Synthetic card request is incomplete or malformed.

Expected behavior and validation boundary:

_TODO_

### Sensitive Data Leakage

Assume request validation or an exception fails. Identify places raw synthetic card details/CVV could leak: logs, exception messages, DTO `toString`, tracing, tests, etc.

Mitigation:

_TODO_

### Concurrent In-Memory Access

Even though concurrency is not the focus until later, consider whether the Week 1 temporary storage is safe when two HTTP requests arrive at once.

What assumption does your implementation make?

_TODO_
