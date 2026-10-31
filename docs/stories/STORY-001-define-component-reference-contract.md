# STORY-001. Define the reusable component reference contract

- **Status:** Complete; owner-reviewed proposal and page-reference schema documented
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Dependencies:** Platform-owner direction is recorded; remaining
  operational and production-catalog questions are documented as open in
  [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md)

## User story

As a platform architect, I want an agreed model for backend-managed reusable
components and page references, so that backend and client implementations can
share a consistent contract.

## Context

The component store is a backend-managed content store, not a source-code
repository. Existing page and component schemas may represent overlapping
content differently; see the [system overview](../../architecture/system-overview.md).
The preliminary
[component catalog proposal](../components/component-catalog-proposal.md)
summarizes types visible in the local Content Studio. The platform owner
accepted that snapshot as representative enough for this story's discovery
cross-check; it is not an approved production type allowlist.

## Acceptance criteria

1. The contract defines component identity and whether/how component versions
   can be referenced.
2. The contract defines how a page represents references to stored components
   and whether inline components remain supported.
3. Component update, deletion, publication, and unresolved-reference behavior
   are specified or explicitly recorded as open decisions.
4. The affected page and component schemas are identified, with examples and
   compatibility implications documented.
5. The proposal is reviewed by the platform owner before implementation work is
   marked ready.
6. The proposed catalog is checked against representative authored content, and
   unsupported or unverified types are identified before finalizing the initial
   supported type set.

## Scope

### Included

- Define the contract independent of storage technology.
- Review and validate the preliminary component catalog against representative
  content.
- Identify schema and API surfaces affected by component references.
- Document component lifecycle and reference-resolution decisions.

### Excluded

- Implementing the backend content store.
- Selecting a database or deployment topology.
- Treating the proposal as accepted before owner review.

## Dependencies and constraints

- Depends on decisions captured by [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md).
- Must resolve or explicitly account for the difference between page `sections`
  and API `content.blocks`.

## Completion checks

- [x] Draft contract proposal, page-reference schema, and examples are documented; backend behavior is not implemented.
- [x] Related schemas, API surfaces, and accessible backend surfaces are listed.
- [x] Compatibility questions are recorded.
- [x] Platform owner has reviewed the proposed model; remaining details are listed as open.
- [x] Catalog cross-checked against the owner-accepted representative Content Studio snapshot; limitations and unverified types are documented. Final production allowlist approval is outside this story.

See the [draft reusable component reference contract](../components/component-reference-contract-proposal.md).
