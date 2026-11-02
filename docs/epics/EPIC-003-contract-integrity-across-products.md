# EPIC-003. Contract integrity across backend, web, and iOS

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md).
- **Dependencies:** [EPIC-002](EPIC-002-automated-quality-gates-and-test-foundations.md)
  provides the CI needed to enforce these checks. Builds on
  [STORY-006](../stories/STORY-006-deprecate-slug-based-page-retrieval.md).

## Need and opportunity

All three Sprint 01 retrospectives point to drift risk between the backend and
its clients. The backend's checked-in `openapi.json` is not verified against
the running API, the web app's API types are hand-written, and the iOS app
keeps legacy fallbacks (`legacyType` and slug-based navigation) alongside the
current contract.

## Outcome

The checked-in contract is the single source of truth, and each product is
verified against it automatically. Legacy fallbacks are retired once consumers
have moved.

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
- Retirement of legacy fallbacks, via STORY-006.

### Excluded

- New contract features unrelated to drift or legacy removal.
- API versioning, unless the deprecation plan requires it.

## Assumptions, dependencies, and risks

- **Assumptions:** `api/openapi.json` in the backend is the contract artifact
  that clients should follow. Confirm how it relates to the schemas in this
  repository before implementation.
- **Dependencies:** EPIC-002 CI; STORY-006 consumer inventory.
- **Risks:** Removing fallbacks may break consumers that have not been
  inventoried. There is no production environment today, but any future
  rollout needs its own plan.

## Stories

| ID | Story | Status |
|---|---|---|
| STORY-006 | [Deprecate slug-based page retrieval](../stories/STORY-006-deprecate-slug-based-page-retrieval.md) | Proposed (existing story, to be re-parented from EPIC-001 when this epic is accepted) |
