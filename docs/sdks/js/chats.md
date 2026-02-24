# Chats

## Create, list, and manage chats

```ts
// Create a chat (include your own username in the list)
const chat = await whisp.createChat("Project Discussion", ["johndoe", "janedoe"]);

// List chats (paginated)
const { chats } = await whisp.getChats(0, 20);

// Get users in a chat
const { users } = await whisp.getChatUsers(chat.chatId);

// Add/remove users
await whisp.addUserToChat(chat.chatId, "newuser");
await whisp.removeUserFromChat(chat.chatId, userId);

// Update chat
await whisp.changeChatName(chat.chatId, "New Name");
await whisp.deleteChat(chat.chatId);
```

:::scalar-callout{type="info"}
For detailed request/response schemas and edge cases, see the REST API reference.
:::

::scalar-page-link{filepath="docs/api-reference/openapi.yaml" title="REST API Reference" description="Full endpoint reference generated from OpenAPI."}
