# STORY-015. Generate or verify web API types

- **Status:** Proposed
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
