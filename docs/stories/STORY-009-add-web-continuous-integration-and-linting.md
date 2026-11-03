# STORY-009. Add continuous integration and linting to the web app

- **Status:** Complete; merged in [discover-jesus-web#4](https://github.com/Goody3/discover-jesus-web/pull/4) with CI passing
- **Epic:** [EPIC-002. Automated quality gates and test foundations](../epics/EPIC-002-automated-quality-gates-and-test-foundations.md)
- **Priority:** TBD
- **Dependencies:** None identified

## User story

As a **web maintainer**, I want every proposed web change to be linted,
type-checked, built, tested, and audited automatically, so that defects and
vulnerable dependencies are caught before merge.

## Context

The web retrospective found no CI, no ESLint or Prettier, and an engines field
of Node `>=18`. Current scripts are `build` (`tsc -b && vite build`) and `test`
(TypeScript compile plus Node's test runner). The CI service is **unknown**.
Implementation belongs in the `discover-jesus-web` repository.

## Acceptance criteria

1. A pipeline runs on every pull request and on pushes to the integration
   branch.
2. The pipeline installs from the lockfile, then runs lint, type-check, build,
   and tests, and fails on any failure.
3. ESLint is configured with React hooks and accessibility rules, and the code
   passes it. Existing findings are fixed or explicitly suppressed with a
   reason.
4. A formatter is configured and checked.
5. `npm audit` (or an equivalent) runs and reports findings; the failure
   threshold is agreed with the owner.
6. The supported Node version is pinned in a version file and matches CI.
7. Generated build output such as `.test-build/` is ignored by version control.
8. Scripts for lint and format are documented in the README or CONTRIBUTING.

## Scope

### Included

- CI, ESLint, formatter, audit, and Node version pinning.

### Excluded

- New tests (see [STORY-010](STORY-010-add-web-component-and-api-tests.md)).
- End-to-end browser tests.
- Splitting large source files ([EPIC-006](../epics/EPIC-006-maintainable-client-and-service-structure.md)).

## Dependencies and constraints

- Enabling accessibility lint rules may surface many findings in the current
  large `App.tsx`; fix only what is needed to pass, and record the rest.

## Decisions

- **CI service:** GitHub Actions, matching the backend.
- **Audit threshold:** `npm audit` reports all findings and fails on High or
  Critical, matching the backend.
- **Formatter:** Prettier.
- **Node version:** 22, pinned in `.nvmrc` and used by CI. `engines` is
  `>=20.9.0`, the lowest version ESLint 9 supports.

## Implementation status

- ESLint (typescript-eslint, React hooks, jsx-a11y), Prettier, the CI workflow,
  `.nvmrc`, and README script documentation are in
  [discover-jesus-web#4](https://github.com/Goody3/discover-jesus-web/pull/4).
- ESLint reported four findings: one fixed, two suppressed with reasons
  (`exhaustive-deps`, `no-autofocus`), and one resolved by configuring the
  label rule depth. No other accessibility findings were reported.
- Prettier reformatted several files, including `App.tsx` and `styles.css`.
- The first CI run failed because `dev` contained a truncated duplicate of the
  `App` function that breaks type-checking and the build. The PR removes it; this
  also demonstrates the failing-pipeline check. The final run passes.
- `npm audit` shows no runtime vulnerabilities and one moderate `minimatch`
  finding in the ESLint toolchain, reported and below the failure threshold.

## Completion checks

- A pull request with a lint error fails the pipeline; a clean one passes.
- `npm run lint`, type-check, build, and test commands succeed locally.
