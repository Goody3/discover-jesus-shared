# Stories

Stories are the most granular planning documents in this hierarchy. Create one
Markdown document per story in this folder, using [`template.md`](template.md).
Do not combine multiple stories into a single document.

When asked to implement a story, the agent should follow the
[story implementation workflow](../../ai/developer-guide.md#story-implementation-workflow)
by default. Identify the story by ID or path; the user should not need to
restate the process steps.

Use a stable identifier such as `STORY-001` in the filename and title, for
example `STORY-001-render-page-content.md`. Each story must link to its parent
[epic](../epics/README.md) and describe a focused, reviewable increment of work.

## Story contents

Each story should include:

- the user or system perspective and the need being addressed;
- a concise description of the desired behavior or outcome;
- testable acceptance criteria;
- relevant dependencies, constraints, and references;
- the completion checks needed to verify the criteria.

Keep scope small enough to plan and validate as one increment. If a story
contains independent outcomes or cannot be reviewed as one coherent change,
split it into separate story documents and update the parent epic.

## Planned stories

- [STORY-001. Define the reusable component reference contract](STORY-001-define-component-reference-contract.md) —
  complete; owner-reviewed proposal and page-schema reference shape documented.
- [STORY-002. Define explicit page-navigation references](STORY-002-define-page-navigation-contract.md) —
  complete; owner-reviewed proposal and action schemas documented.
- [STORY-003. Define API, compatibility, and migration requirements](STORY-003-define-contract-migration-requirements.md) —
  complete; current non-production migration plan documented, future
  production gates recorded.
- [STORY-004. Update the backend for reusable components and page navigation](STORY-004-update-backend-component-and-navigation-contracts.md) —
  complete; backend implementation validated and handed off in local development.
- [STORY-005. Update the web interface for the new backend](STORY-005-update-web-interface-for-backend-contracts.md) —
  complete; local-development integration and recovery checks passed.
- [STORY-006. Deprecate slug-based page retrieval](STORY-006-deprecate-slug-based-page-retrieval.md) —
  proposed follow-up; consumer migration and deprecation criteria are not yet
  agreed.
- [STORY-007. Add continuous integration to the backend](STORY-007-add-backend-continuous-integration.md) —
  complete; merged workflow and negative-case checks verified; EPIC-002.
- [STORY-008. Add backend integration tests and a dedicated test project](STORY-008-add-backend-integration-tests.md) —
  complete; merged in
  [discover-jesus-backend#7](https://github.com/Goody3/discover-jesus-backend/pull/7);
  EPIC-002.
- [STORY-009. Add continuous integration and linting to the web app](STORY-009-add-web-continuous-integration-and-linting.md) —
  complete; merged in
  [discover-jesus-web#4](https://github.com/Goody3/discover-jesus-web/pull/4);
  EPIC-002.
- [STORY-010. Add web component and API client tests](STORY-010-add-web-component-and-api-tests.md) —
  complete; merged in
  [discover-jesus-web#5](https://github.com/Goody3/discover-jesus-web/pull/5);
  EPIC-002.
- [STORY-011. Add continuous integration to the iOS app](STORY-011-add-ios-continuous-integration.md) —
  complete; iOS CI workflow added with build/test and dependency scanning;
  EPIC-002.
- [STORY-012. Add iOS decoding, rendering, and navigation tests](STORY-012-add-ios-decoding-rendering-and-navigation-tests.md) —
  complete; `DiscoverJesusAppTests` added for decoding, rendering, navigation,
  and error-path coverage; EPIC-002.
- [STORY-013. Agree and report test coverage targets](STORY-013-agree-and-report-coverage-targets.md) —
  in progress; CI baselines and reporting-only targets approved across backend,
  web, and iOS; PR review and merge pending; EPIC-002.
