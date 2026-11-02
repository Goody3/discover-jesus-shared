# STORY-002. Define explicit page-navigation references

- **Status:** Complete; owner-reviewed navigation proposal and schema documented
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Dependencies:** Owner direction on destination identity, scope, validation
  timing, and legacy-target policy is recorded in
  [ADR 0002](../../architecture/decisions/0002-explicit-page-navigation-references.md).

## User story

As a content author, I want a navigation button to explicitly identify its
destination page, so that page navigation can be authored and validated
consistently.

## Context

The owner-reviewed proposal uses UUID `pageId` references, distinct modal and
HTTPS external-URL actions, authoring/publication validation plus runtime
re-checks, requested-locale resolution without fallback, and migration of legacy
string targets before rollout. The accessible backend currently uses integer
page IDs and slugs, so implementation alignment is not established.

## Acceptance criteria

1. The navigation contract distinguishes page destinations from other
   destination types, if those are supported.
2. The representation of a destination page is agreed and linked to page
   identity.
3. Validation timing and behavior for missing, unpublished, or inaccessible
   pages are specified or recorded as open decisions.
4. Compatibility behavior for existing string navigation targets is
   documented.
5. The proposal is reviewed by the platform owner before implementation work is
   marked ready; unresolved implementation details are recorded as open.

## Scope

### Included

- Define the page-button navigation relationship.
- Identify impacted action, page, or component contracts.
- Document validation and compatibility requirements.

### Excluded

- Implementing navigation in backend or client applications.
- Assuming all button targets are internal pages; external links and modal
  actions require explicit treatment.

## Dependencies and constraints

- Depends on decisions captured by [ADR 0002](../../architecture/decisions/0002-explicit-page-navigation-references.md).
- Must remain consistent with the agreed page identity and component contract.

## Completion checks

- [x] Navigation contract proposal and examples are documented in
  [ADR 0002](../../architecture/decisions/0002-explicit-page-navigation-references.md)
  and represented in the shared action schemas.
- [x] Validation timing and legacy-target migration policy are recorded;
  missing, unpublished, deleted, or inaccessible destinations are ineligible,
  block authoring/publication, and produce a clear runtime unavailable error.
- [x] Platform owner has reviewed and approved the model direction; remaining
  implementation details are listed as open in ADR 0002.
