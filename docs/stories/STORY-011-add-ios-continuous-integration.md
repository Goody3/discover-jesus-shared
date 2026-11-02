# STORY-011. Add continuous integration to the iOS app

- **Status:** Proposed
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** [STORY-012](STORY-012-add-ios-decoding-rendering-and-navigation-tests.md)
  provides the tests the pipeline runs.

## User story

As an **iOS maintainer**, I want every proposed iOS change to be built and
tested automatically, so that regressions are caught before merge.

## Context

The iOS repository has no workflow definitions and no automated checks. It
contains an `.xcodeproj`, a second project file named `DiscoverJesus 2.xcodeproj`,
and an `.xcworkspace`; which one is canonical for CI is **unknown** and should be
confirmed. Hosted CI runners for iOS may have cost and Xcode-version
constraints that are not yet assessed. Implementation belongs in the
`discover-jesus-ios` repository.

## Acceptance criteria

1. A pipeline runs on every pull request and on pushes to the integration
   branch.
2. The pipeline builds the app and runs the unit tests on a simulator, and fails
   on any build or test failure.
3. The Xcode version, simulator, and scheme used by CI are documented and
   match the local development instructions.
4. The canonical project or workspace for building is identified and
   documented.
5. Dependencies, if any, are scanned for known vulnerabilities.
6. No signing credentials are required for the pipeline.

## Scope

### Included

- Build and test pipeline and its documentation.

### Excluded

- Code signing, TestFlight, and App Store release automation.
- Writing new tests (see STORY-012).
- Telemetry and UI tests.

## Dependencies and constraints

- Depends on the platform owner's choice of CI service and agreement on runner
  cost.

## Completion checks

- A pull request with a deliberately failing test fails the pipeline.
- A clean pull request passes.
