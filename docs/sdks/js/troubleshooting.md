# Troubleshooting

## Realtime does not connect (Node.js)

- Ensure you installed `ws`
- Ensure you passed `webSocketImpl: WebSocket` to the client

## “401” loops

- Your refresh token is expired/revoked.
- Clear auth state and re-authenticate via your backend, then call `setAuth()` again.

## Missing events

- Confirm you did not unsubscribe your handlers
- Confirm you’re connected: `whisp.realtime.connected === true`
