# Reusable component reference contract

- **Status:** Draft contract reviewed by the platform owner; STORY-001's
  discovery cross-check is complete. A production type allowlist remains
  unapproved.
- **Related story:** [STORY-001](../stories/STORY-001-define-component-reference-contract.md)
- **Related decisions:** [ADR 0001](../../architecture/decisions/0001-backend-managed-component-store.md)
- **Reviewed:** 2026-10-31

## Purpose and current evidence

This draft identifies the contract choices needed to let pages reuse
backend-managed components. It is technology-independent; the page JSON Schema
defines the proposed authored reference shape and response provenance. This
does not implement backend resolution or change the shared OpenAPI.

## Direction recorded

The following directions were supplied for this work on 2026-10-31 and reviewed
by the platform owner. They guide the page-schema proposal; they do not
approve a backend API shape or close the remaining operational details below.

- Give each reusable definition a stable component UUID. UUIDs survive
  component rename and ownership transfer.
- Resolve references to the latest published revision for the requested
  locale, ordered by a monotonically increasing published revision number for
  that locale. Do not fall back across locales.
- Edits create drafts. Explicitly publishing a validated draft creates a new,
  immutable revision and makes it the latest published revision for that
  locale.
- Prevent both deletion and withdrawal while active page references exist.
- Block page publication if a reference cannot resolve; fail the page response
  explicitly if a reference is unresolved at runtime.
- Return 404 for a missing or withdrawn reference and 503 when the resolver is
  temporarily unavailable.
- Cache resolved content by component UUID, locale, and immutable revision;
  update the latest-revision pointer on publication.
- Expand resolved references inline in the successful `sections` page
  response.
- Expand each reference as one renderable node and preserve its source UUID as
  non-rendering `sourceComponentId` response metadata.
- Permit references in nested page nodes, including conditional branches and
  loop templates. A page reference may provide `children` to replace the
  referenced node's child content; omitting `children` keeps the published
  child content.
- Disallow reusable-component references nested inside reusable definitions
  in the initial contract.
- Support reusable references and inline components.
- Add page references to the page-schema `sections` representation only.
- Keep page-schema `sections` and API `content.blocks` distinct for now; this
  draft does not define a mapping or choose one as authoritative.
- Existing page-owned components remain inline; migration to reusable
  definitions is opt-in. Authorization roles, draft validation rules, cache
  consistency guarantees, stable error payloads, and handling of inaccessible
  references remain open.
- The owner-accepted snapshot supports STORY-001's discovery cross-check;
  broader authored content remains unavailable for final catalog validation.

The shared contracts currently describe page content in two different ways:

- [`page.schema.json`](../../schemas/page.schema.json) requires `sections`, an
  ordered array of inline nodes or reusable-component references. Inline nodes
  have a `type` and may have `value`, `src`, `style`, `action`, or
  conditional/loop fields.
- The shared [OpenAPI page schemas](../../api/openapi.json) use
  `Page.content.blocks`; their content-block types and shape are not the same
  as the page-schema nodes.
- [`component.schema.json`](../../schemas/component.schema.json) describes a
  component by type and rendering properties. It has no reusable definition
  identity or reference/version shape.

The accessible backend source is a third, distinct shape. Its `Page` owns
`Component` records; each component has a numeric database ID, a key unique
within its page, a type, arbitrary JSON properties, and a sort order. The
backend returns these nested under `PageResponse.Components`. Updating a
component replaces its current fields, and deleting a page cascades to its
components. These are observations of the current backend source, not a
recommendation for the shared reusable-component contract.

