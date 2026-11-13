# STORY-013. Agree and report test coverage targets

- **Status:** Complete; implementation merged to `dev` and merged-branch CI verified on November 13, 2026
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** [STORY-007](STORY-007-add-backend-continuous-integration.md),
  [STORY-009](STORY-009-add-web-continuous-integration-and-linting.md),
  [STORY-011](STORY-011-add-ios-continuous-integration.md), and the test
  stories [STORY-008](STORY-008-add-backend-integration-tests.md),
  [STORY-010](STORY-010-add-web-component-and-api-tests.md),
  [STORY-012](STORY-012-add-ios-decoding-rendering-and-navigation-tests.md)

## User story

As a **platform owner**, I want agreed coverage targets reported for each
product, so that I can see whether testing is improving.

## Context

EPIC-002 requires coverage targets to be agreed and reported, and says the
targets are for the owner to decide. Measuring coverage only makes sense once
the tests and pipelines from the other stories are in place. This story spans
the backend, web, and iOS repositories and records the decision in this
repository.

As of November 2026, the prerequisite CI and baseline test stories are marked
complete:

- Backend CI and tests: STORY-007 and STORY-008.
- Web CI/lint and tests: STORY-009 and STORY-010.
- iOS CI and tests: STORY-011 and STORY-012.

This means STORY-013 can now focus on selecting per-product coverage tooling,
capturing first baselines from CI output, and documenting owner-approved
targets (reporting-only or enforced gates).

## Implementation status

- Backend coverage collection and a CI summary/artifact are implemented with
  Coverlet in [merged PR #9](https://github.com/Goody3/discover-jesus-backend/pull/9).
- Web coverage collection is implemented with Vitest's V8 provider and CI
  uploads the reports in [merged PR #7](https://github.com/Goody3/discover-jesus-web/pull/7).
- iOS coverage collection, workflow summary, and artifact upload are implemented
  with Xcode coverage and `xccov` in
  [merged PR #3](https://github.com/Goody3/discover-jesus-ios/pull/3).
- On November 13, 2026, the platform owner approved reporting-only line-coverage
  targets of 80% for backend, 55% for web, and 25% for iOS, explicitly choosing
  no coverage gates. See the
  [accepted decision](../../architecture/decisions/0004-reporting-only-coverage-targets.md).
- All three product PRs merged into `dev` on November 13, 2026. Each merged-branch
  CI run passed and published coverage reports.

## CI coverage baselines

These are line-coverage baselines from pull-request CI runs on November 12,
2026, not merged-branch measurements. Percentages use each product's coverage
tool and are not directly comparable.

| Product | CI baseline | Evidence |
|---|---|---|
| Backend | 81.1% | [CI run 43224255856](https://github.com/Goody3/discover-jesus-backend/actions/runs/43224255856), Coverlet; 22 tests passed, including SQL Server integration tests. |
| Web | 56.80% | [CI run 43224260976](https://github.com/Goody3/discover-jesus-web/actions/runs/43224260976), Vitest V8; 34 tests passed. |
| iOS | 25.50% | [CI run 43224261010](https://github.com/Goody3/discover-jesus-ios/actions/runs/43224261010), Xcode `xccov` for the `DiscoverJesus` app target; 6 tests passed. |

## Merged-branch verification

The November 13, 2026 `dev` push workflows all passed, including their
dependency checks and coverage publication:

| Product | Line coverage | Successful `dev` run |
|---|---|---|
| Backend | 81.1% | [Run 43431067330](https://github.com/Goody3/discover-jesus-backend/actions/runs/43431067330), merge revision `4c0338e37784ac74fb763735f39f8ea6bd2a311b`. |
| Web | 56.80% | [Run 43431150278](https://github.com/Goody3/discover-jesus-web/actions/runs/43431150278), merge revision `1637a4ee1f1dd729555e647409da95078349c48f`. |
| iOS app | 25.50% | [Run 43431241152](https://github.com/Goody3/discover-jesus-ios/actions/runs/43431241152), merge revision `9f6245e739ae974fe6ae1886f2c046f1ee68a223`. |

## Acceptance criteria

1. Coverage is measured for each product in CI, using a tool chosen for that
   product.
2. A baseline for each product is recorded after the test stories are done.
3. The platform owner decides the target for each product, and whether each
   target is a reporting goal or a failing gate. The decision is documented in
   this repository.
4. Coverage results are visible on each pull request or in the pipeline output.
5. If a target is a failing gate, the pipeline enforces it.

## Scope

### Included

- Coverage measurement, baseline, target decision, and reporting.

### Excluded

- Raising coverage beyond what the other stories deliver.
- Cross-product aggregate coverage.

## Dependencies and constraints

- Targets are reporting-only; CI must not fail solely because coverage falls
  below them.

## Completion checks

- Each product's pipeline outputs a coverage figure.
- The decision record states the baseline and the owner-approved targets.
- Verified: PR and merged-branch workflows publish coverage, the accepted
  decision records the baselines and targets, and all targets are reporting-only.
  Acceptance criterion 5 does not require enforcement because no failing
  coverage gates were approved.
