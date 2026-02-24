# Event Types

All events received via `whisp.realtime.on()`:

| Event | Type |
|---|---|
| `message` | `SendMsgEvent` |
| `messageEdited` | `EditMsgEvent` |
| `messageDeleted` | `DeleteMsgEvent` |
| `reply` | `ReplyEvent` |
| `typing` | `TypingEvent` |
| `reaction` | `ReactEvent` |
| `reactionDeleted` | `DeleteReactEvent` |
| `messageRead` | `ReadMsgEvent` |
| `userJoined` | `UserJoinEvent` |
| `userLeft` | `UserLeaveEvent` |
| `chatCreated` | `NewChatEvent` |
| `chatDeleted` | `DeleteChatEvent` |
| `connected` | `void` |
| `disconnected` | `{ reason, willReconnect }` |
| `reconnecting` | `{ attempt }` |
| `error` | `{ error }` |

## Unsubscribing from events

`on()` returns an unsubscribe function:

```ts
const unsubscribe = whisp.realtime.on("message", handler);
unsubscribe();
```

Or remove listeners:

```ts
whisp.realtime.removeAllListeners();
whisp.realtime.removeAllListeners("message");
```
