# EPIC-004. Secure content authoring and access

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Platform vision outcomes are not yet defined; see
  [vision.md](../vision.md). The vision's goal of serving multiple churches
  implies that authoring must be restricted to authorized people.
- **Dependencies:** [EPIC-002](EPIC-002-automated-quality-gates-and-test-foundations.md)
  and [EPIC-003](EPIC-003-contract-integrity-across-products.md), so security
  changes are tested and contract changes are tracked.

## Need and opportunity

The backend retrospective found no authentication or authorization on the
create, update, delete, draft, and publish endpoints, and no CORS policy, rate
limiting, or content payload validation. The web retrospective found no login
or token handling. This is the largest gap preventing production use.

## Outcome

Only authorized people can change content, while published content stays
readable by the clients that need it. Content payloads are validated before
they are stored.

## Success criteria

- Write endpoints require authentication and an appropriate role or policy.
- A decision is recorded on the public read surface and how iOS reads content.
- CORS allows only approved web origins.
- Rate limiting and request size limits are in place.
- Content payloads are validated (lengths, allowed component types, binding and
  action shapes).
- The web app supports login, token handling, and 401/403 responses.
- Swagger UI is restricted outside development.

## Scope

### Included

- Backend authentication, authorization, CORS, rate limiting, and validation.
- Web login and error handling.
- Compatibility of the iOS read path with the chosen read surface.

### Excluded

- End-user accounts and community features described in the vision.
- Choosing an identity provider before requirements are agreed.

## Assumptions, dependencies, and risks

- **Assumptions:** Content reads remain public. The iOS app only reads content;
  confirm this before implementation.
- **Dependencies:** Identity provider and role model are **unknown** and need an
  architecture decision record.
- **Risks:** Adding authentication is a breaking change for current local
  development workflows and for any consumer of write endpoints.

## Stories

| ID | Story | Status |
|---|---|---|
| — | No stories defined yet | — |
