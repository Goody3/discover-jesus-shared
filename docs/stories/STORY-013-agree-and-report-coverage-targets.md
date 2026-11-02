# STORY-013. Agree and report test coverage targets

- **Status:** Proposed
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** [STORY-007](STORY-007-add-backend-continuous-integration.md),
  [STORY-009](STORY-009-add-web-continuous-integration-and-linting.md),
  [STORY-011](STORY-011-add-ios-continuous-integration.md), and the test
  stories [STORY-008](STORY-008-add-backend-integration-tests.md),
  [STORY-010](STORY-010-add-web-component-and-api-tests.md),
  [STORY-012](STORY-012-add-ios-decoding-rendering-and-navigation-tests.md)

## User story

As a **platform owner**, I want agreed coverage targets reported for each
product, so that I can see whether testing is improving.

## Context

EPIC-002 requires coverage targets to be agreed and reported, and says the
targets are for the owner to decide. No baseline exists yet. Measuring coverage
only makes sense once the tests and pipelines from the other stories are in
place. This story spans the backend, web, and iOS repositories and records the
decision in this repository.

## Acceptance criteria

1. Coverage is measured for each product in CI, using a tool chosen for that
   product.
2. A baseline for each product is recorded after the test stories are done.
3. The platform owner decides the target for each product, and whether each
   target is a reporting goal or a failing gate. The decision is documented in
   this repository.
4. Coverage results are visible on each pull request or in the pipeline output.
5. If a target is a failing gate, the pipeline enforces it.

## Scope

### Included

- Coverage measurement, baseline, target decision, and reporting.

### Excluded

- Raising coverage beyond what the other stories deliver.
- Cross-product aggregate coverage.

## Dependencies and constraints

- Target values are **unknown** and must not be assumed.

## Completion checks

- Each product's pipeline outputs a coverage figure.
- The decision record states the baseline and the owner-approved targets.
