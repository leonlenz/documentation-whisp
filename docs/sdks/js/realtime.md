# Realtime

Whisp realtime uses **STOMP over WebSocket (SockJS)**. A connection requires a short-lived **ticket** from `GET /api/user/getTicket`.

If you use the JS/TS SDK, you typically work with these functions:

- `connect()`
- `disconnect()`
- `on(event, handler)`
- `removeAllListeners(...)`
- action methods such as `sendMessage`, `editMessage`, etc.

---

## `realtime.connect()`

Connect and start receiving events.

**Protocol flow**
1. SDK calls `getTicket()` (REST)
2. SDK opens SockJS: `https://<clientDomain>.api.whispchat.com/api/wsConnect?ticket=<ticket>`
3. SDK connects via STOMP and sends JWT as **STOMP header**: `Authorization: Bearer <JWT>`
4. SDK subscribes to: `/user/<userId>/queue/messages`

:::scalar-callout{type="warning"}
The JWT must be sent as a **STOMP header during connect**, not as an HTTP header.
:::

```ts
await whisp.realtime.connect();
```

---

## `realtime.disconnect()`

```ts
whisp.realtime.disconnect();
```

---

## `realtime.on(eventName, handler)`

```ts
const unsubscribe = whisp.realtime.on("message", (event) => {
  console.log(event.chatId, event.message);
});
unsubscribe();
```

Event payload shapes are documented on [Event Types](/sdks/js/events).

---

## Action methods (send to `/api/chat`)

### `realtime.sendMessage(chatId, message)`

### SendMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `message` | string | required |  |
| `timeStamp` | string (date-time) | required |  |
| `chatId` | string (uuid) | required |  |

**Example**

```json
{
  "type": "SEND_MSG",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "message": "Hello, world!",
  "timeStamp": "2024-01-15T10:30:00Z",
  "chatId": "660e8400-e29b-41d4-a716-446655440001"
}
```


### `realtime.editMessage(chatId, messageId, message)`

### EditMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `message` | string | required |  |
| `messageId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |
| `timeStamp` | string (date-time) | required |  |

**Example**

```json
{
  "type": "EDIT_MSG",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "message": "Hello, world! (edited)",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "timeStamp": "2024-01-15T10:35:00Z"
}
```


### `realtime.deleteMessage(chatId, messageId)`

### DeleteMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |
| `messageId` | string (uuid) | required |  |

**Example**

```json
{
  "type": "DELETE_MSG",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "messageId": "770e8400-e29b-41d4-a716-446655440002"
}
```


### `realtime.replyToMessage(chatId, messageId, message)`

### ReplyPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |
| `messageId` | string (uuid) | required | ID of message being replied to |
| `message` | string | required |  |
| `timeStamp` | string (date-time) | required |  |

**Example**

```json
{
  "type": "REPLY",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "message": "I agree with this!",
  "timeStamp": "2024-01-15T10:33:00Z"
}
```


### `realtime.sendTyping(chatId)`

### TypingPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |

**Example**

```json
{
  "type": "TYPING",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "chatId": "660e8400-e29b-41d4-a716-446655440001"
}
```


### `realtime.addReaction(chatId, messageId, reaction)`

### ReactPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |
| `messageId` | string (uuid) | required |  |
| `reaction` | string | required |  |
| `timeStamp` | string (date-time) | required |  |

**Example**

```json
{
  "type": "REACT",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "reaction": "\ud83d\udc4d",
  "timeStamp": "2024-01-15T10:32:00Z"
}
```


### `realtime.removeReaction(reactionId)`

### DeleteReactPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `reactionId` | string (uuid) | required |  |

**Example**

```json
{
  "type": "DELETE_REACT",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "reactionId": "880e8400-e29b-41d4-a716-446655440003"
}
```


:::scalar-callout{type="info"}
You can find `reactionId` on message history (`reactions[].reactionId`) or in the realtime `REACT` event.
:::

### `realtime.markAsRead(chatId, messageId)`

### ReadMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | required |  |
| `senderId` | string (uuid) | required |  |
| `chatId` | string (uuid) | required |  |
| `messageId` | string (uuid) | required |  |

**Example**

```json
{
  "type": "READ_MSG",
  "senderId": "550e8400-e29b-41d4-a716-446655440000",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "messageId": "770e8400-e29b-41d4-a716-446655440002"
}
```


---

## Reconnection

If disconnected, reconnect requires a **new ticket** and re-subscription. After reconnect, backfill by calling REST `getMessages`.
