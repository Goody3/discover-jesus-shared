# Discover Jesus platform: system overview

**Status:** Initial inventory with a proposed target architecture; implementation
details remain to be confirmed.

## Purpose and scope

Discover Jesus is being developed as a platform with backend and web frontend
implementations. The platform owner has also described this repository as a
shared home for contracts and documentation. The backend and web application
source code are not present in this repository.

The existing contribution guide names iOS and Android as part of the wider
ecosystem. Their implementation status and source locations are not documented
here yet.

## What this repository contains

| Area | Current contents | Source of truth |
|---|---|---|
| Backend HTTP contract | OpenAPI 3.1 document for pages, lessons, user context, and action tracking | [`api/openapi.json`](../api/openapi.json) |
| Dynamic page structure | Page envelope, sections, bindings, and nested nodes/actions | [`schemas/page.schema.json`](../schemas/page.schema.json) |
| Reusable content components | Component types, child components, and conditional/loop structures | [`schemas/component.schema.json`](../schemas/component.schema.json) |
| Content actions | Navigation, API calls, local state, sequences, and animations | [`schemas/actions.schema.json`](../schemas/actions.schema.json) |
| Platform and AI guidance | Initial documentation and contribution guidance | [`docs/README.md`](../docs/README.md), [`ai/developer-guide.md`](../ai/developer-guide.md) |

These files define shared contracts; they do not establish which released
applications implement each contract or how those applications are deployed.

## Known platform boundaries

- Backend and web implementations are available in the sibling
  `discover-jesus-backend` and `discover-jesus-web` repositories. Their
  contracts and behaviors still need coordinated review.
- The OpenAPI document describes an HTTP backend contract. Its listed
  production, staging, and local server URLs are specification values, not
  verification of deployed environments.
- The API includes operations for retrieving dynamic pages and lesson page
  lists, reading authenticated user context, and recording actions.
- The shared JSON schemas describe dynamic page, component, and action
  structures.

No deployment topology, authentication-provider details, data-store design,
client framework, or operational ownership is established by the material in
this repository.

## Proposed target architecture

The platform owner has identified two desired architectural changes:

1. **Backend-managed component content store.** Components should be managed
   independently of pages in a backend-managed content repository. Pages should
   refer to components rather than being the only place component definitions
   exist. This is a content store, not a separate source-code repository.
2. **Explicit page navigation references.** The owner-reviewed STORY-002
   proposal identifies internal destinations by the shared API page UUID,
   keeps modal actions distinct, and represents external URLs with a separate
   HTTPS-only action. It requires migration of legacy string targets,
   authoring/publication validation, and a runtime re-check. Implementation
   details remain open as listed in ADR 0002.

These are target goals, not descriptions of the current implementation or
approved technical designs. For components, the platform owner has reviewed a
page-schema reference proposal: stable UUID identity, latest published
immutable revision per requested locale, inline response expansion, and
references in `sections` while `content.blocks` remains distinct. The page
schema contains this proposed reference shape, but backend resolution is not
implemented and operational details remain open. See the proposed
[component-store decision](decisions/0001-backend-managed-component-store.md)
and [page-navigation decision](decisions/0002-explicit-page-navigation-references.md).
The navigation proposal's UUID identity does not currently match the
accessible backend's integer page IDs and slug-based retrieval; backend
alignment remains unimplemented.

## Shared-contract impact

The desired architecture affects multiple shared contracts:

- The page schema now distinguishes reusable references from inline nodes.
  The component schema and API representation need review once the supported
  catalog and cross-representation relationship are decided.
- The actions schema proposes typed `pageId`, `modalId`, and external `url`
  destinations; this is not yet implemented in backend or clients.
- The OpenAPI definition may need operations for component retrieval or
  management and changes to page request/response representations.
- API and content compatibility, reference validation, and migration from
  existing inline components or route-based navigation need explicit design.

Do not treat the page-schema proposal as implemented backend behavior. Update
other schemas and OpenAPI only after the relevant reference, lifecycle, and
compatibility requirements are agreed. Delivery planning is tracked in the
[components and navigation epic](../docs/epics/EPIC-001-reusable-components-and-page-navigation.md).

## Backend-to-web transition

The agreed delivery direction is backend first, followed by the web-interface
update. A temporary incompatibility is acceptable in the current
non-production transition; there is no production environment. Any future
production rollout requires a separately approved maximum transition window,
communications, monitoring, and recovery or forward-fix plan. See
[ADR 0003](decisions/0003-coordinated-backend-first-web-transition.md) and the
backend and web stories in EPIC-001.

## Contract alignment to clarify

The page schema describes a page with `sections` and nested inline/reference
nodes. The OpenAPI document separately describes an API page response with
`content.blocks` and metadata. The platform owner has directed that these
representations remain distinct for the current component-reference proposal;
no mapping is defined. Do not silently make one schema authoritative for the
other. Any future alignment requires a separate decision.

The proposed reference shape is defined in
[`page.schema.json`](../schemas/page.schema.json) and documented with
compatibility implications in the
[component-reference proposal](../docs/components/component-reference-contract-proposal.md).
The proposal was reviewed for STORY-001; it does not establish backend runtime
behavior or close the remaining operational questions.

## Open questions for the platform documentation

Resolve these with the owners or implementation repositories before documenting
them as facts:

- Which repositories contain the backend and web frontend, and what are their
  responsibilities?
- Are iOS and Android clients planned, active, or out of scope?
- Which client consumes each API and content contract?
- How do API page responses map to the dynamic page and component schemas?
- What are the authentication, persistence, deployment, observability, and
  release models?
- Who owns contract review, compatibility, and versioning decisions?

Update this overview as answers are confirmed. Record consequential technical
choices in [architecture decisions](decisions/README.md).
