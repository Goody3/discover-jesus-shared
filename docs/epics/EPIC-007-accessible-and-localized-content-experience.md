# EPIC-007. Accessible and localized content experience

- **Status:** Proposed
- **Owner:** TBD
- **Vision alignment:** Supports the goal in [vision.md](../vision.md) that all
  users can hear the gospel and mature spiritually.
- **Dependencies:** None identified. Easier after
  [EPIC-006](EPIC-006-maintainable-client-and-service-structure.md), but it can
  start earlier.

## Need and opportunity

The vision is aimed at all people. The iOS retrospective found limited
VoiceOver support for dynamic components and a locale fixed to en-US. The web
retrospective found a hand-built modal that does not manage focus and called
for a keyboard and screen-reader audit.

## Outcome

People using assistive technology can read and navigate content in both
clients, and readers can choose their locale where content is available.

## Success criteria

- iOS dynamic components expose meaningful accessibility labels and work with
  VoiceOver.
- The web dialog traps focus, closes with Escape, and restores focus; a keyboard
  and screen-reader audit has no blocking findings.
- The iOS app lets the user select a locale, with a defined fallback when
  content is not available in it.

## Scope

### Included

- iOS VoiceOver support and locale selection.
- Web dialog accessibility and audit.

### Excluded

- Translating content.
- iOS image placeholder and visual polish.
- Right-to-left layout, unless the audit requires it.

## Assumptions, dependencies, and risks

- **Assumptions:** The backend already serves content by locale, as the web
  preview locale selector suggests.
- **Dependencies:** None.
- **Risks:** The set of supported locales and the fallback strategy are
  **unknown** and need an owner decision.

## Stories

| ID | Story | Status |
|---|---|---|
| — | No stories defined yet | — |
