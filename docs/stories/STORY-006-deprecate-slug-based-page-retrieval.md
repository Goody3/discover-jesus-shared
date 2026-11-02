# STORY-006. Deprecate slug-based page retrieval

- **Status:** Proposed
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Dependencies:** [STORY-004](STORY-004-update-backend-component-and-navigation-contracts.md),
  [STORY-005](STORY-005-update-web-interface-for-backend-contracts.md)

## User story

As a platform maintainer, I want page consumers to retrieve pages using stable
UUID identities instead of the legacy slug-based page lookup, so that API
clients use the platform's UUID-based page identity consistently.

## Context

The backend currently exposes `GET /api/pages/{slug}` for the legacy page and
inline-component response. The newer
`GET /api/content/pages/{pageId}?locale={locale}` route uses a public page UUID
and returns published, locale-specific page content with reusable components
expanded. These routes have different response shapes and purposes; replacing
one with the other is not necessarily a parameter-only change.

The backend implementation and its API definition are maintained outside this
shared repository. The web interface and the iOS app are known platform
consumers, but all callers and their required page fields and behaviors must be
inventoried before the legacy route is deprecated. The appropriate replacement
for each use case, any deprecation notice period, and the final removal
criteria remain to be decided.

## Acceptance criteria

1. The backend and client owners inventory all known callers of
   `GET /api/pages/{slug}`, including the iOS app, web interface, and any
   additional consumers discovered during implementation.
2. The inventory documents each caller's required fields and behavior, and
   maps each use case to an approved UUID-based API operation. It explicitly
   accounts for response-shape differences, localized content, and any legacy
   management behavior that has no replacement.
3. The platform owner approves the deprecation signal, notice period,
   monitoring, compatibility requirements, and measurable criteria for
   removing the legacy route. No timeline or removal date is assumed by this
   story.
4. Once the compatibility plan is approved, the backend marks the route
   deprecated in its API documentation and emits the approved deprecation
   signals. In-scope clients migrate to their approved replacements during the
   agreed notice period.
5. Backend and client tests cover the UUID-based use cases and the agreed
   legacy-route deprecation behavior. Each in-scope client is validated against
   the backend before removal, and removal occurs only after the approved
   usage/removal criteria are met.
6. Backend API documentation and any applicable shared contracts accurately
   describe the deprecated route and its replacement without conflating the
   backend contract with the separate shared OpenAPI contract.

## Scope

### Included

- Inventorying page-lookup consumers and their data/behavior requirements.
- Selecting UUID-based replacements per use case, including any required
  contract changes.
- Coordinating client migrations and backend deprecation/removal.
- Updating applicable API documentation and focused tests.

### Excluded

- Removing slug values from page records or public URLs unless separately
  approved.
- Removing unrelated slug-based routes or changing page slugs as user-facing
  identifiers.
- Assuming `GET /api/content/pages/{pageId}` is a drop-in replacement for the
  legacy page-management response.

## Dependencies and constraints

- Backend work belongs in `discover-jesus-backend`; client work belongs in
  each consumer's owning repository.
- The source repository and owner for the iOS app, and any additional runtime
  consumers, must be identified during the consumer inventory.
- Follow [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md)
  for transition coordination. Any future production rollout requires its own
  approved transition plan.
- Do not mark the route deprecated in a shared environment before the
  compatibility plan is approved or remove it before the migration gates are
  satisfied.

## Completion checks

- [ ] Consumer inventory, replacement mapping, and unresolved behavior gaps
      are reviewed by the platform and implementation owners.
- [ ] Deprecation and removal criteria, signals, and notice period are
      approved and documented.
- [ ] In-scope clients are migrated and their UUID-based page retrieval is
      validated against the backend.
- [ ] Backend API documentation and focused backend/client tests reflect the
      approved deprecation behavior.
- [ ] The legacy route is removed only if the approved removal criteria have
      been met; otherwise its retained status and remaining blockers are
      documented.
