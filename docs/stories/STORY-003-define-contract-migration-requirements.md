# STORY-003. Define API, compatibility, and migration requirements

- **Status:** Complete; contract impact, migration, and non-production transition requirements documented
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Dependencies:** [STORY-001](STORY-001-define-component-reference-contract.md),
  [STORY-002](STORY-002-define-page-navigation-contract.md)

## User story

As a platform maintainer, I want the API, compatibility, and migration
requirements understood before changing shared contracts, so that backend and
web updates can be coordinated, including an explicitly accepted temporary
incompatibility.

## Context

The shared OpenAPI document and JSON schemas describe contracts; the backend
and web source are separate repositories. The platform owner confirms there is
currently no production environment. Mobile-client status and any end-user
runtime consumer remain unidentified.

The owner-reviewed STORY-002 proposal uses UUID `pageId` for internal
navigation, separate modal and HTTPS external-URL actions, no generic string
`target`, and requires legacy-target migration before rollout. The Platform
owner is the transition owner; communications are internal-only, and recovery
is forward-fix only. Since no production environment exists, production
downtime checks and a production incompatibility window do not apply to the
current migration. Any future production launch needs a separately approved
transition plan and runtime-client inventory.

## Acceptance criteria

1. The API operations and schema definitions affected by component storage and
   page navigation are identified.
2. The compatibility plan identifies the current web interface as temporarily
   incompatible after the backend update; backward compatibility during this
   transition is not required.
3. A migration approach for existing inline components and navigation targets
   is documented, or the information needed to decide one is listed.
4. Required coordination and validation in backend and client repositories is
   identified, including consumers that remain unknown.
5. The transition plan documents the current environment, owner,
   communications, monitoring, recovery steps, and any production-specific
   decisions that are not applicable until production exists.
6. Backend-first and dependent web-update stories are sequenced after the
   contract proposals and migration requirements have been reviewed.

## Contract and API impact

| Surface | Current contract or behavior | Required coordination |
|---|---|---|
| Shared [`api/openapi.json`](../../api/openapi.json) | `GET /pages/{id}`, `GET /pages/{id}/versions/{version}`, and `GET /lessons/{id}/pages` use UUID identities and `Page.content.blocks`. This is not the backend repository's current API contract. | Decide separately whether and how reusable component references and typed navigation are represented in this API. STORY-001 intentionally did not map `sections` to `content.blocks`; do not assume the proposed page-schema references already apply here. |
| [`page.schema.json`](../../schemas/page.schema.json) | Defines string page `id`, `sections`, inline nodes, component references, and action shapes. | Align page identity with the authoritative OpenAPI UUID before implementation. Define how actions nested in page nodes are validated and serialized. |
| [`component.schema.json`](../../schemas/component.schema.json) | Defines renderable component types and nested content; it is not the reusable-definition store contract. | Review supported types and properties after the production allowlist is approved. Preserve existing inline content unless a deliberate migration is approved. |
| [`actions.schema.json`](../../schemas/actions.schema.json) | Proposes `navigate`/`navigateIf` with `pageId`, `showModal` with `modalId`, and HTTPS-only `openExternalUrl`; rejects the generic `target` field. | Backend and every authoring/runtime client must validate the same shapes. The `navigateIf` condition language remains undecided. |
| Backend `api/openapi.json` and page/action endpoints | `GET /api/pages`, `GET /api/pages/{slug}`, page create/update/delete, `/api/pages/{pageId}/components`, component CRUD, `/api/components/{componentId}/actions`, and action CRUD use integer IDs/slugs; actions are opaque JSON and are not executed. Pages own components. | Backend must align page identity, page/component references, action validation, persistence, errors, and its API description. Inventory and migrate stored action configurations before rejecting `target`. |
| Web `src/api.ts` and `src/App.tsx` | Admin UI uses integer IDs and slugs, edits components and opaque action JSON, and calls the backend CRUD API. It does not execute actions or render end-user dynamic pages. | Update types, editor authoring/validation, and API calls only after backend contract is available. `npm run build` is available; no web test script is defined. |

The currently accessible implementation repositories are
`discover-jesus-backend` and `discover-jesus-web`. The web repository is an
admin/content-authoring interface, not evidence of the end-user runtime
consumer. Mobile-client status and any other runtime consumers are unknown;
identify them before rollout and include them in the compatibility review.

## Compatibility and migration requirements

ADR 0003 accepts the planned backend-first order and temporary incompatibility.
There is currently no production environment, so this migration has no
production downtime window to plan or monitor. In the current non-production
environment, the old web interface is not required to remain backward
compatible during the coordinated backend-to-web update. Schedule STORY-005
before applying the backend change in any shared environment and verify the
end-to-end flow there. Before any future production launch, obtain separate
approval for a maximum incompatibility window and its operational plan.

### Existing page-owned components

- Keep existing components inline by default; STORY-001 makes migration to
  reusable definitions opt-in.
- Do not bulk-convert components before the production type allowlist, source
  content, component ownership, locale/revision mapping, and reference
  validation rules are approved.
