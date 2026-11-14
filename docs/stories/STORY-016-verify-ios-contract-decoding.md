# STORY-016. Verify iOS decoding against contract fixtures

- **Status:** In progress
- **Epic:** [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)
- **Priority:** After backend verification
- **Dependencies:** [STORY-014](STORY-014-verify-backend-openapi-contract.md),
  [STORY-012](STORY-012-add-ios-decoding-rendering-and-navigation-tests.md)

## User story

As an iOS maintainer, I want decoding tests using traceable contract fixtures,
so that content/API drift is detected before release.

## Context

The iOS repository is `Goody3/discover-jesus-ios`. Existing tests cover decoding,
rendering, navigation, and errors. Extend those checks with explicit fixture
provenance under
[ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md).

## Acceptance criteria

1. Identify each consumed HTTP operation and content representation before
   choosing fixtures; document unmapped shapes as gaps.
2. Fixtures identify their source contract/schema and revision, with a
   reproducible validation and refresh process.
3. CI validates fixtures against their applicable contract and runs Swift
   decoding tests for supported components and navigation fields.
4. Negative cases cover malformed/missing required data and unknown component
   handling without hiding intentional compatibility behavior.
5. Tests need no running backend and retain existing legacy compatibility tests
   until the separate removal stories authorize their replacement.

## Scope

### Included

- Traceable contract fixtures, decoding checks, and CI integration.

### Excluded

- Runtime UI changes and removal of navigation or component-type fallbacks.

## Dependencies and constraints

- Implementation belongs in `discover-jesus-ios`.
- Do not equate `sections`, `content.blocks`, and legacy component shapes.

## Completion checks

- Invalid fixture data and an incompatible decoder each produce failing checks.
- Existing iOS tests and the added contract checks pass in CI.

## Implementation status

- Implemented locally in `discover-jesus-ios` on
  `feature/story-016-contract-fixtures`. No runtime behavior or legacy
  fallback removal is included.
- Confirmed consumed operations: backend `GetPages` (`GET /api/pages`) and
  `GetPageContent` (`GET /api/content/pages/{pageId}?locale=...`), both 200 JSON
  responses. Bundled, synthetic fixtures map to those operation response schemas.
- Backend input is pinned at
  `dc977a8d61b789bc33683f6d2b0c41cac8f71d8f`, the merged STORY-015 metadata
  prerequisite. An explicit projection of top-level typed navigation actions
  maps to shared `schemas/actions.schema.json#/definitions/action` at
  `f644127e53d421c9c4c8fb3c5e42663c11e14a1a`. Vendored input digests and a
  refresh/check script provide reproducible provenance without sibling
  checkouts or cross-repository credentials in CI.
- **Documented gap:** Backend `sections` is opaque JSON. Section-array and
  per-section type assumptions, renderer-specific fields, legacy metadata,
  and unknown-component behavior are client compatibility examples, not
  guarantees inferred from shared component/page schemas.
- **Intentional compatibility:** A missing navigation UUID is rejected by
  the mapped shared action schema but remains optional in Swift. Tests make
  this difference explicit rather than tightening the decoder. UUID preference,
  case-insensitive slug fallback, legacy component-type override, and unknown
  component safety remain tested.
- Local contract validation passed for both HTTP fixtures, typed navigation
  projections, and 17 negative probes. All 12 iOS tests passed on an eligible
  iOS simulator (six existing tests and six added fixture tests). A temporary
  non-string title in the fixture failed validation as expected and was removed.
- A temporary incompatible section coding key (`componentType` instead of
  `type`) failed the new fixture decoding test with `DecodingError.keyNotFound`.
  The probe was removed and contract validation plus a clean build and all 12
  iOS tests passed again; no runtime decoder change is included.
- Hosted CI, review, merge, and merged-branch verification remain; this story
  is not complete.
