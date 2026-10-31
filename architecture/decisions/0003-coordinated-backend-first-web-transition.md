# 0003. Coordinated backend-first web transition

- **Status:** Accepted
- **Date:** 2026-10-31
- **Decision owners:** Platform owner
- **Related decisions:** [0001. Backend-managed component content store](0001-backend-managed-component-store.md), [0002. Explicit page-navigation references](0002-explicit-page-navigation-references.md)

## Context

The component-store and page-navigation changes require backend and web
interface updates. The current web interface may not work with the updated
backend contract until the web interface is updated. The platform owner has
confirmed that, because this is a new system, a short period of incompatibility
is acceptable. The platform owner confirms there is currently no production
environment; the current migration and incompatibility apply to
non-production environments.

## Decision

Update the backend first, then update the web interface to consume the new
backend behavior and contracts. A temporary incompatibility between the
backend and current web interface is acceptable during the coordinated
non-production transition.

This permission is limited to the planned transition. It does not imply
acceptance of an indefinite incompatibility or unrelated outage. Schedule the
web update before applying the backend change in a shared environment and
verify end-to-end operation there. Any future production rollout requires a
separate approved transition plan.

## Alternatives considered

- **Require backward compatibility throughout the transition:** Reduces
  disruption but is not required for this new system's planned contract change.
- **Update backend and web simultaneously:** Could avoid a visible
  incompatibility window, but requires coordinated deployment and is not the
  requested implementation sequence.

## Consequences

- Backend and web work must be planned as dependent work, with the web update
  following the backend contract change.
- The team may intentionally accept a bounded non-production period when the
  current web interface cannot operate against the updated backend.
- The Platform owner is accountable for coordinating and approving the
  current transition. Communications are internal-stakeholder-only; no
  end-user downtime notice applies because no production environment exists.
  Recovery is forward-fix only; do not assume backend rollback is safe after
  schema or data changes.
- Monitor backend health and availability, web/API errors and latency, and
  synthetic page-load/navigation checks. Verify page loading, reusable
  component rendering, internal and HTTPS external navigation, authoring/editor
  saves, and modal behavior where a runtime consumer supports it.
- Production downtime monitoring and a maximum production incompatibility
  window are not applicable to the current migration. Before any future
  production rollout, the Platform owner must approve a maximum duration and a
  release-specific plan.
- Runtime consumers beyond the current admin UI remain unidentified. Identify
  them before any future production rollout and ensure transition monitoring
  and recovery checks exercise the actual end-user runtime.
- The web update must be validated against the new backend contract before the
  transition is considered complete.

## Open questions

- Before the first production rollout, what maximum incompatibility window and
  release-specific transition plan will the Platform owner approve?
- Which runtime clients consume page content and execute actions, and who will
  own end-to-end production recovery verification?
