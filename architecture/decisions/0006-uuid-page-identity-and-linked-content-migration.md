# 0006. UUID page identity and linked content migration

- **Status:** Accepted
- **Date:** 2026-11-14
- **Decision owners:** Platform owner
- **Related decisions:** [0002. Explicit page-navigation references](0002-explicit-page-navigation-references.md), [0003. Coordinated backend-first web transition](0003-coordinated-backend-first-web-transition.md), [0005. Separate contract verification boundaries](0005-separate-contract-verification-boundaries.md)

## Context

EPIC-003 is preparing a coordinated, pre-production migration of stored
component payloads and page navigation. The shared OpenAPI already represents
page IDs as UUIDs, while the backend stores an integer database ID alongside a
stable public UUID. Stored component history includes slug-based page
references and legacy component-type fields.

The hosted development database inventory and the owner's selected migration
direction are documented in the
[EPIC-003 inventory](../../docs/stories/EPIC-003-legacy-retirement-inventory.md).

## Decision

1. Use the backend page `PublicId` UUID as the shared page identity. The
   shared OpenAPI `Page.id`, dynamic page-schema `id`, and navigation
   `pageId` identify the same page. The backend integer database `Id` and
   human-facing slug are not substitutes for that identity.
2. Place component semantic roles in a top-level `role` property, separate
   from generic `type` and presentation `style`. The canonical role vocabulary
   and role-specific fields remain open and must be approved before migrating
   stored payloads.
3. Migrate all 55 inventoried reusable-component revisions by retaining each
   original revision unchanged and creating a migrated representation linked
   to its source revision. Do not overwrite or reuse the original revision
   identity. The backend implementation must define the durable link and how
   migrated representations are selected as current content.
4. Treat `source.page` as a live page reference and migrate it to the shared
   UUID identity. Convert stored navigation targets only through the approved
   page mapping and action contract.

This decision applies to the hosted development database, backend, web, and
iOS, plus any additional in-scope consumers the owner identifies. It does not
authorize a database migration, deployment, route removal, or legacy fallback
removal. The planned cutover is non-production and requires no
backward-compatibility period. A future production rollout needs a separate
transition plan under ADR 0003.

Aligning page identity does not unify the distinct shared OpenAPI
`content.blocks` and dynamic page-schema `sections` structures; those contract
boundaries remain as recorded in ADR 0005.

## Alternatives considered

- **Keep shared identity separate from backend `PublicId`:** Rejected because
  it would require an extra translation boundary for navigation and would not
  match the current backend/iOS UUID lookup.
- **Rewrite historical revisions in place:** Rejected because the revisions
  are treated as immutable history and the owner selected preservation of
  original records.
- **Put semantic roles in `style`:** Rejected because role expresses component
  meaning, not presentation.

## Consequences

- Shared schemas can validate UUID shape and document the common identity;
  backend, web, and iOS implementations still need coordinated updates and
  focused validation.
- The generic component schema can express a top-level role, but payload
  migration remains blocked on the role vocabulary, role-specific field names,
  allowed values, and treatment of ambiguous legacy records.
- The backend must preserve source revision identities, create linked
  transformed representations, and make the active-revision behavior explicit.
- Slug values remain user-facing identifiers unless separately approved.
- Stored-content and consumer inventories must be refreshed before the actual
  migration and cutover.

## Open questions

- What canonical role values, role-specific properties, and allowed values
  preserve every inventoried rendering behavior?
- What backend storage and API representation will link each migrated
  revision to its immutable source, and how will the migrated current revision
  be selected?
- What exact client, backend, and content checks gate the non-production
  cutover, and are there additional active consumers?
- What production transition plan will be required before a future launch?
