# STORY-024. Validate content before storage

- **Status:** In progress; backend and Content Studio implementation is locally
  validated on `feature/story-024-content-validation`; SQL Server integration
  and remote CI remain.
- **Epic:** [EPIC-004](../epics/EPIC-004-secure-content-authoring-and-access.md)
- **Priority:** Before passkey cutover
- **Dependencies:** EPIC-003 contract baselines

## User story

As a content author, I want actionable errors when content is invalid so
that invalid payloads are not stored or published.

## Acceptance criteria

1. Inventory existing validation and add missing server-side checks for
   field lengths, allowed component types, and binding and action shapes
   across management writes, reusable components, and page drafts.
2. Distinguish rules for saving an incomplete draft from rules for
   publishing. Reject invalid writes with documented validation errors
   and no partial persistence or publication.
3. Preserve supported legacy and migrated content behavior where intended.
   Use the applicable backend contract and do not assume the separate shared
   content schema is an interchangeable request contract.

## Completion checks

- Cover representative valid, malformed, and boundary inputs and verify
  rejected writes leave stored content unchanged.
- Verify affected API contract snapshots and Content Studio error handling.

## Agreed validation policy

- Modern page and reusable-component nodes use the supported component types
  from the shared dynamic-content contract; page-only `componentReference`
  nodes remain a separate reference shape.
- Legacy management component writes accept those modern type names plus only
  the legacy types explicitly mapped by `LegacyReusableComponentPayloadMigrator`.
  Their `Props`, `Binding.Config`, and `Action.Config` values must be JSON
  objects; their internal properties and binding/action type semantics remain
  opaque.
- Draft saves reject malformed types and shapes but may omit role-specific
  fields and may refer to components or pages that are not publishable yet.
  Publication requires the role-specific fields and validates all references
  and navigation destinations.
- Keep the backend's OpenAPI snapshot aligned with request field bounds,
  component type allowlist, and JSON object shapes. The shared OpenAPI file is
  not the contract for these backend management routes.

## Implementation progress

Backend validation now enforces the agreed management and modern-content rules.
The checked-in backend OpenAPI snapshot documents request constraints, and the
Content Studio surfaces field-level Problem Details validation errors. Local
checks passed: 56 backend tests that do not require SQL Server, 40 web tests,
web lint/build/API contract verification, and backend OpenAPI snapshot parity.
The backend and web changes remain unmerged; SQL Server integration tests and
remote CI are still required before completion.
