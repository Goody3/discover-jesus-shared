# Component store catalog proposal

- **Status:** Preliminary catalog checked against the owner-accepted representative snapshot; final type allowlist still requires separate approval
- **Evidence reviewed:** Pages visible in the local Content Studio at
  `http://localhost:5173/`
- **Reviewed:** 2026-10-31
- **Related work:** [EPIC-001](../epics/EPIC-001-reusable-components-and-page-navigation.md),
  [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md),
  [ADR 0002](../../architecture/decisions/0002-explicit-page-navigation-references.md)

## Summary

The platform owner accepted the previously observed `TestPage01`–`TestPage06`
and `Welcome` Content Studio snapshot as representative enough for the
discovery and comparison required by STORY-001. This bounded acceptance does
not make the sample exhaustive or approve the proposed catalog as a production
allowlist.

The local Content Studio displayed seven published pages containing 24
component records. Six pages are named `TestPage01` through `TestPage06`; the
seventh is `Welcome`. Most test-page names and values look like demonstration
content. Owner acceptance allows this snapshot to serve as representative
story evidence, but the names and scope still limit what can be inferred about
the complete production catalog.

The observed UI labels include `Button`, `Header`, `Text`, `List`,
`BibleVerse`, `NavBar`, `NBButton`, `Media`, and `title`. A proposed initial
catalog should normalize these labels into a small set of typed, reusable
rendering components, with type-specific validated properties. It should not
turn every content instance or arbitrary label into a new component type.

## Evidence from the visible pages

| Page | Count | Observed component labels and details |
|---|---:|---|
| `TestPage01` | 6 | Six `Button` components named `ButtonTB01`–`ButtonTB06`; no action definitions or data bindings shown. |
| `TestPage02` | 6 | Three `Header` and three `Text` components. A header's properties include `text: "Header 1"` and `attribute: "H1"`. |
| `TestPage03` | 4 | Two `Header` components and two `List` components. The list records are named `UL01` and `OL01`; the inspected unordered list has `attribute: "ul"` and a three-item `list` property. |
| `TestPage04` | 1 | `BibleVerse` with text, `version: "esv"`, and `reference: "John 3:16"`. |
| `TestPage05` | 3 | One `NavBar` and two `NBButton` components. An inspected button has `text: "Home"` and `target: "testpage01"`. |
| `TestPage06` | 3 | One `Header`, one `Media`, and one `Text`. The inspected media properties identify an image and a URL. |
| `Welcome` | 1 | One component whose key and type are both `title`. Its detailed properties were not inspected. |

Across these pages, the visible component type counts are: `Header` 6,
`Button` 6, `Text` 4, `List` 2, `NBButton` 2, and one each of `BibleVerse`,
`NavBar`, `Media`, and `title` (24 total).

The UI showed zero data bindings and zero action definitions on the pages
inspected. The `NBButton` target was stored in its properties as a string,
which supports making explicit page navigation a design requirement, but does
not demonstrate a validated page relationship in the current system.

## Proposed initial catalog

The following are candidate **rendering types** for the backend-managed
component store. The catalog describes type families; individual headings,
verses, and buttons are component instances or content, not new types.

| Proposed type | Evidence | Proposed responsibility and properties |
|---|---|---|
| `heading` | Repeated `Header` records; inspected `H1` attribute | Semantic heading text and level. Use a constrained heading level instead of arbitrary tag names. |
| `text` | Repeated `Text` records | Body or supporting text, with explicitly defined formatting options only as required. |
| `button` | Six `Button` records and two `NBButton` records | Interactive control with label and a typed action. Page navigation should reference a page identity, not an unchecked route string. |
| `list` | `UL01` and `OL01`; one inspected three-item list | Ordered or unordered list with structured items. Establish whether items are plain text or may contain richer content. |
| `scripture` | One `BibleVerse` with text, translation/version, and reference | Scripture passage or verse with text, translation identifier, and reference. Decide whether text is authored, fetched, or both. |
| `image` | `Media` record marked as image with a URL | Image asset/source plus accessible description and presentation options. Decide asset ownership and URL rules. |
| `video` | Existing API contract names `video` as a content-block type; no video instance was found in the visible pages | Candidate for a later or initial release if video is a required use case. Define provider/source, poster, captions, and playback rules before inclusion. |
| `navigationBar` | One `NavBar` record with two `NBButton` records on the same page | Navigation grouping/layout. Decide whether it is a composite component containing button references or a page-level arrangement of independent button components. The UI evidence does not establish parent-child ownership. |

### Type families versus shared component instances

