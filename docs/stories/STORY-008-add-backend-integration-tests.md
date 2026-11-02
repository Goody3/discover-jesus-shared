# STORY-008. Add backend integration tests and a dedicated test project

- **Status:** Proposed
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** None identified; benefits from
  [STORY-007](STORY-007-add-backend-continuous-integration.md) for automated
  runs.

## User story

As a **backend maintainer**, I want the HTTP endpoints covered by integration
tests in a standalone test project, so that behavior that clients depend on is
verified end to end.

## Context

The backend retrospective found that existing tests appear to cover only the
reusable-content service. The legacy pages, components, bindings, and actions
endpoints and the reusable-content endpoints are not covered over HTTP. The
tests currently sit in `Tests/` and are excluded from the API project with
`Compile Remove`, with no solution file. `Program` is already exposed as a
partial class for `WebApplicationFactory`. Implementation belongs in the
`discover-jesus-backend` repository.

## Acceptance criteria

1. The tests live in their own project, referenced by a solution file, and no
   longer rely on `Compile Remove` in the API project.
2. Integration tests use `WebApplicationFactory` and a database approach that
   runs without developer-specific setup.
3. Tests cover success and failure responses (including not-found and
   validation errors) for the pages, components, bindings, and actions
   endpoints.
4. Tests cover the reusable-content endpoints, including draft, publish, and
   locale behavior and the published content route used by clients.
5. The existing service tests continue to pass.
6. Build output under `Tests/` is ignored by version control.

## Scope

### Included

- Test project and solution structure.
- Integration tests for existing endpoints.

### Excluded

- Authorization, concurrency, and pagination tests, which depend on features
  in later epics.
- Changing endpoint behavior, except to fix defects the tests reveal; record
  any such defect separately.

## Dependencies and constraints

- The database approach (for example an in-memory provider versus a real SQL
  Server instance) is a **decision to be recorded**. Behavior differences
  between providers must be understood, as the service uses transactions.

## Completion checks

- `dotnet test` at the solution level runs the service and integration tests.
- The tests run with no local connection string beyond what is documented.
