# STORY-005. Update the web interface for the new backend

- **Status:** Complete; local-development transition validated
- **Epic:** [EPIC-001. Reusable components and explicit page navigation](../epics/EPIC-001-reusable-components-and-page-navigation.md)
- **Priority:** TBD
- **Owner:** Dalen
- **Dependencies:** [STORY-004](STORY-004-update-backend-component-and-navigation-contracts.md)

## User story

As a user of the Discover Jesus web interface, I want it to consume the
backend's reusable-component and page-navigation contracts, so that the
interface continues to display pages and navigate correctly after the backend
update.

## Implementation progress

In `discover-jesus-web`, the Content Studio offers a
published-page preview that fetches the locale-specific page-content endpoint,
renders expanded section nodes, and follows available `navigate` actions by
public page UUID. It reports missing or invalid content and preview limitations
instead of silently dropping them. HTTPS external links are opened safely;
conditional navigation and content requiring runtime bindings are not
evaluated because their semantics/data are not available in this interface.
The Vite development proxy now defaults to the local backend at
`http://localhost:5187`.

The studio now also has a Reusable components workspace for creating drafts,
loading existing drafts or published revisions by UUID, saving drafts,
publishing, withdrawing, and deleting components. Page details include a
locale-specific sections editor with load/save/publish actions and a helper to
insert a `componentReference` by UUID. The backend has no list-components
endpoint, so discovery of existing component UUIDs remains a documented
limitation rather than being represented as a list the API cannot provide.

On 2026-10-31, the refreshed backend's reusable-component store was populated
with 24 published English test examples copied from the page-owned components
on `TestPage01`–`TestPage06` and `Welcome`. The original page components were
left unchanged. Their UUIDs and the legacy-to-current content mappings are
recorded in the [embedded component library examples](../components/embedded-component-library-examples.md)
catalog. Since the API has no list route, the Content Studio still loads these
entries by UUID.

The six source pages have also been duplicated as published `TestPage07`–
`TestPage12` pages in English. Each page's sections reference the corresponding
published component examples in source order; the legacy page-component lists
for these duplicates are empty. HTTP verification confirmed each page-content
response expands the expected component UUIDs. The page UUIDs are in the
component example catalog.

The backend response-mapping fix is active on the refreshed local API at port
5187. The web app's production build, dependency audit, and five persistent
preview/navigation tests pass. Browser checks against the live API verified
expanded reusable content on `TestPage07`–`TestPage12`, button navigation from
`TestPage07` to `TestPage08` and from `TestPage11` to `TestPage07`, and a clear
missing-locale error. The authoring workspace also loaded the published
revision 2 navigation component by UUID. Existing English embedded pages were
not changed.

The Content Studio is available at `http://localhost:5173` in this local
development environment. This validates the backend/web transition here; it
does not represent a production deployment. There is no production environment
or additional runtime client in scope. Persistent tests cover rendering,
navigation resolution/action forwarding, and invalid or unresolved content.
The component store still has no list endpoint, so existing component records
are loaded by UUID.

## Context

The backend implementation is available in the current local development
environment at `http://localhost:5187` as of 2026-10-31. This localhost URL is
not generally reachable from another machine; use an appropriately reachable
development URL if the web application runs elsewhere. Per
[ADR 0003](../../architecture/decisions/0003-coordinated-backend-first-web-transition.md),
a short incompatibility period is acceptable in non-production; there is
currently no production environment. This story is the required follow-up to
the backend work, not optional future cleanup.

## Acceptance criteria

1. The web interface consumes page responses containing references to
   backend-managed components according to the approved contract.
2. The web interface renders the supported component content and handles
   missing, unavailable, or invalid component references according to the
   agreed behavior.
3. Navigation buttons resolve and navigate to their explicit destination pages
   according to the approved page-navigation contract.
4. Relevant web tests cover component rendering, navigation, and reference
   failure behavior.
5. The web application is validated against the updated backend, and
   end-to-end page display and navigation work.
6. The temporary incompatibility is considered resolved only after the updated
   web application is deployed or otherwise made available in the agreed
   environment and the recovery checks pass.
7. The web implementation is completed and validated in its owning repository;
   this shared story records the outcome and references that work.

## Scope

### Included

- Updating web API integration and types for the approved backend contracts.
- Rendering backend-managed reusable components.
- Updating page-navigation handling and relevant tests.
- Coordinating web deployment or release to complete the transition.

### Excluded

- Changing backend behavior beyond fixes required to satisfy the approved
  contract.
- Redesigning unrelated web functionality.

## Dependencies and constraints

- Depends on STORY-004 backend implementation and availability of the updated
  backend contract in the agreed environment.
- Scheduled to start on 2026-10-31. Before validating from another machine or
  moving to a shared environment, identify a reachable backend URL and
  coordinate that environment.
- Any future production rollout requires a separate maximum transition window
  and operational plan before deployment.

## Completion checks

- [x] Web implementation conforms to the approved contracts.
- [x] Relevant web tests pass.
- [x] End-to-end page display and button navigation are verified against the
      updated backend.
- [x] Updated web application is available at `http://localhost:5173` against
      the refreshed local backend at `http://localhost:5187`.
- [x] The local-development incompatibility window is closed. No production
      environment exists; a future production transition remains out of scope.

## Transition handoff

- **Implementation owner:** Dalen.
- **Scheduled start:** 2026-10-31.
- **Backend currently available:** `http://localhost:5187` (local development;
  refreshed API, `/health` and live content endpoints verified).
- **Web application:** `http://localhost:5173` (local development).
- **Transition state:** Complete in local development. No production rollout
  has occurred or is implied.
