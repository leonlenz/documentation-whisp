# Troubleshooting

## Realtime fails to connect

**Checklist**
1. Fetch a ticket immediately before connecting (`getTicket`)
2. Connect to: `https://<clientDomain>.api.whispchat.com/api/wsConnect?ticket=<ticket>`
3. Pass JWT as a **STOMP** header (`Authorization: Bearer <JWT>`) — not an HTTP header

## “401” loops

- Refresh token expired or revoked → clear auth state and re-authenticate.

## Missing messages after reconnect

The realtime protocol does not guarantee re-delivery of all missed events across reconnects. After reconnect, backfill by calling:

- `getMessages(chatId, size, lastMessageId?)`

## Reaction deletion confusion

In realtime events:
- `REACT.messageId` is the **reaction ID** (used to delete)
- In REST history:
  - reaction id lives at `reactions[].reactionId`

## Multiple devices

A user may hold multiple simultaneous connections (e.g. phone + desktop). Design your UI to tolerate multiple connections and duplicate device presence.
