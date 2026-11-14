# 0005. Separate contract verification boundaries

- **Status:** Accepted
- **Date:** 2026-11-14
- **Decision owners:** Platform owner
- **Related decisions:** [0003](0003-coordinated-backend-first-web-transition.md)

## Context

[EPIC-003](../../docs/epics/EPIC-003-contract-integrity-across-products.md)
needs automated contract-drift checks. The contracts describe different APIs:

- The shared [OpenAPI document](../../api/openapi.json) describes `/pages`,
  lesson pages, user context, and action tracking, with `content.blocks`.
- The backend repository's `api/openapi.json` describes `/api` management and
  published-content operations, including page content with `sections`.
- The shared [page](../../schemas/page.schema.json),
  [component](../../schemas/component.schema.json), and
  [action](../../schemas/actions.schema.json) schemas define their respective
  content contracts; they are not interchangeable with either HTTP document.

An automatic comparison between the two OpenAPI documents would conflate
separate contracts, rather than identify drift within one contract.

## Decision

On November 14, 2026, the platform owner approved preserving separate contracts
and verifying the backend API first:

1. Compare the backend's generated OpenAPI with its own checked-in document.
2. Generate or verify web HTTP types against an identified revision of that
   backend document, not the unrelated shared API.
3. Use explicitly mapped contract fixtures for client decoding checks.
   Document which HTTP operation or content schema each fixture exercises;
   record gaps instead of inventing a mapping.
4. Preserve shared contracts unless a separate, reviewed change is required.
5. Keep legacy retirement blocked on a consumer/content inventory and an
   owner-approved compatibility and deprecation plan.

The HTTP metadata check does not prove all response bodies or dynamic JSON
content conform to their contracts; those require separate tests.

## Alternatives considered

- **Unify shared and backend OpenAPI first:** Not selected; requires separate
  product and representation decisions outside the initial verification work.
- **Compare only endpoint paths:** Insufficient; misses schema and operation
  metadata drift.

## Consequences

Backend verification can proceed without changing API behavior. Web and iOS
follow-up stories must identify their contract inputs and update process.
Starting this epic does not authorize removal of legacy routes, navigation,
or component-type fallbacks.

## Open questions

- Exact cross-repository contract distribution and fixture refresh mechanisms.
- Consumer inventory, replacement behavior, and legacy removal criteria.
