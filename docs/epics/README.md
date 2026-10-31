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
  complete for local-development integration; production rollout remains out of
  scope because no production environment exists.
