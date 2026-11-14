# EPIC-003 legacy retirement inventory

- **Date:** 2026-11-14
- **Status:** Source inventory verified; additive UUID management lookup and
  web migration approved on November 14, 2026 and implemented locally.
  Retirement policy, stored-content and external-consumer inventories remain
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
public UUID selection, including after slug rename. Both local source changes
are pending hosted verification and merge. The inventory table above describes
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

## Outstanding evidence and removal gates

These are proposed gates, not an approved notice/monitoring policy:

1. Identify operational owners and all environments/clients/producers. Confirm
   external consumers explicitly rather than interpreting no source match as
   no usage.
2. Obtain authorized, read-only aggregate inventory for each environment:
   legacy fields/effective-type conflicts, navigation targets and unresolved
   slugs, locale coverage, drafts, published and historical page revisions,
   reusable drafts/revisions, and nested nodes. No raw content or private data
   belongs in this document. Current counts are **unknown**, not zero.
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
