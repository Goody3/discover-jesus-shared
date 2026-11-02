# EPIC-001. Reusable components and explicit page navigation

- **Status:** Core scope complete for local-development integration; slug-based page retrieval deprecation follow-up proposed; no production rollout
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md).
- **Dependencies:** Platform-owner agreement on the open questions in the
  related architecture decision records. Backend and web implementation work
  follows the coordinated rollout direction in
  [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).

## Need and opportunity

The platform owner wants components managed independently from pages in a
backend-managed content store, and wants navigation buttons to have an explicit
relationship with the pages they open. The current shared contracts do not yet
define either relationship end to end.

## Outcome

Pages can use reusable components managed by the backend without treating
component definitions as page-owned content, and navigation buttons can refer
to valid destination pages through an agreed contract.

The contract and migration proposals are complete. Backend and web
implementations have been validated together in local development, restoring
end-to-end page rendering and navigation. There is no production environment;
the completed local transition does not imply a production rollout.

The planned implementation sequence is backend first, then web. A temporary
incompatibility is accepted in non-production as recorded in
[ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).
There is currently no production environment; any future production rollout
requires its own approved transition window and operational plan.

The original component and navigation integration is complete. A follow-up
story proposes migrating consumers from the legacy slug-based page lookup;
that work requires a consumer inventory and an approved compatibility and
deprecation plan before the route is marked deprecated or removed.

## Success criteria

- The component store, component identity/lifecycle, and page-reference
  behavior are documented and approved.
- Navigation destination semantics and validation behavior are documented and
  approved.
- Shared API and schema contracts consistently represent both relationships.
- The current non-production incompatibility, communications, monitoring, and
  forward-fix steps are agreed before the backend change is applied in a shared
  environment. A future production transition plan is approved before
  production rollout.
- Backend changes are implemented and validated before the dependent web update.
- The web interface is updated and validated against the new backend, restoring
  end-to-end operation.

## Scope

### Included

- Design of a backend-managed reusable component content model.
- Explicit page-to-component references.
- Explicit button-to-destination-page navigation relationships.
- Shared contract changes, compatibility planning, and migration planning.

### Excluded

- Selecting a specific database or deployment architecture before requirements
  are agreed.
- Claiming implementation in the backend or clients from this shared
  documentation repository.
- Changes to unrelated authentication, content, or deployment behavior.

## Assumptions, dependencies, and risks

- **Assumptions:** “Component repository” means a backend-managed content
  store. “Tighter coupling” is provisionally interpreted as a typed reference
  to a page identity, subject to owner approval.
- **Dependencies:** Agreement on open questions in [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md)
  and [ADR 0002](../../architecture/decisions/0002-explicit-page-navigation-references.md);
  rollout follows [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).
- **Risks:** There is no production environment now, so the accepted
  incompatibility is non-production only. Other runtime clients may require
  coordinated updates; identify them before any future production rollout.

## Stories

| ID | Story | Status |
|---|---|---|
| STORY-001 | [Define the reusable component reference contract](../stories/STORY-001-define-component-reference-contract.md) | Complete (production allowlist is separate) |
| STORY-002 | [Define explicit page-navigation references](../stories/STORY-002-define-page-navigation-contract.md) | Complete; owner-reviewed navigation proposal documented |
| STORY-003 | [Define API, compatibility, and migration requirements](../stories/STORY-003-define-contract-migration-requirements.md) | Complete; current non-production plan documented, future production gates recorded |
| STORY-004 | [Update the backend for reusable components and page navigation](../stories/STORY-004-update-backend-component-and-navigation-contracts.md) | Complete; backend validated and available in local development |
| STORY-005 | [Update the web interface for the new backend](../stories/STORY-005-update-web-interface-for-backend-contracts.md) | Complete; local-development integration validated |
| STORY-006 | [Deprecate slug-based page retrieval](../stories/STORY-006-deprecate-slug-based-page-retrieval.md) | Proposed follow-up; consumer inventory and deprecation criteria pending |
