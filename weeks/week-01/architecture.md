# Week 1 Architecture

Complete this **before implementing the main code**.

## Proposed Package / Module Structure

_TODO_

## Main Domain Objects

List the objects you believe belong in the first domain model and give each one a one-sentence responsibility.

_TODO_

## Layer Responsibilities

### API / Controller

_TODO_

### Application / Use Cases

_TODO_

### Domain

_TODO_

### Temporary Storage Adapter

_TODO_

## Payment Creation Flow

Describe the flow from incoming HTTP request to returned response.

_TODO_

## Merchant Creation Flow

_TODO_

## ID Strategy

How will Merchant and Payment IDs be generated? Why?

_TODO_

## Money Representation

What Java representation will you use for amount and currency? Why?

_TODO_

## Payment Method / Card Boundary

What card fields enter the API? Which fields, if any, may reach the domain model? Which fields may be stored? Explain how CVV and other sensitive values are prevented from leaking.

_TODO_

## Week 1 Storage

What is the simplest temporary storage implementation for Week 1? How will the application avoid coupling business logic directly to it?

_TODO_

## Expected Week 2 Changes

What should change when PostgreSQL is introduced? What should remain unchanged?

_TODO_

## Architecture Diagram

Add a simple diagram here.

```text
TODO
```

## Open Questions / Tradeoffs

_TODO_
