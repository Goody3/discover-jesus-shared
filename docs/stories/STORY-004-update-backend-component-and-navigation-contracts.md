# STORY-004. Update the backend for reusable components and page navigation

- **Status:** Complete; validated backend handed off for web integration
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Dependencies:** [STORY-001](STORY-001-define-component-reference-contract.md),
  [STORY-002](STORY-002-define-page-navigation-contract.md),
  [STORY-003](STORY-003-define-contract-migration-requirements.md)

## User story

As a content author, I want the backend to support independently managed
components and explicit page-navigation destinations, so that reusable content
and page navigation follow the agreed platform contracts.

## Context

The backend implementation is maintained outside this shared repository. The
backend work must follow the approved contract and migration requirements from
STORY-001 through STORY-003. The web interface will be updated afterward under
[STORY-005](STORY-005-update-web-interface-for-backend-contracts.md).

The accepted rollout direction permits a temporary non-production
incompatibility between the updated backend and the current web interface.
There is currently no production environment. Follow
[ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md)
and the agreed transition plan.

## Implementation progress

The backend implementation is complete in the `discover-jesus-backend`
repository. It adds public page UUIDs without replacing integer database IDs,
locale-specific reusable-component drafts and immutable published revisions,
and additive page-content draft, publish, and delivery routes. Draft authoring,
publication, and delivery validate locale-specific navigation destinations.
The existing admin CRUD routes and page-owned inline components remain
available; existing inline component data is not automatically converted into
the new `sections` format. The checked-in backend OpenAPI document has been
refreshed to include the new routes. Backend action validation was aligned with
the shared schema for optional condition fields on modal and external-link
actions. Reusable-component types are not restricted to an unapproved
allowlist. During STORY-005 integration, a live component-authoring smoke check
found that the reusable-content endpoint mapper dereferenced error details
even for successful service results. The mapper now returns successful results
as `200 OK`; the backend tests/build pass and a rebuilt local API instance
passed the component lifecycle HTTP smoke check. The already-running service
on port 5187 must be restarted to load this fix.

The STORY-004 database migration has been applied to the configured backend
SQL Server database and verified in EF migration history. The backend build,
migration SQL generation, an OpenAPI/validation smoke check, and thirteen
reusable-content service tests have passed. A read-only inventory of the
currently configured non-production dataset found 7 pages, 24 existing inline
components, and no stored action records or legacy `target` values. Inline
components remain in place as required; no content conversion was needed for
this dataset. Re-inventory before future production or data import because this
is a snapshot, not an ongoing guarantee. Database-backed lifecycle tests
remain outstanding. Authorization for publishing also remains an open
platform decision and is not introduced by this work.

The backend is available in the current local development environment at
`http://localhost:5187`; `/health` and Swagger returned HTTP 200 on
2026-10-31. Dalen owns the dependent web update, scheduled to start on
2026-10-31. This local endpoint is not a production or generally reachable
shared-environment URL. The temporary incompatibility remains open until the
web update and recovery checks are complete.

## Acceptance criteria

1. The backend manages components independently in the backend-managed content
   store and supports the agreed page-to-component reference behavior.
2. The backend implements the agreed page-navigation destination behavior and
   validation.
3. API responses and errors conform to the approved OpenAPI and schema
   contracts.
4. Existing content is migrated or handled according to the agreed migration
   plan.
5. Backend validation covers component references, navigation destinations,
   and relevant failure cases.
6. The backend update and dependent web update are coordinated using the
   approved non-production transition plan; any future production rollout
   requires a separate approved transition window and operational plan.
7. The backend work is implemented and validated in its owning repository; this
   shared story records the outcome and references that work.

## Scope

### Included

- Backend-managed component storage and lifecycle behavior as approved in
  STORY-001.
- Backend support for explicit page-navigation references as approved in
  STORY-002.
- Required API implementation, content migration, and validation.

### Excluded

- Updating the web interface; that is tracked in STORY-005.
- Choosing storage or lifecycle behavior not established by the approved
  contracts.

## Dependencies and constraints

- Depends on approved outcomes from STORY-001, STORY-002, and STORY-003.
- The implementation repository is `discover-jesus-backend`; the current
  backend is running locally for the dependent web work.
- The current temporary incompatibility is limited to non-production. Dalen
  owns the scheduled web follow-up; the incompatibility is not considered
  resolved until STORY-005's checks pass. Any future shared or production
  rollout needs an appropriately reachable endpoint and separate coordination.

## Completion checks

- [x] Backend implementation conforms to the approved component and
      navigation contracts; the open publisher-authorization decision remains
      an explicit platform decision.
- [x] Relevant backend tests and migration checks pass.
- [x] Backend implementation and transition handoff details are recorded;
      the database-backed lifecycle test gap is noted above.
- [x] STORY-005 is owned by Dalen and scheduled to start on 2026-10-31.
- [x] The backend is available at `http://localhost:5187` in the current local
      development environment for STORY-005 validation.

The epic and production transition are not complete until STORY-005 restores
and verifies end-to-end web operation.
