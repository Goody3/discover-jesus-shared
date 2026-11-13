# Epics

Epics describe planned bodies of work that advance the
[platform vision](../vision.md). An epic should express an outcome or capability
at a level that can be delivered through multiple stories; it is not a
collection of implementation tasks.

Create one Markdown document per epic in this folder, using
[`template.md`](template.md). Use a stable identifier such as `EPIC-001` in
the filename and document title, for example `EPIC-001-dynamic-content.md`.

## Epic contents

Each epic document should state:

- the user or platform need and intended outcome;
- scope, exclusions, and assumptions;
- measurable success criteria where possible;
- dependencies and risks;
- the stories that break the epic into deliverable increments.

Keep the story list in the epic document in sync with the individual documents
in [`../stories/`](../stories/README.md). Stories should link back to their
parent epic.

## Planned epics

- [EPIC-001. Reusable components and explicit page navigation](EPIC-001-reusable-components-and-page-navigation.md) —
  core scope complete for local-development integration; a slug-based page
  retrieval deprecation follow-up is proposed. Production rollout remains out
  of scope because no production environment exists.
- [EPIC-002. Automated quality gates and test foundations](EPIC-002-automated-quality-gates-and-test-foundations.md) —
  in progress; CI coverage baselines and reporting-only targets recorded;
  coverage PR review and merge pending.
- [EPIC-003. Contract integrity across backend, web, and iOS](EPIC-003-contract-integrity-across-products.md) —
  proposed.
- [EPIC-004. Secure content authoring and access](EPIC-004-secure-content-authoring-and-access.md) —
  proposed.
- [EPIC-005. Reliable and consistent API behavior](EPIC-005-reliable-and-consistent-api-behavior.md) —
  proposed.
- [EPIC-006. Maintainable client and service structure](EPIC-006-maintainable-client-and-service-structure.md) —
  proposed.
- [EPIC-007. Accessible and localized content experience](EPIC-007-accessible-and-localized-content-experience.md) —
  proposed.

EPIC-002 through EPIC-007 are numbered in the recommended implementation order.
They come from the [Sprint 01 retrospectives](../retrospectives/Sprint01/).
