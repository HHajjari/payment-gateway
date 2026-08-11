# Week 1 Acceptance Criteria

Week 1 is complete only when all required criteria pass review.

## Domain

- [ ] Merchant and Payment concepts are modeled clearly.
- [ ] Money representation is justified and avoids `double`/`float`.
- [ ] Core invariants are enforced intentionally.
- [ ] Domain code does not depend unnecessarily on HTTP or persistence details.
- [ ] Sensitive card/CVV handling is safe for the synthetic learning environment.

## Architecture

- [ ] `architecture.md` is completed before the main implementation.
- [ ] Controller, application, domain, and storage responsibilities are clearly separated.
- [ ] Temporary Week 1 storage can later be replaced by PostgreSQL without rewriting core business rules.
- [ ] ID-generation strategy is explained.

## API

- [ ] Merchant creation works.
- [ ] Payment creation for an existing merchant works.
- [ ] Payment retrieval works.
- [ ] Invalid input produces deliberate 4xx responses.
- [ ] Unknown resources are handled intentionally.
- [ ] Responses do not expose sensitive card fields.

## Testing

- [ ] Required domain tests pass.
- [ ] Required application/use-case tests pass.
- [ ] Required API tests pass.
- [ ] Tests are deterministic.

## Study / Explanation

- [ ] At least 8/10 study questions can be explained correctly in your own words.
- [ ] You can explain authorization vs capture.
- [ ] You can defend the Money representation.
- [ ] You can explain which code should survive the Week 2 PostgreSQL migration unchanged.

## Review

- [ ] Code submitted for review.
- [ ] Review findings addressed.
- [ ] No unresolved correctness/security issue remains within Week 1 scope.

## Final Status

**NEEDS REVIEW**
