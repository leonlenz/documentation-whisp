# Realtime

Realtime keeps your UI in sync by delivering events (new messages, edits, typing indicators, reactions, member changes, …) as soon as they happen.

:::scalar-callout{type="info"}
Prerequisite: the client must be authenticated (JWT set via `signIn(...)` or `setAuth(...)`). See [Authentication](/sdks/js/authentication).
:::

Events are sent to all connected user even the sender. This means that when a user sends a message and recieves it back over the realtime connection the UI can mark it as "persisted in the backend / delivered".

---

## Connect / Disconnect

### `realtime.connect()`

Connect and start receiving events.

```ts
await whisp.realtime.connect();
```

Typical lifecycle:
- Connect after login (or app startup if you restore auth).
- Disconnect on logout.
- In UI frameworks, clean up event listeners when your chat screen unmounts.

---

### `realtime.disconnect()`

Disconnect and stop receiving realtime events.

```ts
whisp.realtime.disconnect();
```

---

## Listening to events

Use `whisp.realtime.on(eventName, handler)` to subscribe to events. The method returns an `unsubscribe()` function.

```ts
const unsubscribe = whisp.realtime.on("message", (event) => {
  console.log(event.chatId, event.message);
});

// later
unsubscribe();
```

If you want to remove everything at once (e.g. on logout), you can also use:

```ts
whisp.realtime.removeAllListeners();
```

### Event reference

Below are all currently supported SDK event names.

---

## New message

- **SDK event:** `message`
- **Description:** Fired when a new message is sent in a chat the current user is a member of.

