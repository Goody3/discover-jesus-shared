# STORY-006. Deprecate slug-based page retrieval

- **Status:** In progress; UUID management migration approved and implemented locally
- **Epic:** [EPIC-003. Contract integrity across products](../epics/EPIC-003-contract-integrity-across-products.md)
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
- Retiring slug-based navigation fallback only after its consumers and content
  satisfy the same approved compatibility and removal criteria.
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
- The iOS repository is `Goody3/discover-jesus-ios`; confirm operational owners
  and any additional runtime consumers during the inventory.
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

## Implementation status

- [Source inventory and proposed replacement](EPIC-003-legacy-retirement-inventory.md)
  records the verified web management caller, backend creation `Location`,
  tests, iOS UUID delivery, and separate slug-navigation dependency.
- The web requires inline management data, including unpublished pages;
  published-content delivery is not a drop-in replacement. On November 14,
  2026, the owner approved an additive UUID-addressed management lookup and
  web migration while retaining all legacy compatibility.
- Backend revision `f0466e0797e541784ce077245005c62228529361` adds
  `GET /api/pages/by-public-id/{pageId}` (`GetPageByPublicId`) using the same
  query/response mapping as slug lookup. Creation `Location` targets the
  stable UUID lookup; integer mutation routes and slug values remain.
- The web pins that backend contract and selects/loads management pages by
  `publicId`. Slug rename preserves selected identity; lookup errors are
  surfaced without silently retrying the legacy route.
- Submitted as draft PRs targeting `dev`:
  [discover-jesus-backend#12](https://github.com/Goody3/discover-jesus-backend/pull/12)
  and [discover-jesus-web#9](https://github.com/Goody3/discover-jesus-web/pull/9).
  Merge/deploy the backend increment before the dependent web change.
- Local validation passed: 32 database-independent backend tests, backend test
  project compilation including the new SQL integration test, and all 39 web
  tests plus generated-type check, type-check, lint, formatting, build, and the
  High/Critical audit gate. Seven existing Moderate audit findings remain.
  The new SQL integration test checks UUID/slug response parity, unpublished
  visibility, inline ordering/bindings/actions, creation `Location`, identity
  after slug rename, and missing/malformed/deleted UUIDs. Running it requires
  the SQL Server-backed CI environment; it has not been run locally.
- Stored-content counts, external consumers, operational ownership, notice,
  monitoring, and removal criteria remain unverified/unapproved. No deprecation
  signal or removal is included. Hosted CI, integration validation,
  review, merge, and merged-branch checks remain for this increment; full story
  closure also requires the remaining compatibility/removal gates.
