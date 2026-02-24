# Event Types & Payloads

To receive realtime events, the client subscribes to:

`/user/{userId}/queue/messages`

The SDK maps protocol `type` values to event names:

| SDK event name | Protocol `type` | Payload model |
|---|---|---|
| `message` | `SEND_MSG` | `SendMsgEvent` |
| `messageEdited` | `EDIT_MSG` | `EditMsgEvent` |
| `messageDeleted` | `DELETE_MSG` | `DeleteMsgEvent` |
| `reply` | `REPLY` | `ReplyEvent` |
| `typing` | `TYPING` | `TypingEvent` |
| `reaction` | `REACT` | `ReactEvent` |
| `reactionDeleted` | `DELETE_REACT` | `DeleteReactEvent` |
| `messageRead` | `READ_MSG` | `ReadMsgEvent` |
| `userJoined` | `USER_JOIN` | `UserJoinEvent` |
| `userLeft` | `USER_LEAVE` | `UserLeaveEvent` |
| `chatCreated` | `NEW_CHAT` | `NewChatEvent` |
| `chatDeleted` | `DELETE_CHAT` | `DeleteChatEvent` |

:::scalar-callout{type="info"}
All payloads include a `type` field (the protocol discriminator). Always branch on `type` if you implement the protocol without the SDK.
:::

## `message`

**Protocol `type`:** `SEND_MSG`  
**Payload model:** `SendMsgEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `timeStamp` | string (date-time) | optional |  |
| `chatId` | string (uuid) | optional |  |
| `message` | string | optional |  |
| `messageId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |

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

## `messageEdited`

**Protocol `type`:** `EDIT_MSG`  
**Payload model:** `EditMsgEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `message` | string | optional |  |
| `messageId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `editedAt` | string (date-time) | optional |  |

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

## `messageDeleted`

**Protocol `type`:** `DELETE_MSG`  
**Payload model:** `DeleteMsgEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `messageId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `timeStamp` | string (date-time) | optional |  |

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

## `reply`

**Protocol `type`:** `REPLY`  
**Payload model:** `ReplyEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `messageId` | string (uuid) | optional |  |
| `message` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `replyTo` | object | optional | Original message being replied to |
| `timeStamp` | string (date-time) | optional |  |

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

## `typing`

**Protocol `type`:** `TYPING`  
**Payload model:** `TypingEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |

**Example**

```json
{
  "type": "TYPING",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```

## `reaction`

**Protocol `type`:** `REACT`  
**Payload model:** `ReactEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `messageId` | string (uuid) | optional | Reaction ID (for deletion) |
| `message` | string | optional | Reaction emoji |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `parentMessageId` | string (uuid) | optional | Message that was reacted to |
| `timeStamp` | string (date-time) | optional |  |

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

## `reactionDeleted`

**Protocol `type`:** `DELETE_REACT`  
**Payload model:** `DeleteReactEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `messageId` | string (uuid) | optional | Reaction ID |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |

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

## `messageRead`

**Protocol `type`:** `READ_MSG`  
**Payload model:** `ReadMsgEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `messageId` | string (uuid) | optional |  |

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

## `userJoined`

**Protocol `type`:** `USER_JOIN`  
**Payload model:** `UserJoinEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `message` | string (uuid) | optional | New user's ID |
| `username` | string | optional | New user's username |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |

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

## `userLeft`

**Protocol `type`:** `USER_LEAVE`  
**Payload model:** `UserLeaveEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `message` | string | optional | Leave message |
| `recipientId` | string (uuid) | optional |  |
| `timeStamp` | string (date-time) | optional |  |
| `senderId` | string (uuid) | optional |  |

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

## `chatCreated`

**Protocol `type`:** `NEW_CHAT`  
**Payload model:** `NewChatEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `timeStamp` | string (date-time) | optional |  |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `message` | string | optional | Chat name |

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

## `chatDeleted`

**Protocol `type`:** `DELETE_CHAT`  
**Payload model:** `DeleteChatEvent`

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `chatId` | string (uuid) | optional |  |
| `recipientId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |

**Example**

```json
{
  "type": "DELETE_CHAT",
  "chatId": "660e8400-e29b-41d4-a716-446655440001",
  "recipientId": "550e8400-e29b-41d4-a716-446655440000",
  "senderId": "990e8400-e29b-41d4-a716-446655440004"
}
```
