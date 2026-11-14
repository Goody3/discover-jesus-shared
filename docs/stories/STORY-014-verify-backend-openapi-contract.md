# STORY-014. Verify the backend OpenAPI contract

- **Status:** In progress
- **Epic:** [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)
- **Priority:** First verification increment
- **Dependencies:** [STORY-007](STORY-007-add-backend-continuous-integration.md),
  [STORY-008](STORY-008-add-backend-integration-tests.md)

## User story

As a backend maintainer, I want CI to compare generated API metadata with the
checked-in backend contract, so that undocumented API changes fail verification.

## Context

Implementation belongs in `discover-jesus-backend`. Per
[ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md),
compare `/swagger/v1/swagger.json` with that repository's `api/openapi.json`;
do not replace or compare it with the separate shared OpenAPI document.

## Acceptance criteria

1. A test starts the API in-process and compares the complete served OpenAPI
   document with the checked-in backend document.
2. Whitespace and JSON object-property order do not cause false failures;
   changes to paths, schemas, operation metadata, values, and array order do.
3. The existing CI test command runs the check and fails on contract drift.
4. The check needs no running backend or database and mutates no database.
5. Mismatch output identifies the generated document and the documented
   intentional-update workflow; CI does not silently rewrite the contract.
6. API behavior and the separate shared contracts remain unchanged.

## Scope

### Included

- OpenAPI metadata comparison, generated diagnostics, and local instructions.

### Excluded

- Payload-conformance testing, shared/backend API unification, and legacy removal.

## Dependencies and constraints

- Reuse the backend's existing test host and test runner dependencies.
- Generated contract differences require review, not automatic acceptance.

## Completion checks

- The contract test passes for the unchanged API and fails for a temporary
  metadata change without a contract update.
- Existing database-independent tests pass alongside the new test.
- Hosted CI and merged-branch verification are recorded before completion.

## Implementation status

- Implemented locally in `discover-jesus-backend` on
  `feature/story-014-openapi-verification`: in-process Swagger comparison,
  copied contract input, CI diagnostic artifact, and update instructions.
- The new contract test and 18 existing service tests pass. A temporary change
  to the health operation summary failed the comparison; the probe was removed
  and the passing test run repeated.
- Hosted CI, SQL Server-backed tests, and merged-branch verification have not
  been run for this change. The story is not yet complete.
