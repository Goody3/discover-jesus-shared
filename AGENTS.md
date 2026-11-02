# Repository guidance

This repository holds shared platform documentation and contracts. Read the
[README](README.md), [documentation index](docs/README.md), and relevant source
contracts before making changes.

## Working rules

- Treat checked-in specifications and schemas as the source of truth for their
  respective contracts. Do not infer undocumented runtime behavior from an
  example or schema.
- Keep confirmed facts, proposals, and open questions distinguishable. If a
  platform detail is unknown, record it as unknown rather than inventing it.
- When a shared contract changes, check related schemas and documentation for
  consistency. Call out any corresponding implementation work outside this
  repository.
- Update the relevant documentation when changing a contract or a documented
  platform decision.
- Do not add credentials, tokens, private user data, or other secrets to source
  files, examples, or generated documentation.
- Preserve unrelated working-tree changes and follow the focused validation
  steps in [CONTRIBUTING](CONTRIBUTING.md).

## Story-driven work

When asked to work on a story in `docs/stories/`, follow the
[story implementation workflow](ai/developer-guide.md#story-implementation-workflow)
by default; the user does not need to restate those steps. The user only needs
to identify the story, for example “Work on STORY-004.”

If the implementation belongs to another repository, use that repository for
code and tests. When possible, keep this repository and the implementation
repository open in the same VS Code workspace so the story, contracts, and
workflow are available together. Do not claim that guidance in this repository
was loaded when the workspace does not include it.

## Scope

This repository does not currently contain the backend or web application
implementations. Do not claim that a contract change has been implemented or
tested in those systems unless their source and validation are available.