The accessible web repository is an API-backed Content Studio and contains no
checked-in representative content fixtures. Its configured API and the local
Content Studio were not reachable during this work. The backend repository
also has no checked-in authored page samples or seed content. The platform
owner accepted the previously observed seven-page, 24-component local
Content Studio snapshot as representative enough for STORY-001's discovery
comparison. This bounded acceptance does not make the snapshot exhaustive or
approve a production type allowlist; see the catalog's
[evidence classification](component-catalog-proposal.md#evidence-classification-for-story-001).

## Contract concepts to distinguish

The eventual contract should distinguish:

1. **Component type:** the rendering/validation family, such as `heading` or
   `image`.
2. **Reusable component definition:** independently managed content with a
   stable identity and published revisions.
3. **Page placement/reference:** an ordered use of a reusable definition in a
   page, with any placement-specific configuration clearly separated from the
   reusable definition.
4. **Inline component:** page-owned content that is not stored as a reusable
   definition.

The current schemas and backend do not consistently make these distinctions.
In particular, a page-scoped backend component key is not a globally reusable
identity, and a component type is not an instance identity.

## Page-reference representation

The page schema defines a `componentReference` node with a UUID `componentId`.
It may appear in `sections` or nested page nodes. Its optional `children` array
replaces the resolved node's child content; when omitted, the published child
content is retained. The UUID survives rename and ownership transfer.

```json
{
  "sections": [
    {
      "type": "componentReference",
      "componentId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "children": [
        {
          "type": "text",
          "value": "Page-specific child override"
        }
      ]
    },
    {
      "type": "text",
      "value": "A page-specific inline component"
    }
  ]
}
```

`componentReference` and `componentId` are defined by
[`page.schema.json`](../../schemas/page.schema.json). `sourceComponentId` is
response-only metadata on expanded nodes. The schema deliberately leaves the
rendering component catalog open; validation of type-specific content is
separate. `content.blocks` remains unchanged; no mapping or equivalence
between the two representations is assumed.

Each reference identifies the reusable definition by UUID and delegates
version selection to the resolver, which chooses the latest published
immutable revision for the requested locale. A successful response for the
`sections` representation expands that definition inline at the reference's
position as one renderable node and sets `sourceComponentId` to the source
UUID. If the reference provides `children`, they replace the resolved node's
child content; otherwise the definition's child content remains. This does not
change or define the separate OpenAPI `content.blocks` representation. A
reference must not overload the component's rendering type or page-scoped key
as its reusable identity.

Illustrative resolved-response pseudocode:

```json
{
  "sections": [
    {
      "type": "text",
      "value": "Shared component content",
      "sourceComponentId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "children": [
        {
          "type": "text",
          "value": "Page-specific child override"
        }
      ]
    }
  ]
}
```

`sourceComponentId` is response-only provenance and is marked `readOnly` in
the page schema. The OpenAPI `content.blocks` representation is not changed
by this proposal.

## Lifecycle and unresolved-reference behavior

The directions below are recorded from platform-owner input. Remaining
operational details are called out explicitly:

| Concern | Agreed direction | Remaining detail |
|---|---|
| Identity | UUID is stable across component rename and ownership transfer. | None for this story's reference contract. |
| Versioning | References resolve to the highest monotonically numbered published immutable revision for the requested locale; there is no version pin or cross-locale fallback. | Define withdrawal and restoration behavior for revisions. |
| Update | Edits create drafts; explicit publication validates and creates a new immutable revision that becomes latest for its locale. | Define content validation rules and publisher authorization. |
| Deletion and withdrawal | Prevent both while page references exist. | Define the removal workflow, archived history, restoration/audit behavior, and whether references in unpublished page drafts count. |
| Page publication | Block publication if any reference cannot resolve. | Define diagnostics for each unresolved-reference cause. |
| Runtime resolution failure | Return 404 for a missing or withdrawn reference; return 503 when the resolver is temporarily unavailable. | Define stable error codes/payloads and diagnostics for inaccessible definitions. |
| Nested references | References can appear in nested page nodes. Reusable definitions cannot contain reusable references initially. | None for the initial nesting rule. |
| Reference expansion | Expand each resolved reference as one renderable node with read-only `sourceComponentId`. Optional page-authored `children` replace the resolved node's child content; omission preserves it. | None for the selected expansion rule; the schema defines the authored shape. |
| Inline content | Existing page-owned components remain inline; migration to reusable definitions is opt-in. | Define type-specific restrictions only if required by the catalog/content review. |
| Cache behavior | Cache resolved content by component UUID, locale, and immutable revision; publication updates the latest-revision pointer. | Define consistency guarantees for page responses and cached pointers. |

These policies define the current page-schema proposal. Runtime API behavior,
authorization, and operational error payload details remain implementation
requirements, not implemented behavior.

## Compatibility and affected surfaces

| Surface | Current contract | Expected impact if references are adopted |
|---|---|---|
| [`page.schema.json`](../../schemas/page.schema.json) | `sections` contains inline nodes. | Defines UUID-validated `componentReference` nodes with optional child-content replacement, usable in nested page nodes; expanded nodes may carry read-only `sourceComponentId` metadata. |
| [`component.schema.json`](../../schemas/component.schema.json) | A component is a typed renderable object, with nested children and logic blocks. | Separate a reusable definition from a page reference and define whether child components can be referenced. |
| [Shared `api/openapi.json`](../../api/openapi.json) | `Page` and `PageVersion` contain `content.blocks` with a separate content-block shape. | No change is proposed here. Keep this representation distinct from `sections`; any future alignment or reference support needs a separate decision. Inline expansion applies to the section-bearing page response only. |
| Backend [`ContentContracts.cs`](../../../discover-jesus-backend/Contracts/ContentContracts.cs) and [`ContentEndpoints.cs`](../../../discover-jesus-backend/Endpoints/ContentEndpoints.cs) | Components are created under a page and returned as nested component records. | Add or revise request/response contracts and retrieval behavior when the owner-approved model is implemented. |
| Backend [`ContentModels.cs`](../../../discover-jesus-backend/Models/ContentModels.cs) and [`DiscoverJesusDbContext.cs`](../../../discover-jesus-backend/Data/DiscoverJesusDbContext.cs) | A component has a required `PageId`; its key uniqueness is page-scoped. | Persistence changes may be needed for independent component ownership and references; schema shape alone does not prescribe storage. |
| Client content consumers | Not inspected in this work. | Identify and coordinate consumers before changing payload behavior. |

Existing clients may expect page content to be fully inline or expect nested
page-owned components. Expanding references inline and retaining legacy
page-owned components are intended to preserve the existing rendered content
shape. Stable error payloads for inaccessible references and cache consistency
guarantees still need definition. The accepted backend-first transition in
[ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md)
allows a planned incompatibility window, but does not choose this payload
behavior or eliminate the need for a migration and rollout plan.

Compatibility questions to answer before changing a schema or payload:

- Which clients currently consume each of `sections`, `content.blocks`, and
  the backend's nested `components` response?
- What stable error codes/payload and diagnostics should page retrieval return
  for inaccessible definitions? Missing, withdrawn, and transient resolver
  failures have proposed 404/503 behavior.
- How will existing page-owned components map to reusable definitions, and
  what happens to pages whose components are not migrated?
- Which schema/API versioning or coordinated transition behavior is required
  for the old and new representations?

The page schema now defines the proposed reference node. The shared OpenAPI
`content.blocks` shape remains unchanged unless a separate alignment decision
is made. The backend implementation must add independent definitions,
publication/revision handling, resolution, and reference validation; none of
that runtime behavior is implemented or claimed by this documentation change.

## Catalog cross-check and boundaries

The local Content Studio observations in the preliminary catalog are not
sufficient to finalize an initial supported type set. The platform owner
accepted the seven-page, 24-component snapshot as representative enough for
STORY-001's discovery-level cross-check. It identified tentative mappings and
unverified types, but does not support a final production allowlist. The
available web source contains no authored-content fixtures, the backend source
contains no sample pages or seed records, and the local services were not
reachable for deeper inspection. Separate production catalog approval should:

- identify representative authored pages and distinguish production/real
  content from test or demonstration content;
- inspect full properties and nesting for each observed type, especially
  `List`, `NavBar`, `NBButton`, `Media`, and `title`;
- check whether schema-defined but unobserved types (including layout, logic,
  video, quote, and modal types) are actually authored or required;
- map each observed record to a rendering type and validate which properties
  are required, optional, or unsupported.

The representative-content cross-check is complete for STORY-001 at the
accepted discovery level. The catalog remains preliminary and must not be used
as a finalized supported-type allowlist.

## Remaining decisions and validation

- Define publisher authorization, component draft validation, and diagnostics
  for blocked page publication.
- Define deletion/withdrawal and revision restoration after references are
  removed, including how draft-page references count.
- Define stable error codes/payloads for inaccessible definitions.
- Define cache consistency guarantees when the latest-revision pointer
  changes.
- Validate the proposed catalog against additional representative authored
  pages. None were accessible in the repositories or local services during
  this work.
