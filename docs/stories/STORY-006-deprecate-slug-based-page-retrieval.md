# STORY-006. Deprecate slug-based page retrieval

- **Status:** In progress; additive UUID management migration merged, CI-verified, and live-validated, removal remains gated
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
inventoried before the legacy route is removed. The appropriate replacement
for each use case and the coordinated pre-production cutover checks remain to
be decided.

## Acceptance criteria

1. The backend and client owners inventory all known callers of
   `GET /api/pages/{slug}`, including the iOS app, web interface, and any
   additional consumers discovered during implementation.
2. The inventory documents each caller's required fields and behavior, and
   maps each use case to an approved UUID-based API operation. It explicitly
   accounts for response-shape differences, localized content, and any legacy
   management behavior that has no replacement.
3. Before the pre-production cutover, the platform owner approves the
   replacement operation, UUID identity mapping, stored-content migration,
   affected clients, and focused validation checks. A deprecation period or
   backward-compatible fallback is not required for this non-production
   transition.
4. The backend and in-scope clients switch to approved UUID-based operations
   in a coordinated non-production sequence. Remove the legacy route and
   slug-navigation fallback after the stored content and known callers are
   migrated and validated; keep user-facing slug fields/URLs unless separately
   approved.
5. Backend and client tests cover UUID-based use cases and the post-cutover
   contract. Validate each known in-scope client end-to-end against the
   migrated backend/content. Any future production rollout requires its own
   approved compatibility and transition plan.
6. Backend API documentation and any applicable shared contracts accurately
   describe the post-cutover routes without conflating the backend contract
   with the separate shared OpenAPI contract.

## Scope

### Included

- Inventorying page-lookup consumers and their data/behavior requirements.
- Selecting UUID-based replacements per use case, including any required
  contract changes.
- Coordinating a breaking backend/client migration and route/fallback removal
  in non-production.
- Retiring slug-based navigation fallback after active content and known
  consumers use the approved UUID representation.
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
- No production environment exists, and a backwards-compatibility period is
  not required for the planned non-production cutover. Do not remove the route
  or fallback until the approved data migration and known-client validation
  gates are satisfied.

## Completion checks

- [ ] Consumer inventory, replacement mapping, and unresolved behavior gaps
      are reviewed by the platform and implementation owners.
- [ ] Replacement identity, content migration, affected consumers, and
      non-production cutover validation are approved and documented.
- [ ] In-scope clients are migrated and their UUID-based page retrieval is
      validated against the backend.
- [ ] Backend API documentation and focused backend/client tests reflect the
      post-cutover contract.
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
- Merged to `dev` on November 14, 2026:
  [discover-jesus-backend#12](https://github.com/Goody3/discover-jesus-backend/pull/12)
  and [discover-jesus-web#9](https://github.com/Goody3/discover-jesus-web/pull/9).
  The backend increment merged before the dependent web change.
- Local validation passed: 32 database-independent backend tests, backend test
  project compilation including the new SQL integration test, and all 39 web
  tests plus generated-type check, type-check, lint, formatting, build, and the
  High/Critical audit gate. Seven existing Moderate audit findings remain.
  The new SQL integration test checks UUID/slug response parity, unpublished
  visibility, inline ordering/bindings/actions, creation `Location`, identity
  after slug rename, and missing/malformed/deleted UUIDs. Running it requires
  the SQL Server-backed CI environment; it has not been run locally.
- [Backend CI run 43627726578](https://github.com/Goody3/discover-jesus-backend/actions/runs/43627726578)
  passed at `f0466e0797e541784ce077245005c62228529361` on November 14, 2026,
  including SQL Server-backed tests, OpenAPI verification/artifact upload,
  migration-drift check, coverage, and dependency scanning.
- [Web CI run 43627726420](https://github.com/Goody3/discover-jesus-web/actions/runs/43627726420)
  passed at `099c33c02fb014015455d39da6ac361940429289` on November 14, 2026,
  including pinned generated-type verification and the existing quality checks.
- [Backend merged-branch CI run 43628450915](https://github.com/Goody3/discover-jesus-backend/actions/runs/43628450915)
  passed at `75fbdbc8bb69acef24f0f636d693c589188e8259`.
  [Web merged-branch CI run 43628557431](https://github.com/Goody3/discover-jesus-web/actions/runs/43628557431)
  passed at `e895d45d5f2b8bb6ee124e0d00b41be8294aef64`.
  Both results were verified on November 14, 2026; shared tracking PR #16
  also merged. This verifies the additive increment on merged source, not a
  deployment or live client/backend integration session.
- A live client/backend integration session ran on November 14, 2026: merged
  backend `75fbdbc8bb69acef24f0f636d693c589188e8259` served locally against a
  hosted SQL Server 2022 development database (all three checked-in migrations
  already applied), with the merged web
  `e895d45d5f2b8bb6ee124e0d00b41be8294aef64` dev server proxied to it. Using a
  throwaway page created and deleted within the session: direct API checks
  passed (create 201 with `publicId`, UUID lookup 200, legacy slug lookup 200,
  slug rename 200, UUID lookup after rename returned the same identity, old
  slug 404, unknown UUID 404), and the web UI loaded and reloaded the selected
  page via `GET /api/pages/by-public-id/{pageId}` only — including after a
  slug rename performed in the UI — issuing no legacy slug retrieval. Missing
  locale content 404s surfaced as empty states. This was a local development
  session against the hosted database, not a deployment.
- Stored-content counts for the hosted development database are recorded in
  the [legacy retirement inventory](EPIC-003-legacy-retirement-inventory.md):
  all 55 reusable component revisions embed a `source.page` slug and 24 embed
  a slug navigation target, so stored content still depends on slugs even
  after client migration. At inspection all 55 source-page values and all 24
  navigation targets resolved to current pages; repeat the mapping check
  immediately before migration. No content migration or route/fallback
  removal is included.
- On November 14, 2026, the owner approved backend `PublicId` as the shared
  page identity, a linked migrated representation for each of the 55 immutable
  revisions, and `source.page` as a live page reference. See
  [ADR 0006](../../architecture/decisions/0006-uuid-page-identity-and-linked-content-migration.md).
  Role and migration implementation details, fresh data validation, and
  cutover checks remain open. No route or fallback removal is authorized.
