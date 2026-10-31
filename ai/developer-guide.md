# AI developer guide

This guide applies to AI-assisted work in the Discover Jesus platform
repositories. The shared repository contains contracts and guidance; backend
and web application implementations are outside its scope.

## Before making a change

1. Read the repository's `AGENTS.md`, the relevant contribution guidance, and
   the source contract or implementation involved.
2. Identify which repository owns the requested change. A shared-contract
   update does not, by itself, implement or validate that contract in a client
   or service.
3. Separate confirmed behavior from assumptions, proposals, and open
   questions. Do not invent platform architecture, product requirements,
   implementation status, or test results.
4. Ask for clarification when a choice materially affects user-visible
   behavior, platform boundaries, contract compatibility, or future direction.

## Story implementation workflow

When the user asks to work on a story in `docs/stories/`, follow this workflow
without requiring them to repeat it:

1. Locate the story by ID or path. Read its acceptance criteria, parent epic,
   listed dependencies, linked architecture decisions, and relevant contracts.
2. Confirm dependencies and prerequisites are satisfied. If a material
   decision or dependency is unresolved, do not invent an answer; ask or
   clearly limit the work to what can safely proceed.
3. Identify the owning repository from the story. Inspect that repository's
   instructions and working-tree state. Implement backend or web application
   changes in their owning codebase, not in this shared repository.
4. Make the smallest complete change that meets the acceptance criteria. Add
   or update focused tests and run the narrowest applicable validation in the
   owning repository.
5. Update shared API definitions, schemas, architecture, or planning documents
   when the implementation changes a shared contract or decision. Keep those
   changes consistent with the implementation and avoid claiming code or test
   results for repositories that were unavailable.
6. Do not deploy, release, or accept a planned production incompatibility
   unless the user explicitly asks and the agreed transition plan is available.
   For EPIC-001, follow the backend-first sequence and its dependent web story.
7. Summarize changed repositories/files, acceptance criteria addressed,
   validation and results, remaining work, and unresolved questions. Do not
   mark a story complete unless its criteria have actually been verified.

The story may be identified in a short request such as “Work on STORY-004.”
For a reusable guided entry point, use the workspace
[`work-on-story` prompt](../.github/prompts/work-on-story.prompt.md).
In a multi-root VS Code workspace, include this shared repository and the
implementation repository. If only the implementation repository is open,
this repository's workflow instructions may not be available to the agent.

## Shared contracts and documentation

- Treat each checked-in API definition or JSON schema as authoritative for the
  contract it defines. Do not assume similarly named structures are
  interchangeable.
- Before changing a contract, inspect its references and related schemas for
  compatibility and consistency.
- Update relevant documentation with the contract change. Call out which
  implementation repositories may need coordinated updates.
- Describe only validation that was actually performed. If an implementation
  or environment is unavailable, state that it was not validated.
- Record consequential cross-cutting decisions using the
  [architecture decision process](../architecture/decisions/README.md).

## Safety and quality

- Never put credentials, tokens, private user data, or secrets in code,
  documentation, examples, logs, or prompts.
- Prefer small, reviewable changes that fit existing conventions. Do not
  introduce tooling or dependencies without a concrete need.
- Preserve unrelated changes in the working tree.
- Verify changes with the narrowest applicable validation. For schema edits,
  at minimum validate JSON syntax and check affected references; run
  implementation tests in their owning repository when available.

## Completion summary

At the end of a task, report the files or contracts changed, validation run and
its result, any coordinated work needed in other repositories, and unresolved
questions. Never present proposed work as completed implementation.
