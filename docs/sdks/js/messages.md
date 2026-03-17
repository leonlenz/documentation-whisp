# Messages

This page documents message-related SDK functions **individually**, including parameters and return objects.

:::scalar-callout{type="info"}
The `getMessages` function automatically triggers a read on the last message in that chat.
:::

---

## `getMessages(chatId, size?, lastMessageId?)`

Fetch message history for a chat using cursor-based pagination.

```ts
const messages = await whisp.getMessages(chatId);
const olderMessages = await whisp.getMessages(chatId, 50, cursor);
```

**Parameters**
- `chatId: string` (uuid)
- `size?: number` (default `50`)
- `lastMessageId?: string` (uuid) — cursor for older messages

**Returns**

### MessagesResponse

| Field | Type | Notes |
|---|---|---|
| `messages` | array[Message] |  |

### Message

| Field | Type | Notes |
|---|---|---|
| `chatId` | string (uuid) |  |
| `timeStamp` | string (date-time) |  |
| `messageId` | string (uuid) |  |
| `senderId` | string (uuid) |  |
| `content` | string |  |
| `edited` | boolean |  |
| `editedAt` | string (date-time) or null |  |
| `replyTo` | object |  |
| `reactions` | array[Reaction] |  |

### Reaction

| Field | Type | Notes |
|---|---|---|
| `reactionId` | string (uuid) |  |
| `chatId` | string (uuid) |  |
| `messageId` | string (uuid) |  |
| `senderId` | string (uuid) |  |
| `reaction` | string |  |
| `createdAt` | string (date-time) |  |

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


