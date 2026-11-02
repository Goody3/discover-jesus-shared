# STORY-012. Add iOS decoding, rendering, and navigation tests

- **Status:** Proposed
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** None identified

## User story

As an **iOS maintainer**, I want automated tests for component decoding,
rendering, navigation, and error handling, so that contract changes and
refactoring do not silently break content display.

## Context

The iOS retrospective recommends tests for decoding every component shape and
the legacy mapping path, rendering of each supported component and the
unknown-component fallback, navigation resolution via `action.pageId` and the
legacy slug fallback, and error paths. The supported component types it names
are Button, Header, Text, List, NavBar/NBButton, BibleVerse, and Image. The
logic currently sits largely in `ContentView.swift`, so tests may need small
seams (for example injectable networking) to run without a backend.
Implementation belongs in the `discover-jesus-ios` repository.

## Acceptance criteria

1. A unit test target exists and runs from the command line.
2. Decoding tests cover each supported component type, the `type` plus
   `legacyType` fallback, and malformed or empty payloads.
3. Renderer tests cover each supported component and confirm that an unknown
   component type shows the fallback instead of crashing.
4. Navigation tests cover resolution through `action.pageId` and the legacy
   slug fallback, including a target that cannot be found.
5. Error-path tests cover 4xx and 5xx responses, malformed JSON, and empty
   payloads, and check that the displayed error message and failed URL are
   correct.
6. Tests use fixtures and need no running backend.
7. Fixtures are based on shapes in the shared schemas or examples in this
   repository, so that they track the contract.

## Scope

### Included

- Test target, fixtures, and tests for the items above.

### Excluded

- UI automation tests and snapshot tests.
- Refactoring beyond small seams needed for testing; the file split belongs to
  [EPIC-006](../epics/EPIC-006-maintainable-client-and-service-structure.md).
- Removing the legacy fallbacks ([EPIC-003](../epics/EPIC-003-contract-integrity-across-products.md)).

## Dependencies and constraints

- Tests must keep passing when the legacy fallbacks are later removed, or be
  updated in the same change.

## Completion checks

- The tests run from the command line and in Xcode.
- Removing the unknown-component fallback causes a test to fail.