- If migration is selected, inventory source pages, map each component to an
  approved type and reusable UUID, preserve content and order, validate all
  page references, and retain a recoverable source snapshot. Unmapped or
  invalid records require manual resolution; do not silently drop them.
- Identify whether any clients consume page-owned components in a shape other
  than the current backend's nested `components` response.

### Existing navigation targets

- Inventory persisted action records and configuration JSON before rollout.
  At planning time, the backend API stored arbitrary action configuration and
  its database contents had not been inspected. A read-only inventory during
  STORY-004 found no action records or legacy `target` values in the currently
  configured non-production dataset. This is a snapshot only; repeat the
  inventory before a future production launch or importing other data.
- Classify each legacy `target` as an internal page identifier/slug, modal
  identifier, external URL, or unknown value. Map page targets through an
  authoritative page-identity table; convert modal targets to `modalId`, and
  convert only approved HTTPS URLs to `openExternalUrl`.
- Unknown, ambiguous, invalid, or unmappable targets block migration and
  require manual resolution. Produce a dry-run report and a verifiable record
  count before applying an idempotent migration.
- Complete migration and validation before deploying a backend that rejects
  `target`. No compatibility shim or dual-read period is part of the reviewed
  direction.
- The backend currently has integer IDs/slugs, the shared OpenAPI has UUID
  `Page.id`, and `page.schema.json` has a string `id`. Define and validate the
  mapping before converting any navigation data.

## Repository coordination and validation

| Repository or consumer | Required work and validation |
|---|---|
| Shared contracts | Keep OpenAPI, page/component/action schemas, ADRs, and migration instructions aligned. Validate schemas, examples, and generated API documentation. |
| Backend | Implement UUID identity mapping, reference resolution and publication checks, typed action validation, migrations, and the agreed API behavior. Run unit/integration tests, database migration checks against representative existing data, and API contract tests. The available backend has no navigation execution behavior. |
| Web admin | Update API types and authoring controls to produce approved payloads; verify create/edit/load flows and build with `npm run build`. The inspected project has no configured test script. |
| End-user runtime clients | Identify all web/mobile or other clients that render pages and execute actions. Add contract and end-to-end tests for component resolution, page navigation, modal behavior, HTTPS external links, and unavailable destinations. No runtime consumer has been identified in the accessible web repository. |

Do not treat the admin UI's ability to save action JSON as proof that a
navigation action is executed or works end to end.

## Production transition plan

| Control | Requirement |
|---|---|
| Sequence | Backend rollout first; update the web interface and identified runtime consumers immediately afterward. STORY-005 is a required dependent release, not optional cleanup. |
| Current production downtime window | **Not applicable.** The platform owner confirms there is no production environment. |
| Future production launch | Before production exists or a production rollout is planned, approve a maximum incompatibility window and record the release-specific transition plan. |
| Accountable owner | Platform owner coordinates and approves the transition. |
| User communications | Internal stakeholders only; no end-user notice was requested. |
| Monitoring | Monitor backend health/availability, web/API errors and latency, and synthetic page-load/navigation checks. Ensure checks cover the actual runtime consumer once identified. |
| Recovery | Forward-fix only. Prepare the web/client correction and escalation path before rollout; do not assume backend rollback is safe after schema/data changes. |
| Recovery verification | Verify page loading, reusable-component rendering, internal page navigation, HTTPS external navigation, and authoring/editor save behavior against the updated backend. Include modal behavior where a runtime consumer supports it. |

The current non-production migration does not require production downtime
checks. Identify runtime consumers and assign verification ownership before
any future production launch; include the actual end-user runtime in
release-specific monitoring and recovery checks.

## Implementation sequence

1. Complete STORY-001 and STORY-002 contract proposals and retain their open
   implementation questions as explicit backend/web requirements.
2. Use this story's migration requirements and current non-production
   transition plan. Before any future production launch, separately establish
   the production window and identify runtime consumers.
3. Prepare and validate the backend implementation and data migration in
   STORY-004; schedule the web update before applying the backend change in a
   shared environment.
4. Update the web interface and identified runtime consumers in STORY-005,
   then verify the end-to-end recovery checks before closing the transition.

## Dependencies and constraints

- Depends on [STORY-001](STORY-001-define-component-reference-contract.md) and
  [STORY-002](STORY-002-define-page-navigation-contract.md).
- Implementation repositories and API consumers need to be identified.
- The current backend-first incompatibility is limited to the coordinated
  non-production transition; schedule and own the web follow-up before
  applying the backend change in a shared environment.

## Completion checks

- [x] Contract impact, API surfaces, and accessible consumers are documented;
  unidentified runtime/mobile consumers are called out.
- [x] Compatibility and migration requirements are documented, including the
  reviewed no-legacy-target policy and opt-in component migration.
- [x] Backend-first STORY-004 and dependent STORY-005 are listed under
  EPIC-001.
- [x] Current absence of a production environment is recorded; production
  downtime planning is explicitly not applicable to this migration.
- [x] Future production-window approval and runtime-client inventory are
  recorded as pre-production-launch gates rather than blockers for this story.
