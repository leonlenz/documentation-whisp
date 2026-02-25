# Troubleshooting

## Realtime fails to connect

**Checklist**
1. Confirm the client is authenticated (you called `signIn(...)` or restored auth via `setAuth(...)`).
2. Call `await whisp.realtime.connect()` only after auth is set.
3. If you run realtime in **Node.js**, pass a WebSocket implementation (e.g. `ws`) as `webSocketImpl` when constructing the client.
4. If you are behind a corporate proxy / firewall, verify that WebSocket connections are allowed.

## “401” loops

- Refresh token expired or revoked → clear auth state and re-authenticate.

## Missing messages after reconnect

If the client was offline, you may have missed realtime events. After reconnect, backfill by calling:

- `getMessages(chatId, size, lastMessageId?)`

## Reaction deletion confusion

In realtime events:
- `REACT.messageId` is the **reaction ID** (used to delete)
- In REST history:
  - reaction id lives at `reactions[].reactionId`

## Multiple devices

A user may hold multiple simultaneous connections (e.g. phone + desktop). Design your UI to tolerate multiple connections and duplicate device presence.
