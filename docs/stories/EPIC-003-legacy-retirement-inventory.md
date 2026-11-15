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
- **Boundaries:** [ADR 0005](../../architecture/decisions/0005-separate-contract-verification-boundaries.md)
  and [ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md).

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
| iOS navigation | `targetPage` compares `action.pageId` to `PageSummary.publicId`, then uses case-insensitive `source.legacyProps.target`. If a supplied UUID has no matching summary, slug fallback is still attempted. | The current backend/iOS runtime therefore resolves typed destinations against backend `PublicId`. The shared navigation decision instead names shared OpenAPI `Page.id`; align or explicitly separate these contracts before migration. Malformed UUID decoding already fails; a missing UUID remains an intentional decoder compatibility gap. |
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

## Draft pre-production cutover proposal — owner review required

**Status:** Proposal only; not an approved contract, migration plan, deprecation
notice, or authorization to change data or remove legacy behavior.

**Owner-approved direction (2026-11-14):** No backward-compatibility period is
required before production. Scope the cutover to the hosted development
database, backend, web, and iOS, plus any additional consumers the owner names.
Use backend `PublicId` for current backend/iOS page navigation; choose generic
component types with explicit semantic roles; migrate all 55 stored component
revisions; and treat `source.page` as a live page reference to migrate to the
approved UUID. These directions do not yet define the final schema fields,
resolve how shared OpenAPI `Page.id` relates to backend `PublicId`, or specify
how to preserve immutable revision history. No data or code changes are
authorized by this proposal. Any future production rollout needs a separate
transition plan.

The mappings below are candidates based on the hosted development database
snapshot and checked-in shared schemas. The shared component schema defines
generic types (`text`, `image`, `button`, `stack`, and others); it does not
define the observed legacy display roles or their property mappings. In
addition, the iOS source inventory confirms `source.legacyType` can take
precedence over `type`. Therefore an observed pair is not proof that removing
the field preserves behavior; define the replacement behavior first, migrate
the active content, and validate both known clients at cutover.

### STORY-017: candidate component mapping

| Observed `type` / `source.legacyType` | Candidate treatment | Status and required review |
| --- | --- | --- |
| `button` / `Button` (12 current) | Keep generic `button`; map display text and action to explicit current fields. | Aggregate history shows `legacyProps.text` and `target`; confirm label field and preserve action semantics. |
| `button` / `NBButton` (4 current) | Keep generic `button`; encode navigation-bar membership as a semantic role and destination as `action.pageId` using backend `PublicId`. | Aggregate history shows `text`, `target`, and `attribute`; define the role and label field, then validate iOS navigation-bar grouping. |
| `text` / `Text` (8 current) | Keep generic `text`; map display text to its current text field and size to style metadata. | Aggregate history shows `text` and `attribute` values `Small`, `Medium`, `Large`; define the normalized size vocabulary. |
| `text` / `Header` (12 current) | Keep generic `text`; use role `heading` with level 1, 2, or 3. | Current records already use `style.role=heading`; aggregate `attribute` values are H1/H2/H3. Map these to numeric levels and verify rendering. |
| `text` / `BibleVerse` (2 current) | Keep generic `text`; use role `scripture-reference` with explicit reference/version metadata and displayed text. | Current records already use `style.role=scripture-reference`; define canonical field names and preserve rendering. |
| `text` / `title` (1 current) | Keep generic `text`; use a distinct title role and move text/alignment/font size to current fields. | Aggregate history confirms `alignment`, `fontSize`, and `text`; define the title role and accepted style values before conversion. |
| `stack` / `List` (4 current) | Keep generic `stack`; use role `list` and normalize `ol`/`ul` to an explicit list-style value. | Current records already use `style.role=list`; preserve item ordering and map `legacyProps.list` plus `attribute`. |
| `stack` / `NavBar` (2 current) | Keep generic `stack`; use role `navigation` and model its child items explicitly. | Current records already use `style.role=navigation`; coordinate with the `NBButton` role and action mapping. |
| `image` / `Media` (2 current) | Keep generic `image`; map URL to `src` and preserve descriptive text in an approved alternative-text field. | Aggregate history shows `url`, `text`, and `attribute=image`; confirm whether text is alt text or a caption before mapping. |

