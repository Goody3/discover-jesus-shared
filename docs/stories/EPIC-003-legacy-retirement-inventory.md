# EPIC-003 legacy retirement inventory

- **Date:** 2026-11-14
- **Status:** Source inventory verified; additive UUID management lookup and
  web migration approved and merged on November 14, 2026, with passing `dev`
  CI and a same-day live client/backend integration session. The hosted
  development database's stored content is inventoried below.
  Pre-production migration mappings and the in-scope consumer inventory remain
  incomplete. No removal is authorized by this document.
- **Stories:** [STORY-006](STORY-006-deprecate-slug-based-page-retrieval.md),
  [STORY-017](STORY-017-retire-legacy-component-type-fallbacks.md)
- **Boundaries:** [ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md),
  [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md),
  and the owner-approved [ADR 0006](../../architecture/decisions/0006-uuid-page-identity-and-linked-content-migration.md).

## Evidence scope

The source trees inspected match these merged `dev` revisions:

- Backend: `6c3d4ccc1560d02d0f34cc97ae7493fc228c7268`.
- Web: `20a6044acc99df65afe1f6bb3ce2e195fffe33dd`.
- iOS: `57f91588e41c7fd69580a0b6c65896879b5e38cd`.

The source revisions below establish code-level findings, not runtime usage
or a list of every consumer. A separate read-only audit of the hosted
development database is recorded later in this document. Operational owners,
imports/scripts outside these repositories, Android or other clients, deployed
versions, and request-log retention remain unknown.

## STORY-006: page lookup and navigation

