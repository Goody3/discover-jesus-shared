# STORY-017. Retire legacy component-type fallbacks

- **Status:** Proposed; removal blocked on inventory and owner approval
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
3. Obtain owner approval for compatibility requirements, migration steps,
   notice/monitoring needs, and measurable removal criteria.
4. Migrate affected in-scope content/producers and verify consumer decoding
   against current-contract fixtures before removal.
5. Remove fallback code and update tests/documentation only when the approved
   gates are satisfied; retain the unknown-component safety behavior.

## Scope

### Included

- Inventory, approved migration, fallback removal, and coordinated tests.

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
- Stored-content counts and approved mappings are still unknown. The historical
  iOS standardization plan is a proposal, not an accepted payload contract.
- Removal remains blocked. In particular, changing only the classifier would
  miss navigation-bar assembly's direct `legacyType` dependency.
