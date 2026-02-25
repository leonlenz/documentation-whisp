# Realtime actions

This page documents the realtime **action methods** exposed by the SDK (for example sending a message or marking a message as read).

Under the hood these methods call `/api/chat` and will usually cause other participants to receive the corresponding realtime event (e.g. sending a message triggers the `message` event for members of the chat).

If you only need to *receive* updates, see [Realtime](/sdks/js/realtime).

---

## Action methods (send to `/api/chat`)

### `realtime.sendMessage(chatId, message)`

Send a new message to a chat.

#### SendMsgPayload

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

---

### `realtime.editMessage(chatId, messageId, message)`

Edit an existing message.

#### EditMsgPayload

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

---

### `realtime.deleteMessage(chatId, messageId)`

Delete a message.

#### DeleteMsgPayload

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

---

### `realtime.replyToMessage(chatId, messageId, message)`

Reply to a specific message.

#### ReplyPayload

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

---

### `realtime.sendTyping(chatId)`

Notify other participants that the current user is typing.

#### TypingPayload

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

---

### `realtime.addReaction(chatId, messageId, reaction)`

Add a reaction (emoji) to a message.

#### ReactPayload

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

---

### `realtime.removeReaction(reactionId)`

Remove a reaction by its id.

#### DeleteReactPayload

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
You can find `reactionId` on message history (`reactions[].reactionId`) or in the realtime `reaction` event (`messageId`).
:::

---

### `realtime.markAsRead(chatId, messageId)`

Mark a message as read.

#### ReadMsgPayload

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
