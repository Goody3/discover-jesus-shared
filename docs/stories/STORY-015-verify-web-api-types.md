# STORY-015. Generate or verify web API types

- **Status:** In progress
- **Epic:** [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)
- **Priority:** After backend verification
- **Dependencies:** [STORY-014](STORY-014-verify-backend-openapi-contract.md)

## User story

As a web maintainer, I want API types tied to the backend HTTP contract, so that
incompatible contract changes cannot silently drift from the client.

## Context

The web API types are hand-written in `discover-jesus-web`. The owning backend
contract is distinct from the shared OpenAPI and content schemas; see
[ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md).

## Acceptance criteria

1. Document the backend contract revision consumed by the web app and a
   reproducible retrieval/update process that needs no sibling checkout in CI.
2. Generate types or add an equivalent automated verification covering the
   HTTP request/response types used by the app.
3. CI fails on stale generated types or incompatible hand-written types;
   intentional updates are reviewed with their contract revision.
4. Preserve API error handling, 204 handling, and editor/preview behavior.
5. Keep opaque dynamic JSON explicit; do not infer a shared-schema mapping
   solely from an example or an untyped payload.

## Scope

### Included

- Contract input/version tracking, type checks, and update instructions.

### Excluded

- New API features, changing content representations, and legacy removal.

## Dependencies and constraints

- Implementation belongs in `discover-jesus-web`.
- Select the simplest supported generation or verification tool after inspecting
  the backend document's type coverage.

## Completion checks

- A controlled stale-type probe fails CI verification.
- Web tests, type-check, lint, formatting, and build pass.

## Implementation status

- On November 14, 2026, the owner approved checking backend serialization and
  correcting required-field metadata before generating web types. The existing
  backend response schemas omitted `required` lists, and `/health` omitted its
  JSON shape; those omissions did not establish the web app's existing
  required-field assumptions.
- Backend prerequisite implemented on
  `feature/story-015-response-contract-types`, revision
  `dc977a8d61b789bc33683f6d2b0c41cac8f71d8f`: a response-only Swagger filter
  documents every serialized output field, and a named health DTO documents
  the unchanged health response. Request validation, error metadata, and JSON
  serializer settings are unchanged.
- Twelve response examples are checked against the configured HTTP serializer,
  including false, zero, empty collections, and opaque JSON null. The health
  endpoint is checked in-process. All 32 database-independent backend tests
  passed; SQL Server integration tests have not been run locally for this change.
- Web implementation on `feature/story-015-web-api-types` vendors that exact
  backend contract with its revision and SHA-256 digest. Pinned
  `openapi-typescript` generates schema and operation types; the application's
  responses and JSON request literals reference them. Opaque JSON remains
  `unknown`; no shared-schema mapping is inferred.
- `npm run api:update -- <full-backend-sha> [backend-checkout]` retrieves the
  committed backend document through GitHub or local Git and regenerates types.
  CI uses only the vendored input and lockfile, without a sibling checkout,
  backend server, or cross-repository credentials.
- `npm run api:check` rejects provenance mismatches and stale generated output.
  A temporary generated-file change failed that command; a temporary string
  in the page request's boolean field failed type-checking. Both probes were
  removed and verification passed again.
- Web validation passed: 36 tests, type-check, lint, formatting, production
  build, and the High/Critical dependency-audit gate. The existing seven
  Moderate dependency findings remain reported, not fixed by this story.
  Existing API error, 204, editor, and preview tests passed.
- Generated types verify the pinned document, not the latest backend branch.
  Updates remain explicit and reviewed. Constructed URLs and runtime response
  payloads are not validated by these compile-time checks.
- Publication, hosted CI, review, merge, and merged-branch verification remain.
  Merge the backend prerequisite before the dependent web change; do not mark
  this story complete yet.
