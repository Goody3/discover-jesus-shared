# STORY-007. Add continuous integration to the backend

- **Status:** Proposed
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** None identified

## User story

As a **backend maintainer**, I want every proposed backend change to be built,
tested, and scanned automatically, so that regressions and vulnerable
dependencies are caught before merge.

## Context

The Sprint 01 backend retrospective found no CI pipeline. The backend is an
ASP.NET Core 8 project with EF Core migrations and tests under `Tests/` that are
excluded from the API project. The CI service is **unknown**; the backend
repository has no workflow definitions today. Implementation belongs in the
`discover-jesus-backend` repository.

## Acceptance criteria

1. A pipeline runs on every pull request and on pushes to the integration
   branch.
2. The pipeline restores, builds, and runs all backend tests, and fails on any
   build or test failure.
3. The pipeline fails when the EF Core model has pending changes without a
   migration.
4. The pipeline scans NuGet dependencies for known vulnerabilities and reports
   findings; the failure threshold is agreed with the owner.
5. The pipeline needs no secrets beyond what is documented, and no credentials
   are stored in source.
6. The backend README or CONTRIBUTING document describes how to run the same
   checks locally.

## Scope

### Included

- Build, test, migration-drift, and dependency-scan steps.
- Local-run documentation.

### Excluded

- Deployment and release automation.
- Writing new tests (see [STORY-008](STORY-008-add-backend-integration-tests.md)).
- Verifying `openapi.json` against the code (covered by
  [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)).

## Dependencies and constraints

- The tests need a database strategy that works in CI; this is decided in
  STORY-008 if the existing tests do not already cover it.

## Completion checks

- A pull request with a deliberately failing test fails the pipeline.
- A pull request with a model change and no migration fails the pipeline.
- A clean pull request passes.