Aggregate-only field inventory across the 55 revisions: `Button` has `text`
and `target`; `NBButton` has `text`, `target`, and `attribute`; `Text` has
`text` and `attribute`; `Header` has `text` and `attribute`; `BibleVerse` has
`text`, `reference`, and `version`; `List` has `text`, `list`, and `attribute`;
`NavBar` has `text` and `attribute`; `Media` has `text`, `url`, and
`attribute`; and `title` has `text`, `alignment`, and `fontSize`. This is a
property-name/count summary, not a complete semantic mapping.

The owner selected migration of all 55 revisions. Because those revisions are
treated as immutable history, implementation must preserve their historical
identity and auditability while producing migrated representations. The
migration mechanism (in-place transformation with an audit artifact versus
new migrated revisions linked to originals) still needs an explicit design.
Unknown-component handling remains required.

### STORY-006: candidate slug migration

1. Use backend `PublicId` for current backend/iOS navigation as directed by
   the owner. The inspected iOS client matches `action.pageId` to
   `PageSummary.publicId`, and the backend uses that UUID for published and
   management lookup. The separate shared OpenAPI `Page.id` contract has not
   been shown to use the same identifiers; decide whether to align it or
   explicitly keep it separate before changing shared contracts.
2. Resolve the 24 stored `source.legacyProps.target` values, convert confirmed
   internal destinations to the approved typed navigation action, and preserve
   other action semantics. Fix or explicitly discard unresolved/non-page
   targets; do not infer destinations from string shape alone.
3. Treat `source.page` as a live page reference as directed by the owner.
   Resolve all 55 values and migrate each to backend `PublicId`, after
   validating the agreed relationship between reusable-component provenance
   and owning page.
4. Migrate all 55 revisions as directed by the owner, with a mechanism that
   preserves revision identity/auditability. Migrate active drafts and
   published content before switching known consumers. The legacy slug route
   and client fallback can then be removed in the coordinated non-production
   cutover; no deprecation period is proposed.
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

### Owner decisions requested

- Confirm the known pre-production scope (hosted development database, backend,
  web, and iOS) and whether any other active stores or clients must be included.
- Define the precise schema field names/allowed values for the approved
  generic-type semantic roles and the legacy property mappings in the table.
- Decide whether the shared OpenAPI `Page.id` is to be aligned with backend
  `PublicId` or remain explicitly distinct.
- Choose how to migrate all immutable revisions while preserving their
  identity and auditability (for example, preserve originals as archival
  records and write a linked migrated representation, or explicitly authorize
  an audited in-place rewrite).
- Confirm the cutover validation scope and that forward-fix is the recovery
  approach for this non-production transition.

## Outstanding evidence and removal gates

The first two inventory gates are partially satisfied for the known
development environment. Remaining gates concern correct migration and
validation, not a backwards-compatibility window:

1. Confirm which environments, clients, and producers are in the pre-production
   scope. External consumers are not assumed to be supported before launch;
   include any the owner identifies.
2. Obtain authorized, read-only aggregate inventory for each environment:
   legacy fields/effective-type conflicts, navigation targets and unresolved
   slugs, locale coverage, drafts, published and historical page revisions,
   reusable drafts/revisions, and nested nodes. No raw content or private data
   belongs in this document. The hosted development database is inventoried
   above; counts for any other environment remain **unknown**, not zero.
3. Approve current section/navigation representations and migration mappings.
   Resolve ambiguous records; preserve unknown-component safety. Decide
   whether immutable history must remain readable.
4. Migrate active content and update every known in-scope producer and consumer.
5. Run focused tests and end-to-end checks for all known clients against the
   migrated backend/content; remove legacy route and fallback code as part of
   the coordinated non-production cutover.

No staged deprecation or compatibility support is proposed for this
pre-production work. A separate transition plan is required before any future
production rollout.
