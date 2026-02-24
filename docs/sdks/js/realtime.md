# Realtime

## Connect

```ts
await whisp.realtime.connect();
```

## Lifecycle events

```ts
whisp.realtime.on("connected", () => console.log("Connected!"));

whisp.realtime.on("disconnected", ({ reason, willReconnect }) => {
  console.log(`Disconnected: ${reason}. Will reconnect: ${willReconnect}`);
});

whisp.realtime.on("reconnecting", ({ attempt }) => {
  console.log(`Reconnecting... attempt ${attempt}`);
});
```

## Receive events

```ts
whisp.realtime.on("message", (event) => {
  console.log(`${event.senderId}: ${event.message}`);
});

whisp.realtime.on("typing", (event) => {
  console.log(`User ${event.senderId} is typing in ${event.chatId}`);
});
```

## Send actions

```ts
whisp.realtime.sendMessage(chatId, "Hello!");
whisp.realtime.editMessage(chatId, messageId, "Hello! (edited)");
whisp.realtime.deleteMessage(chatId, messageId);
whisp.realtime.replyToMessage(chatId, messageId, "I agree!");
whisp.realtime.sendTyping(chatId);
whisp.realtime.addReaction(chatId, messageId, "👍");
whisp.realtime.removeReaction(reactionId);
whisp.realtime.markAsRead(chatId, messageId);
```

## Connection status + disconnect

```ts
whisp.realtime.connected; // boolean
whisp.realtime.disconnect();
```

## Automatic reconnection

If the WebSocket connection drops, the SDK automatically:

1. Refreshes the JWT if needed
2. Gets a new WebSocket ticket
3. Reconnects with exponential backoff (1s → 2s → 4s → ... → 30s max)

Listen to `disconnected`, `reconnecting`, and `connected` to keep your UI state correct.
