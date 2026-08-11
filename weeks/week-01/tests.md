# Week 1 Tests

## Required Test Categories

### Domain Tests

Implement tests for the rules you place inside `Payment`, `Money`, and other domain objects.

At minimum, consider:

- valid money creation
- invalid monetary amounts
- valid/invalid currency handling
- payment creation invariants
- merchant/payment identity behavior

### Application Tests

Test the use cases without requiring HTTP where practical.

Cover:

- create merchant
- create payment for existing merchant
- reject payment for nonexistent merchant
- retrieve existing payment
- retrieve nonexistent payment

### API Tests

Cover successful requests and intentional validation/error behavior.

Examples:

- merchant creation returns appropriate HTTP semantics
- payment creation returns appropriate HTTP semantics
- malformed/invalid request returns 4xx
- nonexistent payment returns appropriate 4xx
- response does not expose sensitive card fields

## Testing Constraints

- Tests must be deterministic.
- Do not test private implementation details.
- Do not mock the domain model.
- Sensitive card fields must never appear in assertion failure output intentionally.

## Evidence / Notes

_TODO_
