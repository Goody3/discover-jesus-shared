# EPIC-003. Contract integrity across backend, web, and iOS

- **Status:** In progress; backend verification and web type checks complete
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md).
- **Dependencies:** [EPIC-002](EPIC-002-automated-quality-gates-and-test-foundations.md)
  provides the CI needed to enforce these checks and is complete. Legacy
  retirement depends on the inventories and approvals in STORY-006 and STORY-017.

## Need and opportunity

All three Sprint 01 retrospectives point to drift risk between the backend and
its clients. The backend's checked-in `openapi.json` is now verified against
the served API metadata through STORY-014; generated web API types are
verified through STORY-015, and the iOS app
keeps legacy fallbacks (`legacyType` and slug-based navigation) alongside the
current contract.

## Outcome

Each checked-in contract remains authoritative for its own representation,
and products are verified against their identified contract inputs.
The owner approved preserving the separate backend/shared contracts and
verifying the backend API first; see
[ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md).
Legacy fallbacks are retired only after approved inventories and migrations.

## Success criteria

- CI fails if the backend's generated OpenAPI document differs from the
  checked-in contract.
- Web API types are generated from the contract, or verified against it.
- iOS decoding is verified against contract examples.
- Legacy slug-based retrieval and legacy component-type fallbacks are removed,
  following an approved consumer inventory and deprecation plan.

## Scope

### Included

- Contract verification in the backend.
- Generated or verified web types.
- iOS contract-decoding checks.
- Retirement of legacy retrieval/navigation via STORY-006 and component-type
  fallbacks via STORY-017.

### Excluded

- New contract features unrelated to drift or legacy removal.
- API versioning, unless the deprecation plan requires it.

## Assumptions, dependencies, and risks

- **Confirmed boundary:** Backend OpenAPI describes the implemented `/api`
  HTTP surface. Shared OpenAPI and content schemas remain separate contracts;
  no automatic `sections`/`content.blocks` mapping is approved.
- **Dependencies:** EPIC-002 CI is complete. Legacy removal requires consumer
  and content inventories plus an owner-approved plan.
- **Risks:** Removing fallbacks may break consumers that have not been
  inventoried. There is no production environment today, but any future
  rollout needs its own plan.

## Stories

| ID | Story | Status |
|---|---|---|
| STORY-014 | [Verify the backend OpenAPI contract](../stories/STORY-014-verify-backend-openapi-contract.md) | Complete |
| STORY-015 | [Generate or verify web API types](../stories/STORY-015-verify-web-api-types.md) | Complete |
| STORY-016 | [Verify iOS decoding against contract fixtures](../stories/STORY-016-verify-ios-contract-decoding.md) | Proposed; after STORY-014 |
| STORY-006 | [Deprecate slug-based page retrieval](../stories/STORY-006-deprecate-slug-based-page-retrieval.md) | Proposed; removal requires approved inventory and plan |
| STORY-017 | [Retire legacy component-type fallbacks](../stories/STORY-017-retire-legacy-component-type-fallbacks.md) | Proposed; removal requires approved inventory and plan |
