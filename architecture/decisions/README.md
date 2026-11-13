# Architecture decisions

Use a decision record when a choice has meaningful, lasting effects across
platform boundaries, shared contracts, security, or developer workflows. Do not
use one for routine implementation details.

## Process

1. Copy [`template.md`](template.md) into this directory.
2. Name it `NNNN-short-title.md`, using the next available number.
3. Describe the context, decision, alternatives, and consequences. Mark
   unresolved details as open rather than guessing.
4. Link related contracts, documentation, and implementation changes.
5. Set the status to `Proposed`, `Accepted`, `Superseded`, or `Deprecated`.
   A proposal is not an approved platform decision.

Records are append-only for history: when an accepted decision changes, create
a new record that links to the superseded one rather than rewriting the old
rationale.

## Records

- [0001. Backend-managed component content store](0001-backend-managed-component-store.md) —
  proposed direction; storage and lifecycle details remain open.
- [0002. Explicit page-navigation references](0002-explicit-page-navigation-references.md) —
  proposed direction; destination identity and behavior remain open.
- [0003. Coordinated backend-first web transition](0003-coordinated-backend-first-web-transition.md) —
  accepted non-production sequence; any future production transition requires
  separate approval.
- [0004. Reporting-only coverage targets](0004-reporting-only-coverage-targets.md) —
  accepted backend, web, and iOS line-coverage goals; no coverage gates.
