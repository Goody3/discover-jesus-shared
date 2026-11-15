# EPIC-003 legacy retirement inventory

- **Date:** 2026-11-14
- **Status:** Source inventory verified; additive UUID management lookup and
  web migration approved and merged on November 14, 2026, with passing `dev`
  CI and a same-day live client/backend integration session. The hosted
  development database's stored content is inventoried below.
  Retirement policy and external-consumer inventories remain
  incomplete. No removal is authorized by this document.
- **Stories:** [STORY-006](STORY-006-deprecate-slug-based-page-retrieval.md),
  [STORY-017](STORY-017-retire-legacy-component-type-fallbacks.md)
- **Boundaries:** [ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md)
  and [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).

## Evidence scope

The source trees inspected match these merged `dev` revisions:

- Backend: `6c3d4ccc1560d02d0f34cc97ae7493fc228c7268`.
- Web: `20a6044acc99df65afe1f6bb3ce2e195fffe33dd`.
- iOS: `57f91588e41c7fd69580a0b6c65896879b5e38cd`.

This is a source inventory, not evidence of runtime usage, a database audit,
or a list of every consumer. No database or live endpoint was queried.
Operational owners, imports/scripts outside these repositories, Android or
other clients, deployed versions, and request-log retention remain unknown.

## STORY-006: page lookup and navigation

| Surface | Verified dependency and required behavior | Replacement / gap |
| --- | --- | --- |
| Web management page loading | `App` selects a page by slug and calls `GetPage`. Needs `id`, `publicId`, `slug`, `title`, `isPublished`, and inline `components` with integer IDs, sort order, props, bindings, and actions. Supports unpublished pages and management edits/deletes. | Published-content delivery is not a replacement. Propose a UUID-addressed management lookup returning the existing `PageResponse`. |
| Backend page creation | `CreatePage` returns a `Location` header pointing to `/api/pages/{slug}` as well as a `PageSummary` body. | After a UUID management lookup exists, coordinate changing `Location` to it; do not leave a dangling slug link on removal. |
| Backend SQL integration tests | Test lookup after create, slug rename, missing page, and deletion. | Add parity tests for UUID management lookup; retain explicit slug compatibility tests until approved removal. |
| Web published preview and draft editor | Already use `publicId` for published and draft page-content routes. Preview resolves UUIDs and publication status from page summaries. | Preserve existing locale, draft lifecycle, and published-content behavior; no route substitution needed. |
| iOS page list and published-page loading | Uses `GET /api/pages` for summaries and public UUID for published content. No slug-detail HTTP call found in app source. | Keep these operations; do not confuse retaining a `slug` field with consuming slug-detail retrieval. |
| iOS navigation | `targetPage` prefers a matching `action.pageId`, then uses case-insensitive `source.legacyProps.target`. If a supplied UUID has no matching summary, slug fallback is still attempted. | Migrate stored destinations to typed UUID actions before removing fallback; malformed UUID decoding already fails. Missing UUID remains an intentional decoder compatibility gap. |
| Generated/pinned contracts and examples | Backend OpenAPI, generated web types, and vendored web/iOS snapshots include the slug route. Synthetic fixtures test slug navigation fallback. | Refresh identified contract inputs intentionally after approved changes. Historical snapshots do not prove an active runtime caller. Preserve regressions until migration gates pass. |

### Source references

