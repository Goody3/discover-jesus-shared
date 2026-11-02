# EPIC-005. Reliable and consistent API behavior

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md).
- **Dependencies:** [EPIC-003](EPIC-003-contract-integrity-across-products.md)
  for contract tracking; [EPIC-004](EPIC-004-secure-content-authoring-and-access.md)
  should be settled first because it changes request handling.

## Need and opportunity

The retrospectives describe gaps in how the API behaves under real use. The
backend has no pagination on list endpoints, no concurrency control for
competing edits, and no logging. The web app fetches without cancellation or
conflict handling. The iOS app wants typed errors, Problem Details parsing,
timeouts, and retries.

## Outcome

Clients can rely on predictable, bounded API responses and clear error
behavior, and operators can see what the API is doing.

## Success criteria

- List endpoints support pagination and filtering with a maximum page size.
- Concurrent edits are detected and reported with 409 or 412, and the web app
  offers a clear way to resolve the conflict.
- The API emits structured logs with correlation IDs and has separate liveness
  and readiness health checks.
- The iOS app uses a dedicated API client with typed errors, Problem Details
  parsing, timeouts, and retry behavior.
- The web app cancels stale requests and shows consistent loading and error
  states.

## Scope

### Included

- Backend pagination, concurrency control, logging, and health checks.
- Client handling of those behaviors in web and iOS.

### Excluded

- Soft delete and audit trail.
- OpenTelemetry tracing and metrics.
- API versioning.

## Assumptions, dependencies, and risks

- **Assumptions:** Content volumes are small today, so pagination is
  preventive; confirm the default page size with the owner.
- **Dependencies:** EPIC-003 and EPIC-004.
- **Risks:** Pagination changes list response shapes, which is a contract
  change that affects all three clients.

## Stories

| ID | Story | Status |
|---|---|---|
| — | No stories defined yet | — |
