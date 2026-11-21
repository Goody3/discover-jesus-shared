# STORY-023. Apply origin and request limits

- **Status:** Complete; merged to backend `dev` in
  [discover-jesus-backend#19](https://github.com/Goody3/discover-jesus-backend/pull/19),
  with backend CI checks passing. Not deployed.
- **Epic:** [EPIC-004](../epics/EPIC-004-secure-content-authoring-and-access.md)
- **Priority:** Before passkey cutover
- **Dependencies:** Passkey origin decisions are needed for final cutover
  settings; general API safeguards can be implemented independently.

## User story

As a platform operator, I want approved browser origins and bounded API
requests so that clients can use the service without exposing it to
unnecessary cross-origin access or resource consumption.

## Acceptance criteria

1. Cross-origin browser access is denied by default and enabled only for
   explicitly approved origins where needed. Same-origin Studio use and
   native iOS reads continue to work. CORS is not an authorization control.
2. Define and document request-size and rate-limit policies appropriate to
   published reads and authoring. Oversized requests return `413`; requests
   that exceed the rate limit return `429`.
3. The final passkey cutover covers enrollment, sign-in, and recovery
   endpoints with appropriate request limits; browser origin settings align
   with the approved session and WebAuthn design.

## Completion checks

- Test allowed and disallowed origins and request boundaries. Verify
  ordinary reads and representative authoring payloads continue to work.

## Agreed policy

- **CORS:** Read exact origins from `Cors:AllowedOrigins`; an empty list is
  the default and denies cross-origin browser access. Do not automatically
  allow localhost. Same-origin Studio use and native clients remain unaffected.
- **Request body:** 1 MiB maximum. Content images remain URL/asset references,
  not uploaded request bodies.
- **Rate limits:** Fixed windows, keyed by direct remote IP: 120 requests per
  minute on published-page reads and 30 requests per minute on authoring
  routes. Health is not rate-limited.
- **Proxies:** Do not trust forwarded IP headers by default. A deployment must
  configure only its actual trusted proxies before limits can represent
  individual clients behind a reverse proxy.
- Passkey enrollment, sign-in, and recovery endpoints must receive their own
  reviewed limits when implemented in STORY-025.

Local verification: 52 backend tests not requiring SQL Server passed, including
CORS, per-policy rate-limit, exact 1 MiB request, and OpenAPI parity tests. A
Kestrel smoke test allowed the configured Studio preflight, accepted a 1 MiB
request body, and returned `413` for a body one byte larger. These settings are
not deployed; browser origins for a hosted environment remain to be configured.
PR `build-and-test` and `dependency-scan` checks both passed.
