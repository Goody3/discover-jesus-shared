# Documentation

This documentation is the evolving reference for the Discover Jesus platform.
It should describe verified behavior and architecture while making proposals
and unanswered questions visible.

## Platform

- [Platform vision](vision.md) - high-level direction, audience, outcomes, and
  guiding principles to be defined by the platform owner.
- [Component store proposal](components/component-catalog-proposal.md) -
  preliminary component catalog based on the local Content Studio pages.
- [Embedded component library examples](components/embedded-component-library-examples.md) -
  UUID catalog for the published test copies derived from existing pages.
- [Reusable component reference contract](components/component-reference-contract-proposal.md) -
  draft of the reference model, lifecycle questions, and affected contracts.
- [Explicit page-navigation decision](../architecture/decisions/0002-explicit-page-navigation-references.md) -
  owner-directed proposal for typed page destinations and remaining validation
  and migration questions.
- [Epics](epics/README.md) - planned bodies of work that advance the vision,
  including the proposed [reusable components and page navigation epic](epics/EPIC-001-reusable-components-and-page-navigation.md).
- [Stories](stories/README.md) - individual, granular delivery documents,
  linked to their parent epics.
- [System overview](../architecture/system-overview.md) - repository scope,
  known platform boundaries, shared contracts, and open questions.
- [Architecture decisions](../architecture/decisions/README.md) - decision
  record process and template.

## Development

- [AI developer guide](../ai/developer-guide.md) - using AI assistants safely
  and consistently in this project.
- [Work on a story prompt](../.github/prompts/work-on-story.prompt.md) - start
  story work using the documented implementation workflow.
- [Contributing guidelines](../CONTRIBUTING.md) - branch and review practices.

## Source contracts

- [Backend API](../api/openapi.json) - OpenAPI 3.1 definition.
- [Dynamic page schema](../schemas/page.schema.json)
- [Component schema](../schemas/component.schema.json)
- [Action schema](../schemas/actions.schema.json)

## Documentation conventions

- Prefer linking to an existing source contract over duplicating its details.
- Label proposals and assumptions; do not present them as current behavior.
- Include a status or date when it helps readers judge whether information is
  current.
- Record consequential, cross-cutting technical choices as decision records.
- Keep documentation focused and update it alongside the relevant contract or
  implementation change.

The platform overview is intentionally incomplete where repository evidence is
not yet available. Expand it as implementation details and product decisions
are confirmed.
