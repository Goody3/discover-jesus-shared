# STORY-010. Add web component and API client tests

- **Status:** Complete; merged in [discover-jesus-web#5](https://github.com/Goody3/discover-jesus-web/pull/5) with CI passing
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** None identified; runs in CI once
  [STORY-009](STORY-009-add-web-continuous-integration-and-linting.md) is done.

## User story

As a **web maintainer**, I want automated tests for the API client, editors,
and error paths, so that I can change the app without breaking authoring and
preview behavior.

## Context

The web retrospective found about five tests, all on the published-page
preview. The API wrapper in `src/api.ts`, the page and reusable-component
editors, and error handling were untested. The implementation uses Vitest and
React Testing Library. Tests should check behavior rather than internal
structure, because `App.tsx` will be split later under
[EPIC-006](../epics/EPIC-006-maintainable-client-and-service-structure.md).
Implementation belongs in the `discover-jesus-web` repository.

## Acceptance criteria

1. A component test setup exists and runs through the same `npm test` command
   as the existing tests.
2. `apiRequest` is tested for success, 204 responses, legacy `{error}` bodies,
   Problem Details `title` bodies, non-JSON error bodies, and network failure.
3. The page editor is tested for create, edit, and delete flows, including
   displayed errors.
4. The reusable-component manager is tested for draft, publish, and locale
   flows, including displayed errors.
5. The published-page preview keeps its existing tests and gains tests for
   loading and error states.
6. Tests mock the API at the network boundary and need no running backend.

## Scope

### Included

- Test tooling and tests for the items above.

### Excluded

- Browser end-to-end tests.
- Refactoring source files except for small changes needed to make code
  testable.
- Visual regression testing.

## Dependencies and constraints

- Adding test dependencies changes `package.json` and the lockfile.

## Completion checks

- `npm test` passes locally and in CI.
- Temporarily breaking the API error parsing causes a test to fail.

## Implementation status

- Vitest and React Testing Library tests cover the API client, page editor,
  reusable-component manager, and published-page preview. API requests are
  mocked at the network boundary, so tests need no running backend.
- Local tests, formatting, lint, type-check, build, and dependency audit passed.
  The API error-handling mutation check caused the expected tests to fail.
- The merged
  [discover-jesus-web#5](https://github.com/Goody3/discover-jesus-web/pull/5)
  has passing `build-and-test` and `dependency-audit` CI checks.
