# 0001. Backend-managed component content store

- **Status:** Proposed
- **Date:** 2026-10-31
- **Decision owners:** TBD
- **Related decisions:** [0002. Explicit page-navigation references](0002-explicit-page-navigation-references.md)

## Context

The current shared schemas describe components as content nested in pages or
other components. The platform owner wants components decoupled from pages and
managed in a component repository. The repository is clarified to mean a
backend-managed content store, not a source-code repository.

The current page schema uses `sections` and nested `node` values, while the
OpenAPI page response uses `content.blocks`. The platform owner has directed
that these remain distinct for this proposal; no mapping is defined.

## Decision

**Proposed:** Manage reusable component content independently in a
backend-managed content store, and allow pages to refer to stored components.
Platform-owner direction is to use stable UUID identities that survive rename
and ownership transfer, resolve references to the highest monotonically
numbered published immutable revision for the requested locale without
fallback, and support both reusable references and inline page components.
Edits create drafts; explicit publication validates a draft and creates a new
revision. Deletion and withdrawal are prevented while active page references
exist.

References are represented in page-schema `sections` as
`type: "componentReference"` with a UUID `componentId`; optional `children`
replace the referenced node's children. References may appear in nested page
nodes, but reusable definitions cannot contain reusable references initially.
Successful page responses expand each reference to one renderable node with
read-only `sourceComponentId` provenance. Page publication is blocked if a
reference cannot resolve. Runtime retrieval returns 404 for missing or
withdrawn references and 503 when resolution is temporarily unavailable.
Resolved content is cached by component UUID, locale, and immutable revision,
with the latest-revision pointer updated on publication. Existing page-owned
components remain inline; migration to reusable definitions is opt-in.

References are proposed for the page schema's `sections` representation; the
OpenAPI `content.blocks` representation remains distinct for now.

These refinements are an owner-reviewed model direction, not an accepted
technical contract. The decision does not select a database or API operations.
Publisher authorization, draft validation rules, withdrawal/restoration after
references are removed, cache consistency guarantees, diagnostics for blocked
publication, and stable error payloads for inaccessible references remain open.

## Alternatives considered

- **Keep component definitions embedded in pages:** Retains the current
  authoring model but does not meet the goal of independent component
  management and reuse.
- **Store components in a separate source-code repository:** Rejected by the
  platform owner's clarification; the intended repository is managed by the
  backend as content.

## Consequences

- The proposed page schema defines references alongside inline components;
  component and API contracts still need alignment for implementation.
- The backend will need clear ownership of component creation, retrieval,
  updates, validation, and deletion behavior.
- Reference consistency, access control, and coordination with the distinct
  OpenAPI `content.blocks` representation need to be designed.
- API operations may need to be added or changed; no endpoint shape has been
  selected.

## Open questions

- What access controls, publisher permissions, and review requirements apply?
- What validation rules must component drafts pass before publication?
- Do references in unpublished page drafts prevent component deletion or
  withdrawal?
- What cache consistency guarantees apply when the latest-revision pointer
  changes?
- What stable error payloads and diagnostics apply to inaccessible references
  and blocked page publication?
- What archive/restoration behavior is needed after references are removed?
- Should API `content.blocks` remain distinct, or should its future alignment
  with `sections` be handled by a separate decision?
- Which component types and type-specific properties belong in the production
  allowlist? STORY-001 cross-checked the owner-accepted Content Studio snapshot
  as discovery evidence; it is not exhaustive and does not approve the
  allowlist.