The component store should distinguish a component **type** (for example,
`scripture`) from a reusable component **instance/definition** (for example, a
specific reusable scripture panel) and from a page's **reference** to that
instance. The current UI's component key, type, sort order, and free-form JSON
properties do not by themselves define these separate identities or lifecycles.
Resolve that data model under [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md)
before finalizing schemas or endpoints.

## Existing contract cross-check

The current [component schema](../../schemas/component.schema.json) includes
`text`, `image`, `button`, `stack`, `row`, `column`, `conditional`, `loop`,
`spacer`, `divider`, and `modal`. The OpenAPI content-block schema additionally
lists `heading`, `paragraph`, `image`, `video`, `scripture`, `quote`, `cta`, and
`divider`. The local UI uses additional labels such as `Header`, `BibleVerse`,
`List`, `NavBar`, and `NBButton`.

These are not yet one consistent catalog. The proposed mapping above is a
starting point, not an instruction to copy every old label or enum value into
the new store. In particular:

- `Header` and `heading` appear to overlap, as do `Text` and `paragraph`.
- `BibleVerse` and `scripture` appear related, but the schema and UI properties
  have not been reconciled.
- `NBButton` may be a button variant rather than a separate type.
- `NavBar` composition is unclear.
- Layout and logic types (`stack`, `row`, `column`, `conditional`, `loop`) are
  schema-defined but not evidenced by these pages. Decide whether the store
  needs structural/composite types in its first release.
- `quote`, `cta`, `spacer`, `divider`, and `modal` are also contract candidates
  or existing schema types, but were not observed in the inspected page records.

### Evidence classification for STORY-001

The owner-accepted snapshot supports discovery-level comparison for the
following labels: `Header`, `Text`, `Button`, `List`, `BibleVerse`, `NavBar`,
`NBButton`, `Media`, and `title`. The proposal maps these tentatively to
`heading`, `text`, `button`, `list`, `scripture`, `navigationBar`, and `image`.
That mapping remains a proposal because some component properties and
composition details were not inspected.

The following types or families remain **unverified by the accepted snapshot**
and must not be treated as part of a finalized allowlist based on this story:

- API candidates with no observed instance: `video`, `quote`, `cta`, `quiz`,
  and `reflection`;
- schema-defined types with no observed instance: `stack`, `row`, `column`,
  `conditional`, `loop`, `spacer`, `divider`, and `modal`;
- detailed shape/requirements for `List`, `NavBar`, `NBButton`, `Media`, and
  the `Welcome` `title` record.

The snapshot therefore satisfies STORY-001's representative-content
cross-check at discovery level only. Final production catalog approval and
type-specific validation remain separate work.

## Recommendation

1. Use the proposed catalog as an initial discovery list, not as the final
   allowlist.
2. The platform owner accepted the visible snapshot as representative enough
   for STORY-001's discovery comparison. Review additional authored pages
   before locking a production type allowlist.
3. Keep the first catalog small and type-specific. Prefer a `button` with
   explicit action variants over separate `Button` and `NBButton` types unless
   they have genuinely different rendering or behavior.
4. Define validation per type instead of accepting arbitrary JSON properties
   as the long-term contract.
5. Resolve component type versus reusable component identity, versioning,
   page references, ownership, and navigation validation under the related
   architecture decisions.
6. Update the shared schemas and OpenAPI only after the catalog and data model
   are approved.

## Limitations and follow-up

This review used the local Content Studio's visible page list and component
editor and later inspected the accessible backend source for its current data
model and API behavior. The platform owner accepted the prior seven-page,
24-component snapshot as representative enough for STORY-001's discovery
comparison. This review did not directly query backend storage, inspect every
component property, or establish that the sample exhaustively represents
production. Component edit dialogs were opened for inspection and canceled
without saving.

The accessible web repository contains no authored-content fixtures, and the
backend repository contains no sample pages or seed records. The local
Content Studio at `localhost:5173` was not running, and the configured API at
`localhost:5000` did not provide an accessible response during the current
contract review. The owner-accepted snapshot cross-check for STORY-001 is
complete, but deeper production-catalog validation remains unavailable; the
catalog remains discovery evidence, not an approved allowlist.

Before approving a final catalog:

- Review additional authored pages before locking types; the current snapshot
  was accepted as representative enough for STORY-001 but is not exhaustive.
- Review the detailed properties for the remaining component examples,
  especially ordered lists, navigation buttons, and the `Welcome` title.
- Inspect representative authored content for quotes, calls to action,
  images/video, and structured lesson content.
- Agree on accessibility, localization, responsive layout, and content
  authoring requirements.
- Reconcile the approved list with `component.schema.json`,
  `page.schema.json`, and `api/openapi.json`.
