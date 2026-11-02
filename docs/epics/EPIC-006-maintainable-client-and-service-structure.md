# EPIC-006. Maintainable client and service structure

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md). Supports the goal of adding churches and
  presentations without slowing delivery.
- **Dependencies:** [EPIC-002](EPIC-002-automated-quality-gates-and-test-foundations.md)
  tests must be in place first; [EPIC-005](EPIC-005-reliable-and-consistent-api-behavior.md)
  introduces the web data layer and iOS API client that this epic builds on.

## Need and opportunity

Each product has a few oversized files holding most of its logic. The web app's
`App.tsx` is about 1,750 lines, the backend's `ContentEndpoints.cs` and
`ReusableContentService.cs` are about 680 and 1,350 lines, and the iOS app's
`ContentView.swift` was flagged for the same reason. This slows changes and
makes testing harder.

## Outcome

Each product is organized into focused modules with clear responsibilities,
without changing user-visible behavior.

## Success criteria

- Web components, hooks, and types are split into feature modules, with routing
  that gives pages and editors their own URLs.
- Backend endpoint handlers delegate to services, and the large service is split
  by responsibility.
- iOS models, API client, renderer, and component views are in separate files,
  with a normalized component model.
- The tests added in EPIC-002 pass unchanged before and after each split.

## Scope

### Included

- Refactoring of the identified files in all three products.
- Web routing.

### Excluded

- New user-facing features.
- iOS design tokens and visual polish.
- Web styling migration.

## Assumptions, dependencies, and risks

- **Assumptions:** Behavior-preserving refactoring is verified by the existing
  and new tests.
- **Dependencies:** EPIC-002, EPIC-005.
- **Risks:** Refactoring without adequate tests could introduce regressions;
  do not start a split until the relevant area is covered.

## Stories

| ID | Story | Status |
|---|---|---|
| — | No stories defined yet | — |
