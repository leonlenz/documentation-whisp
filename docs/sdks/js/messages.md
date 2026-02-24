# Messages

Whisp message history uses **cursor-based pagination**.

## Read messages

```ts
// Get messages (defaults: limit=50, from latest)
const { messages } = await whisp.getMessages(chatId);

// Load older messages
const { messages: older } = await whisp.getMessages(chatId, 50, lastMessageId);
```

## Efficient “last N messages” pattern

For typical chat UIs:

1. Load the newest page: `getMessages(chatId, 50)`
2. When the user scrolls up, request older messages using `lastMessageId`

:::scalar-callout{type="info"}
For realtime delivery (new messages, typing, reactions), use the Realtime API.
:::

::scalar-page-link{filepath="docs/sdks/js/realtime.md" title="Realtime" description="Connect, subscribe to events, send actions."}
