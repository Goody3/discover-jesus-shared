# STORY-007. Add continuous integration to the backend

- **Status:** Complete; successful GitHub Actions run on `main`, with both negative-case gates verified locally using temporary probes
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
excluded from the API project. The workflow and README documentation were
implemented in the `discover-jesus-backend` repository and merged to `main`.

Decisions by the platform owner:

- **CI service:** GitHub Actions.
- **Triggers:** pull requests targeting `dev` or `main`, and pushes to `dev` or
  `main`, matching the branching model in CONTRIBUTING.
- **Test database:** a real SQL Server container, because the service relies on
  SQL Server and explicit transactions.
- **Vulnerability threshold:** fail on High or Critical findings.

## Acceptance criteria

1. A GitHub Actions workflow runs on pull requests targeting `dev` or `main`
   and on pushes to `dev` or `main`.
2. The pipeline restores, builds, and runs all backend tests, and fails on any
   build or test failure. Tests that need a database run against a SQL Server
   container started by the workflow.
3. The pipeline fails when the EF Core model has pending changes without a
   migration.
4. The pipeline scans NuGet dependencies, including transitive ones, for known
   vulnerabilities, reports all findings, and fails on High or Critical
   severity.
5. The pipeline needs no secrets beyond what is documented, and no credentials
   are stored in source. The SQL Server container password is generated or
   supplied through workflow configuration, not committed as a reusable secret.
6. The backend README or CONTRIBUTING document describes how to run the same
   checks locally.

## Scope

### Included

- The GitHub Actions workflow with build, test, migration-drift, and
  dependency-scan steps.
- The SQL Server container used by the tests in CI.
- Local-run documentation.

### Excluded

- Deployment and release automation.
- Writing new tests (see [STORY-008](STORY-008-add-backend-integration-tests.md)).
- Verifying `openapi.json` against the code (covered by
  [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)).

## Dependencies and constraints

- The database choice (SQL Server container) also applies to
  [STORY-008](STORY-008-add-backend-integration-tests.md). The existing service
  tests use EF Core InMemory; the CI workflow starts SQL Server for integration
  tests as they are added.
- Branch protection rules that require this check to pass before merge are a
  repository setting and are not covered by this story.

## Completion checks

- A temporary deliberately failing xUnit test made the `dotnet test` command
  exit non-zero; the probe was removed afterward.
- A temporary EF Core model change without a migration made the migration
  check exit non-zero; the probe was reverted afterward.
- The GitHub Actions run on merged `main` completed successfully, including
  SQL Server startup/readiness, tests, migration-drift check, and dependency
  scan: [run 41441603622](https://github.com/Goody3/discover-jesus-backend/actions/runs/41441603622).
- A temporary project using `System.Text.Encodings.Web` 4.7.1 produced a
  Critical-severity advisory; the workflow's High/Critical match condition
  matched that output and would fail the scan job. The vulnerable dependency
  was only in a disposable probe and was not committed.