```ts
const unsubscribe = whisp.realtime.on("message", (event) => {
  console.log("new message", event.chatId, event.messageId, event.message);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `SEND_MSG`. |
| `timeStamp` | string (date-time) | When the message was created. |
| `chatId` | string (uuid) | Chat the message belongs to. |
| `message` | string | Message text/content. |
| `messageId` | string (uuid) | Unique message id. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | Sender user id. |

**Example**

```json
{
  "type": "SEND_MSG",
  "timeStamp": "2024-01-15T10:30:00Z",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "message": "Hello, world!",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

---

## Message edited

- **SDK event:** `messageEdited`
- **Description:** Fired when an existing message is edited.

```ts
const unsubscribe = whisp.realtime.on("messageEdited", (event) => {
  console.log("message edited", event.chatId, event.messageId, event.message);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `EDIT_MSG`. |
| `chatId` | string (uuid) | Chat the message belongs to. |
| `message` | string | Updated message content. |
| `messageId` | string (uuid) | Edited message id. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who edited the message. |
| `editedAt` | string (date-time) | When the edit happened. |

**Example**

```json
{
  "type": "EDIT_MSG",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "message": "Hello, world! (edited)",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "editedAt": "2024-01-15T10:35:00Z"
}
```

---

## Message deleted

- **SDK event:** `messageDeleted`
- **Description:** Fired when a message is deleted.

```ts
const unsubscribe = whisp.realtime.on("messageDeleted", (event) => {
  console.log("message deleted", event.chatId, event.messageId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `DELETE_MSG`. |
| `chatId` | string (uuid) | Chat the deleted message belonged to. |
| `messageId` | string (uuid) | Deleted message id. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who deleted the message. |
| `timeStamp` | string (date-time) | When deletion happened. |

**Example**

```json
{
  "type": "DELETE_MSG",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "messageId": "770e8400-e29b-41d4-a716-446655440002",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "timeStamp": "2024-01-15T10:40:00Z"
}
```

---

## Reply to a message

- **SDK event:** `reply`
- **Description:** Fired when a user replies to a specific message.

```ts
const unsubscribe = whisp.realtime.on("reply", (event) => {
  console.log("reply", event.chatId, event.messageId, event.replyTo?.messageId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `REPLY`. |
| `messageId` | string (uuid) | The reply message id. |
| `message` | string | Reply content. |
| `chatId` | string (uuid) | Chat where the reply happened. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who replied. |
| `replyTo` | object | Snapshot of the original message. |
| `timeStamp` | string (date-time) | When the reply was created. |

**Example**

```json
{
  "type": "REPLY",
  "messageId": "bb0e8400-e29b-41d4-a716-446655440006",
  "message": "I agree!",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "replyTo": {
    "chatId": "660e8400-e29b-41d4-a716-446655440001",
    "timeStamp": "2024-01-15T10:30:00Z",
    "messageId": "770e8400-e29b-41d4-a716-446655440002",
    "senderId": "550e8400-e29b-41d4-a716-446655440000",
    "content": "Hello, world!",
    "edited": false,
    "editedAt": null
  },
  "timeStamp": "2024-01-15T10:33:00Z"
}
```

---

## Typing indicator

- **SDK event:** `typing`
- **Description:** Fired when a chat participant starts typing.

```ts
const unsubscribe = whisp.realtime.on("typing", (event) => {
  console.log("typing", event.chatId, event.senderId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `TYPING`. |
| `chatId` | string (uuid) | Chat where typing happened. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who is typing. |

**Example**

```json
{
  "type": "TYPING",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

---

## Reaction added

- **SDK event:** `reaction`
- **Description:** Fired when a reaction (emoji) is added to a message.

```ts
const unsubscribe = whisp.realtime.on("reaction", (event) => {
  console.log("reaction", event.chatId, event.parentMessageId, event.message);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `REACT`. |
| `messageId` | string (uuid) | **Reaction id** (use it to delete the reaction). |
| `message` | string | Reaction emoji. |
| `chatId` | string (uuid) | Chat where the reaction happened. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who reacted. |
| `parentMessageId` | string (uuid) | Message id that was reacted to. |
| `timeStamp` | string (date-time) | When the reaction was created. |

**Example**

```json
{
  "type": "REACT",
  "messageId": "880e8400-e29b-41d4-a716-446655440003",
  "message": "\ud83d\udc4d",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "parentMessageId": "770e8400-e29b-41d4-a716-446655440002",
  "timeStamp": "2024-01-15T10:32:00Z"
}
```

---

## Reaction removed

- **SDK event:** `reactionDeleted`
- **Description:** Fired when a reaction is removed.

```ts
const unsubscribe = whisp.realtime.on("reactionDeleted", (event) => {
  console.log("reaction deleted", event.chatId, event.messageId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `DELETE_REACT`. |
| `messageId` | string (uuid) | Reaction id that was removed. |
| `chatId` | string (uuid) | Chat where the reaction was removed. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who removed the reaction. |

**Example**

```json
{
  "type": "DELETE_REACT",
  "messageId": "880e8400-e29b-41d4-a716-446655440003",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

:::scalar-callout{type="info"}
You can find `reactionId` on message history (`reactions[].reactionId`) or in the realtime `reaction` event (`messageId`).
:::

---

## Read receipt

- **SDK event:** `messageRead`
- **Description:** Fired when a user marks a message as read.

```ts
const unsubscribe = whisp.realtime.on("messageRead", (event) => {
  console.log("read receipt", event.chatId, event.messageId, event.senderId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `READ_MSG`. |
| `chatId` | string (uuid) | Chat where the read happened. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who read the message. |
| `messageId` | string (uuid) | Last read message id (for that read action). |

**Example**

```json
{
  "type": "READ_MSG",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "messageId": "770e8400-e29b-41d4-a716-446655440002"
}
```

---

## Member joined

- **SDK event:** `userJoined`
- **Description:** Fired when a user is added to a chat.

```ts
const unsubscribe = whisp.realtime.on("userJoined", (event) => {
  console.log("user joined", event.chatId, event.message, event.username);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `USER_JOIN`. |
| `chatId` | string (uuid) | Chat that was joined. |
| `message` | string (uuid) | New user’s id. |
| `username` | string | New user’s username. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who triggered the join (e.g. inviter). |

**Example**

```json
{
  "type": "USER_JOIN",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "message": "aa0e8400-e29b-41d4-a716-446655440005",
  "username": "newuser",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

---

## Member left

- **SDK event:** `userLeft`
- **Description:** Fired when a user leaves (or is removed from) a chat.

```ts
const unsubscribe = whisp.realtime.on("userLeft", (event) => {
  console.log("user left", event.chatId, event.senderId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `USER_LEAVE`. |
| `chatId` | string (uuid) | Chat that was left. |
| `message` | string | Leave message (human-readable). |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `timeStamp` | string (date-time) | When the leave happened. |
| `senderId` | string (uuid) | User who left (or was removed). |

**Example**

```json
{
  "type": "USER_LEAVE",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "message": "johndoe left the Chat",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "timeStamp": "2024-01-15T11:00:00Z",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

---

## New chat

- **SDK event:** `chatCreated`
- **Description:** Fired when a new chat is created that the current user is a member of.

```ts
const unsubscribe = whisp.realtime.on("chatCreated", (event) => {
  console.log("chat created", event.chatId, event.message);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `NEW_CHAT`. |
| `timeStamp` | string (date-time) | When the chat was created. |
| `chatId` | string (uuid) | New chat id. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who created the chat. |
| `message` | string | Chat name. |

**Example**

```json
{
  "type": "NEW_CHAT",
  "timeStamp": "2024-01-15T09:00:00Z",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004",
  "message": "Project Discussion"
}
```

---

## Chat deleted

- **SDK event:** `chatDeleted`
- **Description:** Fired when a chat is deleted.

```ts
const unsubscribe = whisp.realtime.on("chatDeleted", (event) => {
  console.log("chat deleted", event.chatId);
});
```

**Event object**

| Field | Type | Notes |
|---|---|---|
| `type` | string | Event type. Here it is `DELETE_CHAT`. |
| `chatId` | string (uuid) | Deleted chat id. |
| `recipientId` | string (uuid) | ID of current user. Can be ignored. |
| `senderId` | string (uuid) | User who deleted the chat. |

**Example**

```json
{
  "type": "DELETE_CHAT",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

---

## Reconnection & backfill

Realtime is for *immediate* updates. If your client was offline, you may need to backfill with REST after reconnect.

After you reconnect, load the latest messages again (or load older messages using a cursor):

```ts
// latest messages
const { messages } = await whisp.getMessages(chatId, 50);

// older messages (cursor-based pagination)
const older = await whisp.getMessages(chatId, 50, oldestMessageId);
```
