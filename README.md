# Discover Jesus Shared

This repository is the shared source of platform contracts and development
guidance for the Discover Jesus platform. It is intended to keep documentation,
API definitions, and content schemas aligned as the platform evolves.

The platform owner reports that backend and web frontend code is in place. This
repository does not contain either application implementation.

## Start here

- [Documentation index](docs/README.md) - platform documentation and its status.
- [Platform vision](docs/vision.md) - high-level direction and desired outcomes.
- [Epics](docs/epics/README.md) - planned bodies of work.
- [Stories](docs/stories/README.md) - granular story documents; one file per story.
- [System overview](architecture/system-overview.md) - what is known about the
  platform boundaries and shared contracts.
- [AI developer guide](ai/developer-guide.md) - workflow and expectations for
  AI-assisted changes.
- [Work on a story prompt](.github/prompts/work-on-story.prompt.md) - reusable
  entry point for implementing a story.
- [Contributing guidelines](CONTRIBUTING.md) - branches, reviews, and changes.
- [Architecture decisions](architecture/decisions/README.md) - how to record
  decisions that affect future development.

## Shared contracts

- [OpenAPI definition](api/openapi.json) describes the backend HTTP API.
- [Page schema](schemas/page.schema.json), [component schema](schemas/component.schema.json),
  and [actions schema](schemas/actions.schema.json) define shared dynamic-content
  structures.

These are contracts, not proof that every client or service currently implements
them. Keep implementation status and unresolved questions explicit in the
documentation.
