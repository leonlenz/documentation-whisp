# Messages

This page documents message-related SDK functions **individually**, including parameters and return objects.

:::scalar-callout{type="info"}
The `getMessages` function automatically triggers a read on the last message in that chat.
:::

---

## `getMessages(chatId, size?, lastMessageId?)`

Fetch message history for a chat using cursor-based pagination.

**REST mapping:** `GET /api/messages/getMessages/<chatId>?size=<size>&lastMessage=<cursor>`

```ts
const result = await whisp.getMessages(chatId);
const older = await whisp.getMessages(chatId, 50, cursor);
```

**Parameters**
- `chatId: string` (uuid)
- `size?: number` (default `50`)
- `lastMessageId?: string` (uuid) — cursor for older messages

**Returns**

### MessagesResponse

| Field | Type | Required | Notes |
|---|---|---|---|
| `messages` | array[Message] | optional |  |

### Message

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | optional |  |
| `timeStamp` | string (date-time) | optional |  |
| `messageId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `content` | string | optional |  |
| `edited` | boolean | optional |  |
| `editedAt` | string (date-time) | null | optional |  |
| `replyTo` | object | optional |  |
| `reactions` | array[Reaction] | optional |  |

### Reaction

| Field | Type | Required | Notes |
|---|---|---|---|
| `reactionId` | string (uuid) | optional |  |
| `chatId` | string (uuid) | optional |  |
| `messageId` | string (uuid) | optional |  |
| `senderId` | string (uuid) | optional |  |
| `reaction` | string | optional |  |
| `createdAt` | string (date-time) | optional |  |

**Example**

```json
{
  "messages": [
    {
      "chatId": "550e8400-e29b-41d4-a716-446655440000",
      "timeStamp": "2024-01-15T10:30:00Z",
      "messageId": "550e8400-e29b-41d4-a716-446655440000",
      "senderId": "550e8400-e29b-41d4-a716-446655440000",
      "content": "string",
      "edited": false,
      "editedAt": "2024-01-15T10:30:00Z",
      "replyTo": {
        "chatId": "550e8400-e29b-41d4-a716-446655440000",
        "timeStamp": "2024-01-15T10:30:00Z",
        "messageId": "550e8400-e29b-41d4-a716-446655440000",
        "senderId": "550e8400-e29b-41d4-a716-446655440000",
        "content": "string",
        "edited": false,
        "editedAt": "2024-01-15T10:30:00Z"
      },
      "reactions": [
        {
          "reactionId": "550e8400-e29b-41d4-a716-446655440000",
          "chatId": "550e8400-e29b-41d4-a716-446655440000",
          "messageId": "550e8400-e29b-41d4-a716-446655440000",
          "senderId": "550e8400-e29b-41d4-a716-446655440000",
          "reaction": "string",
          "createdAt": "2024-01-15T10:30:00Z"
        }
      ]
    }
  ]
}
```


---

## Pagination pattern (“load older on scroll”)

1. Initial load: `getMessages(chatId, 50)`
2. Keep the oldest message’s `messageId` as `cursor`
3. When the user scrolls up: `getMessages(chatId, 50, cursor)`