| Surface | Verified dependency and required behavior | Replacement / gap |
| --- | --- | --- |
| Web management page loading | `App` selects a page by slug and calls `GetPage`. Needs `id`, `publicId`, `slug`, `title`, `isPublished`, and inline `components` with integer IDs, sort order, props, bindings, and actions. Supports unpublished pages and management edits/deletes. | Published-content delivery is not a replacement. Propose a UUID-addressed management lookup returning the existing `PageResponse`. |
| Backend page creation | `CreatePage` returns a `Location` header pointing to `/api/pages/{slug}` as well as a `PageSummary` body. | After a UUID management lookup exists, coordinate changing `Location` to it; do not leave a dangling slug link on removal. |
| Backend SQL integration tests | Test lookup after create, slug rename, missing page, and deletion. | Add parity tests for UUID management lookup; update route tests for the coordinated removal cutover. |
| Web published preview and draft editor | Already use `publicId` for published and draft page-content routes. Preview resolves UUIDs and publication status from page summaries. | Preserve existing locale, draft lifecycle, and published-content behavior; no route substitution needed. |
| iOS page list and published-page loading | Uses `GET /api/pages` for summaries and public UUID for published content. No slug-detail HTTP call found in app source. | Keep these operations; do not confuse retaining a `slug` field with consuming slug-detail retrieval. |
| iOS navigation | `targetPage` compares `action.pageId` to `PageSummary.publicId`, then uses case-insensitive `source.legacyProps.target`. If a supplied UUID has no matching summary, slug fallback is still attempted. | The current backend/iOS runtime resolves typed destinations against backend `PublicId`. The owner approved that shared OpenAPI `Page.id`, dynamic page-schema `id`, and `action.pageId` use this same UUID; the slug fallback remains until stored content and clients are migrated. Malformed UUID decoding already fails; a missing UUID remains an intentional decoder compatibility gap. |
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
  and [navigation UUID/slug resolution](https://github.com/Goody3/discover-jesus-ios/blob/57f91588e41c7fd69580a0b6c65896879b5e38cd/DiscoverJesus/DiscoverJesus/PageContentLogic.swift#L68).

### Approved additive migration increment

On November 14, 2026, the owner approved the UUID management lookup and web
migration while retaining existing legacy behavior for that increment. It did
not approve route or fallback removal or certify complete consumer coverage.

1. Add `GET /api/pages/by-public-id/{pageId}` with a UUID route constraint,
   distinct operation ID, and the same `PageResponse` shape and management
   visibility as slug lookup. Keep integer mutation routes and slug values.
2. Verify identical inline-component ordering, bindings/actions, draft-page
   visibility, missing/deleted-page behavior, and identity after slug rename.
3. Generate/verify backend metadata, update the web's pinned contract, and
   switch web management selection/loading to `publicId`. Update creation
   `Location` once the replacement is available.
4. Retain the slug route and iOS navigation fallback for this additive
   increment. The later pre-production cutover is a separate coordinated
   change and is not authorized by this approval.

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
  The source-page field has 7 distinct slug values; navigation targets have 6.
  All embedded slugs resolved against current `Pages.Slug` at inspection, but
  no schema constraint enforces this; renaming a page slug can silently
  orphan stored navigation targets. Client adoption of the UUID management
  lookup does not remove this stored dependence; retiring slug navigation
  requires migration of these stored values.

External environments, producers, and consumers remain uninventoried; no raw
content was copied into this document.

## Pre-production cutover and migration plan

**Status:** Approved directions are recorded in
[ADR 0006](../../architecture/decisions/0006-uuid-page-identity-and-linked-content-migration.md).
Role vocabulary, role-specific schema constraints, migration implementation
details, validation gates, and authorization to execute the data migration or
remove legacy behavior remain open.

**Owner-approved direction (2026-11-14):** No backward-compatibility period is
required before production. Scope the cutover to the hosted development
database, backend, web, and iOS, plus any additional consumers the owner names.
Use backend `PublicId` as the shared page identity; place semantic roles in a
top-level `role`; migrate all 55 stored component revisions by preserving each
original and creating a linked migrated representation; and treat `source.page`
as a live page reference to migrate to the same UUID. Exact role values and
role-specific fields, backend link storage and active-revision selection,
validation gates, and additional consumers remain to be defined. No data
migration or legacy-behavior removal is authorized by this direction. Any
future production rollout needs a separate transition plan.

The mappings below are candidates based on the hosted development database
snapshot and checked-in shared schemas. The shared component schema defines
generic types (`text`, `image`, `button`, `stack`, and others) and now exposes
the approved top-level `role` field, but does not define role values or their
property mappings. In addition, the iOS source inventory confirms
`source.legacyType` can take precedence over `type`. Therefore an observed
pair is not proof that removing the field preserves behavior; define the
replacement behavior first, migrate the active content, and validate both
known clients at cutover.

### STORY-017: candidate component mapping

| Observed `type` / `source.legacyType` | Candidate treatment | Status and required review |
| --- | --- | --- |
| `button` / `Button` (12 current) | Keep generic `button`; map `legacyProps.text` to `value` and a validated internal `target` to `action.pageId`. No additional role is proposed for a generic button. | Confirm label semantics and preserve action behavior; do not infer a page from a target that fails the approved lookup. |
| `button` / `NBButton` (4 current) | Keep generic `button`; propose `role=navigation-item`, map text to `value`, and map validated target to `action.pageId`. | `attribute=text` is not mapped. Verify how navigation-bar membership and parent/child grouping are represented before conversion. |
| `text` / `Text` (8 current) | Keep generic `text`; map text to `value` and `attribute` to proposed `style.textSize` (`small`, `medium`, `large`). | All three size labels occur in this type's history. Approve lower-case normalization and confirm this is presentation-only. |
| `text` / `Header` (12 current) | Keep generic `text`; propose `role=heading`, map text to `value`, and map H1/H2/H3 to `headingLevel` 1/2/3. | Existing `style.role=heading` is evidence, not the new field location. Validate heading hierarchy and rendering in both clients. |
| `text` / `BibleVerse` (2 current) | Keep generic `text`; propose `role=scripture-reference`, map text to `value`, and retain `reference` and `version` as explicit semantic fields. | Field names and version semantics need approval. Do not fetch or alter verse text as part of this structural mapping. |
| `text` / `title` (1 current) | Keep generic `text`; propose `role=title`, map text to `value`, and normalize `alignment`/`fontSize` into presentation style. | Only `alignment=center` and `fontSize=large` occur in this one record; this is insufficient to define the complete allowed values. |
| `stack` / `List` (4 current) | Keep generic `stack`; propose `role=list`, map each string in `legacyProps.list` to an ordered text child, and map `ol`/`ul` to `style.listStyle=ordered`/`unordered`. | All four historical arrays contain three strings. Preserve order and confirm string-only items are complete; the meaning of `legacyProps.text` (for example, title or label) remains unknown. |
| `stack` / `NavBar` (2 current) | Keep generic `stack`; propose `role=navigation`, with child buttons represented as `role=navigation-item`. | Current iOS groups NBButtons by matching labels; stored ownership/grouping must be verified before modeling the relationship as children. `text` meaning is unknown; `attribute` is empty in both records. |
| `image` / `Media` (2 current) | Keep generic `image`; map URL to `src`. Consider mapping `text` to `altText` only if its meaning is confirmed. | `attribute=image` is observed, but `text` could be alternative text or a caption. Do not migrate it to either field without evidence. |

Aggregate-only field inventory across the 55 revisions: `Button` has `text`
and `target`; `NBButton` has `text`, `target`, and `attribute`; `Text` has
`text` and `attribute`; `Header` has `text` and `attribute`; `BibleVerse` has
`text`, `reference`, and `version`; `List` has `text`, `list`, and `attribute`;
`NavBar` has `text` and `attribute`; `Media` has `text`, `url`, and
`attribute`; and `title` has `text`, `alignment`, and `fontSize`. This is a
property-name/count summary, not a complete semantic mapping.

Additional aggregate value-shape evidence across history: `Header.attribute`
contains H1 (6), H2 (4), and H3 (2); `Text.attribute` contains Small (2),
Medium (4), and Large (2); `List.attribute` contains `ol` (2) and `ul` (2).
Each of the four `List` revisions has three string items. `NBButton.attribute`
is `text` in all six occurrences; `NavBar.attribute` is empty in both
occurrences; `Media.attribute` is `image` in both. The sole `title` record has
`alignment=center` and `fontSize=large`. These are observed values, not
complete allowed-value sets.

On November 14, 2026, the owner accepted the table as a candidate mapping
baseline and directed that unresolved items remain blocked. This does not make
the table a final payload contract: generic component types and the top-level
`role` location are approved directions, while role values, field names,
normalization rules, and representation changes remain candidates. Do not add
role enums or migrate records until the final schema and all required mappings
are approved.

The owner selected a linked migrated representation for each of the 55
revisions while preserving each original unchanged. The backend must still
define the durable source link and current-revision selection. Unknown
component handling remains required.

### STORY-006: candidate slug migration

1. Use backend `PublicId` for page identity as directed by the owner. The
   shared OpenAPI `Page.id`, dynamic page-schema `id`, and `action.pageId` now
   document this identity. The inspected iOS client matches `action.pageId` to
   `PageSummary.publicId`, and the backend has UUID-addressed published and
   management lookups.
2. Resolve the 24 stored `source.legacyProps.target` values, convert confirmed
   internal destinations to the approved typed navigation action, and preserve
   other action semantics. Fix or explicitly discard unresolved/non-page
   targets; do not infer destinations from string shape alone.
3. Treat `source.page` as a live page reference as directed by the owner.
   Resolve all 55 values and migrate each to backend `PublicId`. At inspection,
   all 55 resolved across 7 distinct slugs; repeat the mapping check immediately
   before migration.
4. Preserve every original revision unchanged and create one migrated
   representation linked to its source revision, as directed by the owner.
   Define durable link storage and current-revision selection in the backend
   implementation. Migrate active drafts and published content before
   switching known consumers. The legacy slug route and client fallback can
   then be removed in the coordinated non-production cutover; no deprecation
   period is proposed.
5. Validate the backend, web, and iOS against the migrated content and remove
   all legacy-route callers in the same planned transition. Keep the separate
   user-facing page slug field/URLs unless separately approved.

### Proposed cutover and validation

- Coordinate the backend change and dependent web/iOS updates as one bounded
  non-production transition, following accepted [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).
  Temporary incompatibility is acceptable; no public deprecation signal,
  notice duration, zero-traffic observation window, or backward-compatible
  fallback is required.
- Before cutover, verify the approved payload mappings and migrate active
  content. The cutover gate is successful API/client tests plus end-to-end
  checks for page selection, navigation, rendering, editor saves, and error
  handling in every known client. If a check fails, forward-fix the
  non-production environment; do not assume database rollback is safe after
  content/schema changes.
- Keep any future production-release communications, monitoring, recovery, and
  compatibility requirements out of this proposal; they require separate
  owner approval before production exists.

### Remaining design and execution gates

- Define the precise role values, role-specific field names and allowed
  values, and mappings for every legacy property in the table.
- Specify backend storage for the original-to-migrated revision link and how
  consumers select the migrated current representation.
- Confirm any additional active consumers beyond the hosted development
  database, backend, web, and iOS, and define the cutover validation matrix.
- Complete a fresh aggregate inventory and a dry-run mapping report before
  executing migration; use the accepted non-production forward-fix approach.

## Outstanding evidence and removal gates

The first two inventory gates are partially satisfied for the known
development environment. Remaining gates concern correct migration and
validation, not a backwards-compatibility window:

1. Confirm whether any additional environments, clients, or producers beyond
   the approved hosted development database, backend, web, and iOS are in the
   pre-production scope. External consumers are not assumed to be supported
   before launch; include any the owner identifies.
2. Obtain authorized, read-only aggregate inventory for each environment:
   legacy fields/effective-type conflicts, navigation targets and unresolved
   slugs, locale coverage, drafts, published and historical page revisions,
   reusable drafts/revisions, and nested nodes. No raw content or private data
   belongs in this document. The hosted development database is inventoried
   above; counts for any other environment remain **unknown**, not zero.
3. Approve current component/navigation representations and migration mappings.
   Resolve ambiguous records and preserve unknown-component safety. Define how
   consumers access linked migrated representations without changing originals.
4. Create linked migrated representations for all 55 selected revisions,
   switch active-content selection, and update every known in-scope producer
   and consumer.
5. Run focused tests and end-to-end checks for all known clients against the
   migrated backend/content; remove legacy route and fallback code as part of
   the coordinated non-production cutover.

No staged deprecation or compatibility support is proposed for this
pre-production work. A separate transition plan is required before any future
production rollout.
