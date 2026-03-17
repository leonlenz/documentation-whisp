# Getting Started

## Base URL

Whisp uses a client-specific base URL:

`https://{clientDomain}.api.whispchat.com`

Example:

`https://demo.api.whispchat.com`

## Authentication model (high level)

- **Server-only (API key):** `registerUser`, `signIn`
- **Client (JWT):** all other REST endpoints require `Authorization: Bearer <JWT>`
- **Refresh:** JWTs are refreshed using `/api/auth/refresh` with a refresh token

:::scalar-callout{type="info"}
For browser apps, the recommended setup is: **backend authenticates with API key → browser receives JWT + refreshToken → browser calls `setAuth()`**.
:::

## Real-time messaging

Whisp realtime uses **STOMP over WebSocket (SockJS)** and requires a short-lived **ticket** from `GET /api/auth/getTicket`.

If you use the JS/TS SDK, the realtime layer is wrapped behind `whisp.realtime.*`.

## Next steps

- [JS/TS SDK Overview](/sdks/js)
- [Quickstart (Browser)](/sdks/js/quickstart/browser)
- [REST API Reference](/api)
