# auth test 4

## Vision

Just be pretty — just a test of the auth flow.

## Status

Initial app built. A polished post-login landing page replaces the placeholder
home page. Auth0 sign-in, callback, sealed-cookie session, and the standard
app shell (header, settings dialog, logout) are all exercised end-to-end.

## App Shell

The framework-provided shell handles the auth flow:

- `pages/login.vue` — Auth0 redirect with server-status gate.
- `pages/a0callback.vue` — exchanges the auth code, verifies tokens, and seals
  the session into a cookie.
- `pages/logout.vue` — clears session state and bounces back to login.
- `app.vue` — gates `AppHeader`, dialogs, and notifications behind a
  successful sign-in (`userName` from `useUserState()`).

## Modules

### Home (`pages/index.vue`)

The single user-facing page. Designed to be visually striking while staying
true to Lovelace branding:

- Animated "authenticated" status badge and check-ring hero.
- Personalized greeting using `userName` from `useUserState()`.
- Profile card with proxied Auth0 avatar (fallback to initials) and user id.
- 2×2 info grid showing identity provider, session state, access scope, and
  tenant — purely informational, confirms what the auth flow produced.
- Dark gradient background with green / blue radial highlights, FK Grotesk
  Mono typography, and subtle pulse animations.

No data fetching, no agents, no MCP servers — the page reads only what is
already in the Auth0 session and runtime config.
