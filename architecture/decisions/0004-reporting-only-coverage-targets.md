# 0004. Reporting-only coverage targets

- **Status:** Accepted
- **Date:** 2026-11-13
- **Decision owners:** Platform owner
- **Related decisions:** None

## Context

[STORY-013](../../docs/stories/STORY-013-agree-and-report-coverage-targets.md)
requires per-product CI coverage baselines and owner-approved targets. The
November 12, 2026 pull-request CI runs passed and produced these line-coverage
baselines:

| Product | CI baseline | Approved reporting target | Evidence |
|---|---|---|---|
| Backend | 81.1% | 80% | [CI run 43224255856](https://github.com/Goody3/discover-jesus-backend/actions/runs/43224255856); 22 tests including SQL Server integration tests. |
| Web | 56.80% | 55% | [CI run 43224260976](https://github.com/Goody3/discover-jesus-web/actions/runs/43224260976); 34 tests. |
| iOS app | 25.50% | 25% | [CI run 43224261010](https://github.com/Goody3/discover-jesus-ios/actions/runs/43224261010); 6 tests. |

These are PR-run baselines, not merged-branch measurements. Backend uses
Coverlet, web uses Vitest V8 over application source, and iOS uses Xcode
`xccov` for the `DiscoverJesus` app target, excluding the test target.
The percentages are not directly comparable across products.

## Decision

The platform owner approved the targets above on November 13, 2026 as
**reporting goals only**, explicitly choosing not to gate CI on these values.
CI continues to publish measured coverage and reports; a result below a
target must not fail CI solely because of coverage.

Existing build, test, and dependency checks remain unchanged. A future choice
to enforce coverage requires a separate owner-approved decision.

## Alternatives considered

- **Fail CI below the targets:** Not selected by the owner at this time.
- **Defer targets further:** Not selected now that CI baselines are available.

## Consequences

Coverage can be compared with the reporting goals without blocking merges.
This does not require increasing coverage beyond the existing tests.

Implementation is submitted in
[backend PR #9](https://github.com/Goody3/discover-jesus-backend/pull/9),
[web PR #7](https://github.com/Goody3/discover-jesus-web/pull/7), and
[iOS PR #3](https://github.com/Goody3/discover-jesus-ios/pull/3).
Merge and merged-branch verification remain delivery follow-up work; these
PR baselines do not establish that the changes are already on `dev`.

## Open questions

None for the targets or reporting-only mode.
