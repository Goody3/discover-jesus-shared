# Contributing Guidelines — Discover Jesus Shared

This repository contains the shared contracts, schemas, and AI development rules
for the Discover Jesus ecosystem (iOS, Android, Backend, Web).

## Branching Model

We use a simple, AI-friendly branching model:

### main
- Production-ready content
- Only merged via pull requests
- Protected branch

### dev
- Integration branch
- All feature branches merge here first
- Used for testing and validation

### feature/<name>
- One branch per feature
- Examples:
  - feature/page-schema
  - feature/actions-schema
  - feature/backend-contracts
- Created from `dev`
- Merged back into `dev` when complete

### fix/<name>
- One branch per bug fix
- Examples:
  - fix/schema-typo
  - fix/openapi-versioning
- Created from `dev`
- Merged back into `dev`

## Commit Style

Use clear, descriptive commit messages:

- `Add initial page.schema.json`
- `Refactor actions schema for conditional navigation`
- `Fix typo in component schema`

## Pull Requests

- PRs should target `dev`
- PRs into `main` require review
- Include a summary of changes
- Include any schema version updates

## AI Development Rules

AI agents (ChatGPT, GROC, etc.) should follow:

- JSON naming conventions
- Schema versioning rules
- Folder structure consistency
- Commit message style above
- Avoid generating secrets or credentials
- Always update documentation when schemas change
- Follow the repository-level [AI development guide](ai/developer-guide.md)
- Keep implementation claims limited to code and tests that were actually
  available during the change

## Documentation

Platform documentation is indexed in [docs/README.md](docs/README.md). Keep
contracts and related documentation in sync, distinguish confirmed facts from
proposals, and use [architecture decision records](architecture/decisions/README.md)
for consequential cross-cutting technical choices.

## Folder Structure

```text
.
├── ai/                    # AI-assisted development guidance
├── api/                   # Backend API contract
├── architecture/          # Platform overview and decision records
├── docs/                  # Vision, epics, stories, and platform guidance
│   ├── epics/             # One document per epic
│   └── stories/           # One document per story
└── schemas/               # Shared dynamic-content JSON schemas
```

## Folder Structure