- [Web page selection/loading](https://github.com/Goody3/discover-jesus-web/blob/20a6044acc99df65afe1f6bb3ce2e195fffe33dd/src/App.tsx#L126)
  and [management UI](https://github.com/Goody3/discover-jesus-web/blob/20a6044acc99df65afe1f6bb3ce2e195fffe33dd/src/App.tsx#L489).
- [Backend route registration](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Endpoints/ContentEndpoints.cs#L16),
  [creation Location](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Endpoints/ContentEndpoints.cs#L163),
  [response mapping](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Endpoints/ContentEndpoints.cs#L656),
  and [integration tests](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Tests/ContentEndpointIntegrationTests.cs#L43).
- [iOS summary loading](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/ContentView.swift#L130),
  [UUID delivery URL](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/ContentView.swift#L224),
  and [navigation precedence](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/PageContentLogic.swift#L68).

### Approved additive migration increment

On November 14, 2026, the owner approved the UUID management lookup and web
migration while retaining all legacy compatibility. This does not approve the
outstanding deprecation/removal policy or certify complete consumer coverage.

1. Add `GET /api/pages/by-public-id/{pageId}` with a UUID route constraint,
   distinct operation ID, and the same `PageResponse` shape and management
   visibility as slug lookup. Keep integer mutation routes and slug values.
2. Verify identical inline-component ordering, bindings/actions, draft-page
   visibility, missing/deleted-page behavior, and identity after slug rename.
3. Generate/verify backend metadata, update the web's pinned contract, and
   switch web management selection/loading to `publicId`. Update creation
   `Location` once the replacement is available.
4. Retain the slug route and iOS navigation fallback. Deprecation signals,
   notice period, monitoring window, and final removal are separate approvals.
   Do not infer them from permission to add a replacement.

The backend implementation is committed at
`f0466e0797e541784ce077245005c62228529361`. The web pins that input and uses
public UUID selection, including after slug rename. Both product PRs merged
and passed `dev` CI, and a live client/backend integration session on
November 14, 2026 validated the merged UUID management flow against a hosted
development database; see the linked STORY-006 verification evidence.
The inventory table above describes
the inspected merged baseline, not a claim that those migrations are deployed.

## STORY-017: component-type and adjacent compatibility

| Surface | Verified finding | Migration constraint |
| --- | --- | --- |
| iOS classification | `source.legacyType` takes precedence over `type`, not merely a fallback for absent `type`. Navigation/list styling roles can also override classification. | Blindly dropping `legacyType` can turn an intentional button into text. Determine effective type per record; conflicting types require explicit review. |
| iOS navigation-bar assembly | `navBarItems` gathers buttons specifically by `source.legacyType == NBButton`, then matches child labels. Top-level NBButtons are suppressed when a navigation bar exists. | Updating only `sectionKind` can silently remove working navigation items. A replacement needs an agreed navigation-item representation and rendering tests. |
| iOS display properties | Uses `legacyProps.text`, URL, header level/attribute, ordered-list marker, BibleVerse reference/version, and navigation target. | These are adjacent compatibility dependencies, not all authorized for removal by a component-type-only story. BibleVerse reference/version and list titles have no agreed canonical mapping. |
| Web authoring | Section JSON and reusable-component JSON can be edited directly; props and legacy action configs are also arbitrary JSON. | Source checks alone cannot prove new writes are legacy-free. A producer guard or explicitly reviewed transformation would need a separate approved contract. |
| Web preview | Uses current `type` strings; no `legacyType`/`legacyProps` branch found. Its supported set differs from iOS (for example stack/row/column vs specialized header/list/BibleVerse/NavBar). | A conversion valid for iOS may still lose web preview behavior. Shared component enum is not an authoritative schema for all iOS sections. |
| Backend storage and delivery | Stores section/component JSON, validates selected structural/navigation rules, and expands references. No legacy-field normalization found in the inspected service. | All drafts and immutable revisions may carry legacy fields; checking only current published pages is insufficient. Do not silently rewrite immutable revisions or discard old content. |
| Synthetic tests | Existing tests intentionally exercise legacy overrides, slug navigation, and unknown types. | Preserve them until approved migration evidence exists. Unknown-component safety must remain after cleanup. |
| Historical standardization plan | iOS contains a proposal using `component/content/attributes/actions/layout/meta`. Current decoder still uses `type/value/source/action`. | The proposal is not an accepted contract and must not be implemented or treated as current behavior without approval. |

### Source references

- [iOS classifier](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/PageContentLogic.swift#L28),
  [navigation-bar assembly](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/ContentView.swift#L469),
  and [display-property dependencies](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/ContentView.swift#L508).
- [Web preview cases](https://github.com/Goody3/discover-jesus-web/blob/20a6044acc99df65afe1f6bb3ce2e195fffe33dd/src/preview.tsx#L88).
- [Backend JSON storage](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Data/DiscoverJesusDbContext.cs#L80),
  [delivery expansion](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Services/ReusableContentService.cs#L775),
  and [node validation](https://github.com/Goody3/discover-jesus-backend/blob/6c3d4ccc1560d02d0f34cc97ae7493fc228c7268/Services/ReusableContentService.cs#L991).
- [Historical iOS standardization proposal](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/Component-Attribute-Standardization-Plan.md).

## Stored-content inventory: hosted development database

On November 14, 2026, read-only aggregate queries ran against the platform's
hosted development SQL Server database, whose schema matched the three
checked-in backend EF Core migrations. This is the only platform content
store known to the owner today; no production environment exists. Counts are
a point-in-time snapshot of that single database and authorize nothing.

- **Pages:** 13, all published. Page slugs exist only in `Pages.Slug`; no
  page-section, embedded-component, binding, or action payload contains
  `legacyType`, `NBButton`, or slug references (24 embedded-component rows,
  15 page-content drafts, 26 published page revisions, 0 binding and 0 action
  rows at inspection).
- **Reusable components:** 24 definitions, none withdrawn, across 2 locales;
  55 immutable revisions of which 47 are current (latest per
  definition/locale); 0 reusable-component drafts at inspection. Published
  content holds 69 component references; drafts hold 52.
- **`legacyType`:** every reusable component revision (55/55, including all
  47 current) contains exactly one top-level `source.legacyType`; no nested
  legacy nodes were found. Current-revision distribution: Button 12,
  Header 12, Text 8, NBButton 4, List 4, Media 2, NavBar 2, BibleVerse 2,
  `title` 1. Each record pairs with its generic `type` in one of nine
  observed mappings (button↔Button/NBButton, text↔Header/Text/BibleVerse/
  `title`, stack↔List/NavBar, image↔Media). The lowercase `title` value is
  nonstandard and needs explicit mapping review.
- **Stored slug coupling (STORY-006):** all 55 revisions embed a
  `source.page` page slug, and 24 revisions (18 Button, 6 NBButton; 16 of
  them current) embed a `source.legacyProps.target` navigation page slug.
  All embedded slugs resolved against current `Pages.Slug` at inspection, but
  no schema constraint enforces this; renaming a page slug can silently
  orphan stored navigation targets. Client adoption of the UUID management
  lookup does not remove this stored dependence; retiring slug navigation
  requires an approved migration of these stored values.

External environments, producers, and consumers remain uninventoried; no raw
content was copied into this document.

## Draft compatibility and retirement proposal — owner review required

**Status:** Proposal only; not an approved contract, migration plan, deprecation
notice, or authorization to change data or remove compatibility.

The mappings below are candidates based on the hosted development database
snapshot and checked-in shared schemas. The shared component schema defines
generic types (`text`, `image`, `button`, `stack`, and others); it does not
define the observed legacy display roles or their property mappings. In
addition, the iOS source inventory confirms `source.legacyType` can take
precedence over `type`. Therefore an observed pair is not proof that deleting
the legacy field preserves behavior.

### STORY-017: candidate component mapping

| Observed `type` / `source.legacyType` | Candidate treatment | Status and required review |
| --- | --- | --- |
| `button` / `Button` (12 current) | Keep `button` as the generic type; map text and action fields explicitly. | Candidate only. Determine how `legacyProps.text`, `attribute`, and other properties map; validate iOS and web behavior. |
| `button` / `NBButton` (4 current) | Keep button behavior and represent navigation-bar membership/role explicitly, separately from generic button type. | Blocked. Current shared component schema has no navigation-bar item role; do not erase this distinction or infer it from layout. |
| `text` / `Text` (8 current) | Keep `text`; map displayed value explicitly. | Candidate only. Confirm `legacyProps` and binding semantics preserve the current rendered text. |
| `text` / `Header` (12 current) | Keep `text` with an explicit heading role and level. | Blocked. The shared component schema has no heading role/level; resolve `legacyProps.attribute`, values, and styling before conversion. |
| `text` / `BibleVerse` (2 current) | Preserve Bible-verse semantics and reference/version as structured data. | Blocked. Do not reduce to plain text until the canonical representation and rendering behavior are approved. |
| `text` / `title` (1 current) | Quarantine for manual classification; do not normalize automatically. | Blocked. The lowercase legacy value is nonstandard and its intended role is unconfirmed. |
| `stack` / `List` (4 current) | Preserve list role and item order; use a generic layout only if list semantics remain representable. | Blocked. The shared component schema has no list type; ordered/unordered behavior and marker metadata need mapping. |
| `stack` / `NavBar` (2 current) | Preserve navigation-bar role and its child-item relationship explicitly. | Blocked. A generic stack alone does not establish navigation behavior; coordinate with the `NBButton` mapping. |
| `image` / `Media` (2 current) | Keep `image` if the media source and presentation properties map without loss. | Candidate only. Verify the source/asset mapping and required legacy properties. |

Proposed process: define and review a lossless mapping for each role; transform
mutable drafts and active published content first; compare decoded/rendered
behavior in iOS and web; and retain a compatibility reader for historical
revisions until the owner decides those revisions are no longer deliverable or
have been safely superseded. Do not rewrite immutable revisions as part of an
unreviewed bulk update. Producers should be changed to emit only approved
current shapes before the legacy reader is considered for removal. Unknown
component handling remains required.

### STORY-006: candidate slug migration

1. Keep the existing slug route and navigation fallback during migration.
   The additive UUID management lookup does not replace typed navigation or
   change the UUID identity decision.
2. Resolve and review every stored `source.legacyProps.target` as a
   destination. The 24 currently observed targets resolve by slug, but the
   existing shared navigation proposal uses UUID `Page.id`, while the backend
   management model exposes `PublicId`; their equivalence is not established.
   Approve the authoritative identity and an explicit crosswalk before writing
   UUID actions. Do not silently substitute backend `PublicId` for shared
   `Page.id`.
3. Replace only confirmed internal-page targets with the approved typed
   navigation action, preserving conditional behavior and other action
   semantics. Quarantine ambiguous, missing, or non-page destinations for
   owner review; do not convert them by string shape alone.
4. Treat `source.page` separately from navigation targets. It appears in all
   55 revisions, but its producer/consumer semantics are not fully established.
   Retain it as provenance unless and until owners decide it is a live page
   reference and approve a stable replacement.
5. Validate every in-scope draft and published payload, including historical
   revisions that can still be served. Keep slug compatibility wherever
   unmigrated content or an inventoried consumer still depends on it.

### Candidate notice and removal policy

These are starting values for owner discussion, not approved commitments:

- Publish the approved replacement and migration instructions before marking
  the legacy route deprecated. Then document deprecation in the owning
  backend API contract (`deprecated: true`) and emit the owner-approved
  runtime signal. Candidate runtime signals are the HTTP `Deprecation` and
  migration-link `Link` headers; add a `Sunset` header only after an owner
  approves the date. Do not imply the separate shared OpenAPI contract
  describes this backend route.
- Proposed minimum notice: **90 calendar days and at least one supported
  client release cycle, whichever is longer**, measured from the first
  deprecation notice in each affected environment. No removal date is set.
- Before removal, require **30 consecutive days with zero observed calls** to
  the legacy route from identified callers in each environment, explicit
  confirmation from operational owners that all consumers are inventoried,
  and successful replacement integration checks. If reliable per-route
  observation is unavailable, the gate is unmet; absence of telemetry is not
  evidence of zero use.
- For stored content, require zero unresolved internal-page targets in all
  payloads still eligible for delivery, a documented disposition for historical
  immutable revisions, and successful iOS/web behavior checks. Keep fallback
  behavior where history must remain deliverable but cannot be migrated.
- Retain an owner-approved recovery path and the legacy implementation through
  the notice/observation window. This proposal assumes neither production
  telemetry nor a release process is currently available; owners must identify
  those mechanisms before adopting the policy.

### Owner decisions requested

- Confirm this database's environment/scope and identify any other content
  stores, producers, clients, or third-party consumers.
- Approve, revise, or reject each candidate component mapping, especially
  `NBButton`, `Header`, `BibleVerse`, `List`, `NavBar`, and lowercase `title`.
- Decide whether shared `Page.id` and backend `PublicId` are the same
  authoritative navigation identity, or specify the approved mapping.
- Confirm whether `source.page` is provenance or a live dependency, and whether
  immutable history must remain client-deliverable.
- Approve or replace the proposed notice duration, observation window,
  deprecation signal, telemetry owner, measurable gates, and recovery plan.

## Outstanding evidence and removal gates

These are proposed gates, not an approved notice/monitoring policy:

1. Identify operational owners and all environments/clients/producers. Confirm
   external consumers explicitly rather than interpreting no source match as
   no usage.
2. Obtain authorized, read-only aggregate inventory for each environment:
   legacy fields/effective-type conflicts, navigation targets and unresolved
   slugs, locale coverage, drafts, published and historical page revisions,
   reusable drafts/revisions, and nested nodes. No raw content or private data
   belongs in this document. The hosted development database is inventoried
   above; counts for any other environment remain **unknown**, not zero.
3. Approve current section/navigation representations and migration mappings.
   Quarantine ambiguous records; preserve unknown-component safety. Decide
   how immutable revision history is retained or superseded.
4. Migrate producers and affected content; validate before/after behavior in
   web and iOS, not just JSON syntax or schema metadata.
5. Approve deprecation signals, notice duration, usage observation window,
   telemetry ownership, acceptable unknown/conflict counts, and recovery.
   No date or zero-usage claim is established by this source inventory.
6. Require verified UUID replacements for known callers, approved zero
   remaining dependencies (including historical content that can still be
   served), successful integration checks, and explicit removal authorization.

Until then, retain slug lookup, slug navigation, `legacyType`, and adjacent
legacy props. Adding a replacement is not permission to delete compatibility.
