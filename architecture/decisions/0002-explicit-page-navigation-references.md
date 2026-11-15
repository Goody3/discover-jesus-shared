# 0002. Explicit page-navigation references

- **Status:** Proposed; owner-reviewed model, implementation contract pending
- **Date:** 2026-10-31
- **Decision owners:** TBD
- **Related decisions:** [0001. Backend-managed component content store](0001-backend-managed-component-store.md), [0006. UUID page identity and linked content migration](0006-uuid-page-identity-and-linked-content-migration.md)

## Context

The platform owner wants tighter coupling between pages and the buttons used
to navigate to pages. The shared action schemas currently allow a navigation
action to specify a string `target`; they do not define how that value maps to a
page identity or how its validity is checked.

## Decision

**Proposed:** Represent internal page navigation by a typed reference to the
page's UUID identity. The platform owner reviewed and approved this model
direction on 2026-10-31: use `pageId` referencing the shared OpenAPI `Page.id`,
keep modal navigation distinct, and represent external URLs with a separate
HTTPS-only action. The action shapes are:

```json
{ "type": "navigate", "pageId": "f47ac10b-58cc-4372-a567-0e02b2c3d479" }
```

```json
{
  "type": "navigateIf",
  "pageId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "condition": "user.isAuthenticated"
}
```

```json
{ "type": "showModal", "modalId": "lesson-help" }
```

```json
{ "type": "openExternalUrl", "url": "https://example.org/" }
```

`navigateIf` requires a `condition` string; expression syntax and evaluation
semantics remain open. The old generic string `target` is not accepted by the
proposed schema. Legacy targets must be explicitly migrated before rollout; no
temporary compatibility period is proposed.

Destination validation is proposed during authoring and publication, with a
runtime re-check. Missing, unpublished, deleted, or inaccessible destinations
are ineligible and block authoring/publication; if a destination becomes
unavailable at runtime, navigation must show a clear unavailable-destination
error. Exact error codes and presentation remain open. The reference uses the
UUID `Page.id` in shared OpenAPI as authoritative. Locale resolution uses that
page identity in the requested locale, with no cross-locale fallback.
`openExternalUrl` accepts HTTPS URLs only.

The owner approved that shared OpenAPI `Page.id`, dynamic page-schema `id`, and
navigation `pageId` use the backend page `PublicId` UUID on November 14, 2026.
The backend also has an integer database `Id`; it is not the shared page
identity. This identity decision is recorded in
[ADR 0006](0006-uuid-page-identity-and-linked-content-migration.md).

This remains a proposed navigation contract: destination eligibility,
runtime error details, and condition-expression semantics are not implemented
or fully specified. The backend's UUID-addressed lookup does not by itself
implement typed action validation or migrate stored navigation payloads.

## Alternatives considered

- **Keep an unconstrained route string:** Simple and flexible, but the
  relationship to a page is not explicit and may not be verifiable against
  existing pages.
- **Use an explicit page reference:** Makes the relationship visible to
  authoring tools and validation. It requires a stable page identity and
  defined behavior for missing or changed destinations.
- **Use separate destination fields and action types:** Makes page, modal, and
  external-URL destinations distinguishable in shared content. It requires
  consumers and stored actions to migrate from the generic `target` field.
- **Allow only HTTPS external destinations:** Avoids ambiguous or unsafe
  schemes in the shared contract; other schemes are not supported by this
  proposal.

## Consequences

- The action contract and any button/page schemas need a consistent destination
  representation.
- Authoring or publishing workflows may validate that a referenced page exists
  and is eligible for navigation.
- Page identity changes, deletion, localization, and access rules need defined
  behavior.
- Existing navigation values may require migration or compatibility handling.

## Open questions

- What exact error codes and presentation apply when runtime re-validation
  finds an unavailable or inaccessible page?
- What expression language and evaluation semantics apply to the `condition`
  field on `navigateIf`?
- What coordinated validation is required before removing the legacy slug
  route and client slug fallback?
- Which existing string targets are page IDs, routes, modal IDs, or external
  URLs, and what migration tooling is required before rollout? Legacy target
  support is not part of the proposed new contract.
