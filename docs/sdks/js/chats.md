# Chats

This page documents chat-related SDK functions **individually**, including parameters and return objects.

---

## `createChat(chatName, userNames)`

Create a new chat with two or more users.

```ts
const chat = await whisp.createChat("Project Discussion", ["johndoe", "janedoe"]);
```

**Parameters**
- `chatName: string`
- `userNames: string[]` (must include your own username)

**Returns**

### ChatDetails

| Field | Type | Notes |
|---|---|---|
| `chatId` | string (uuid) |  |
| `chatName` | string |  |
| `lastMessageTimestamp` | string (date-time) |  |
| `lastMessage` | string |  |
| `createdAt` | string (date-time) |  |
| `creator` | string (uuid) |  |
| `groupChat` | boolean |  |

**Example**

```json
{
  "chatId": "550e8400-e29b-41d4-a716-446655440000",
  "chatName": "string",
  "lastMessageTimestamp": "2024-01-15T10:30:00Z",
  "lastMessage": "string",
  "createdAt": "2024-01-15T10:30:00Z",
  "creator": "550e8400-e29b-41d4-a716-446655440000",
  "groupChat": false
}
```


---

## `getChats(page?, size?)`

List chats for the authenticated user.

```ts
const result = await whisp.getChats(0, 20);
console.log(result.chats);
```

**Parameters**
- `page: number` (default `0`)
- `size: number` (default `20`)

**Returns**

### ChatsListResponse

| Field | Type | Required | Notes |
|---|---|---|---|
| `chats` | array[ChatDetails] | optional |  |

### ChatDetails

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | optional |  |
| `chatName` | string | optional |  |
| `lastMessageTimestamp` | string (date-time) | optional |  |
| `lastMessage` | string | optional |  |
| `createdAt` | string (date-time) | optional |  |
| `creator` | string (uuid) | optional |  |
| `groupChat` | boolean | optional |  |

**Example**

```json
{
  "chats": [
    {
      "chatId": "550e8400-e29b-41d4-a716-446655440000",
      "chatName": "string",
      "lastMessageTimestamp": "2024-01-15T10:30:00Z",
      "lastMessage": "string",
      "createdAt": "2024-01-15T10:30:00Z",
      "creator": "550e8400-e29b-41d4-a716-446655440000",
      "groupChat": false
    }
  ]
}
```


---

## `getChatUsers(chatId)`

List users in a chat.

```ts
const { users } = await whisp.getChatUsers(chatId);
```

**Parameters**
- `chatId: string` (uuid)

**Returns**

### ChatUsersResponse

| Field | Type | Notes |
|---|---|---|
| `users` | array[ChatUserInfo] |  |

### ChatUserInfo

| Field | Type | Notes |
|---|---|---|
| `userId` | string (uuid) |  |
| `username` | string |  |
| `joinedChatAt` | string (date-time) |  |
| `lastSeenMessage` | string (uuid) |  |

**Example**

```json
{
  "users": [
    {
      "userId": "550e8400-e29b-41d4-a716-446655440000",
      "username": "string",
      "joinedChatAt": "2024-01-15T10:30:00Z",
      "lastSeenMessage": "550e8400-e29b-41d4-a716-446655440000"
    }
  ]
}
```


---

## `addUserToChat(chatId, newUsername)`

Add a user to an existing chat.

```ts
await whisp.addUserToChat(chatId, "newuser");
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required |  |
| `newUsername` | string | required |  |

**Returns:** `void`

---

## `removeUserFromChat(chatId, removeUserId)`

Remove a user from a chat (leave or kick).

```ts
await whisp.removeUserFromChat(chatId, userId);
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required |  |
| `removeUser` | string (uuid) | required |  |

**Returns:** `void`

---

## `changeChatName(chatId, newChatName)`

Change the name of an existing chat.

```ts
await whisp.changeChatName(chatId, "Updated Name");
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required |  |
| `newChatName` | string | required |  |

**Returns:** `void`

---

## `deleteChat(chatId)`

Delete a chat (creator-only).

```ts
await whisp.deleteChat(chatId);
```

**Parameters**
- `chatId: string` (uuid)

**Returns:** `void`

---


