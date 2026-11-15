# STORY-017. Retire legacy component-type fallbacks

- **Status:** Proposed; role mappings and validation gates pending, removal blocked
- **Epic:** [EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)
- **Priority:** After contract-decoding verification
- **Dependencies:** [STORY-016](STORY-016-verify-ios-contract-decoding.md)

## User story

As a platform maintainer, I want legacy component-type fallback logic retired
only after content and consumers are compatible, so that simplified decoding
does not break existing content.

## Context

EPIC-003 names `legacyType` compatibility as a retirement goal. This is separate
from the slug-retrieval/navigation migration in
[STORY-006](STORY-006-deprecate-slug-based-page-retrieval.md). Starting the epic
does not authorize removal; see
[ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md).

## Acceptance criteria

1. Inventory known content producers, payloads, and consumers that rely on
   component-type fallbacks; distinguish verified findings from unknowns.
2. Map legacy shapes to approved current shapes, documenting incompatible or
   ambiguous records rather than silently dropping them.
3. Obtain owner approval for current payload mappings, active-content
   migration, affected producers/consumers, and non-production validation.
   No deprecation window or backward-compatible reader is required before
   production.
4. Migrate active in-scope content and producers, and verify web/iOS decoding
   and behavior against the approved current representation before cutover.
5. Remove the legacy fallback as part of the coordinated non-production
   cutover; retain unknown-component safety behavior. Any future production
   rollout requires its own transition plan.

## Scope

### Included

- Inventory, approved active-content migration, fallback removal, and
  coordinated non-production validation.

### Excluded

- Slug-based retrieval/navigation retirement, unrelated renderer refactoring,
  and assuming production deployment readiness.

## Dependencies and constraints

- Implement changes in each affected owning repository.
- A future production rollout needs a separate approved transition plan.

## Completion checks

- Inventory and owner approval are linked to verified migration evidence.
- Current-contract decoding passes and obsolete fallback behavior is absent.
- Unknown-component handling and other client tests still pass.

## Inventory preparation

- [Source inventory](EPIC-003-legacy-retirement-inventory.md) identifies iOS
  `legacyType` precedence, navigation-bar assembly, and adjacent legacy display
  fields; web/backend producer surfaces; and different renderer type sets.
- The hosted development database is inventoried: every reusable component
  revision carries exactly one top-level `source.legacyType` (47 current
  revisions across nine observed `type`/`legacyType` pairings, including 4
  current NBButtons and one nonstandard lowercase `title`). Counts for other
  environments and approved mappings are still unknown. The historical
  iOS standardization plan is a proposal, not an accepted payload contract.
- Candidate per-role mappings and cutover checks are drafted in the
  inventory for owner review. They do not authorize payload changes or fallback
  removal; unmapped or semantically lossy roles remain blocked.
- On November 14, 2026, the owner approved a top-level `role` separate from
  generic `type` and presentation `style`, and selected linked migrated
  representations for all 55 immutable revisions while preserving the
  originals. The owner accepted the inventory's role/field mapping as a
  candidate baseline and directed that unresolved cases remain blocked. The
  final role vocabulary, role-specific fields, and backend link/current-
  revision mechanics remain open; see
  [ADR 0006](../../architecture/decisions/0006-uuid-page-identity-and-linked-content-migration.md).
  No data migration or fallback removal is authorized.
- Removal remains blocked. In particular, changing only the classifier would
  miss navigation-bar assembly's direct `legacyType` dependency.
