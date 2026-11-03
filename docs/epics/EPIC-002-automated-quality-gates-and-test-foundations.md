# EPIC-002. Automated quality gates and test foundations

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md).
- **Dependencies:** None identified. This epic should be delivered first so
  later epics, especially [EPIC-006](EPIC-006-maintainable-client-and-service-structure.md),
  are protected by tests.

## Need and opportunity

The Sprint 01 retrospectives for the backend, web, and iOS products each
identified thin test coverage and no automated verification. Backend tests
appear to cover only the reusable-content service, the web app has a small
number of preview tests, and the iOS app lacks decoding, rendering, and
navigation tests. Without automated gates, contract changes and refactoring are
risky.

## Outcome

Each product has automated build and test checks that run on every change and
cover the behavior that the platform depends on most.

## Success criteria

- Each product repository runs build, lint or static analysis, and tests
  automatically on proposed changes.
- Backend endpoints are covered by integration tests, not only service tests.
- Web editors, the API wrapper, and error paths have component or unit tests.
- iOS has tests for component decoding (including legacy shapes), rendering of
  each supported component and the unknown-component fallback, navigation
  resolution, and error paths.
- Dependency vulnerability scanning runs for each product.
- Coverage targets are agreed and reported. The targets themselves are to be
  decided by the owner.

## Scope

### Included

- Continuous integration for the backend, web, and iOS repositories.
- Backend integration tests and a dedicated test project.
- Web component tests and linting.
- iOS unit and renderer tests.

### Excluded

- End-to-end tests spanning all three products.
- Deployment pipelines and production release automation.
- Rewriting existing code beyond what is needed to make it testable.

## Assumptions, dependencies, and risks

- **Assumptions:** A CI service is available for each repository. The exact
  service is not yet decided.
- **Dependencies:** None.
- **Risks:** The large files named in the retrospectives may be hard to test
  before they are split; keep tests at the behavior level so they survive the
  refactoring in EPIC-006.

## Stories

| ID | Story | Status |
|---|---|---|
| STORY-007 | [Add continuous integration to the backend](../stories/STORY-007-add-backend-continuous-integration.md) | Complete |
| STORY-008 | [Add backend integration tests and a dedicated test project](../stories/STORY-008-add-backend-integration-tests.md) | Complete |
| STORY-009 | [Add continuous integration and linting to the web app](../stories/STORY-009-add-web-continuous-integration-and-linting.md) | In progress; PR open |
| STORY-010 | [Add web component and API client tests](../stories/STORY-010-add-web-component-and-api-tests.md) | Proposed |
| STORY-011 | [Add continuous integration to the iOS app](../stories/STORY-011-add-ios-continuous-integration.md) | Proposed |
| STORY-012 | [Add iOS decoding, rendering, and navigation tests](../stories/STORY-012-add-ios-decoding-rendering-and-navigation-tests.md) | Proposed |
| STORY-013 | [Agree and report test coverage targets](../stories/STORY-013-agree-and-report-coverage-targets.md) | Proposed |
