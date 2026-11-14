# STORY-016. Verify iOS decoding against contract fixtures

- **Status:** Proposed
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
