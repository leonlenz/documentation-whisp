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
| `message` | string | required | The content of the message. |
| `chatId` | string (uuid) | required | The ID of the Chat the message is being sent to. |



---

### `realtime.editMessage(chatId, messageId, message)`

Edit an existing message.

#### EditMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `message` | string | required | New edited message. |
| `messageId` | string (uuid) | required | The ID of the message being edited. |
| `chatId` | string (uuid) | required | The chat ID the message is in. |



---

### `realtime.deleteMessage(chatId, messageId)`

Delete a message.

#### DeleteMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required | The chat ID the message is in. |
| `messageId` | string (uuid) | required | The ID of the message to be deleted. |



---

### `realtime.replyToMessage(chatId, messageId, message)`

Reply to a specific message.

#### ReplyPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required | The chat ID the message is in. |
| `messageId` | string (uuid) | required | ID of message being replied to. |
| `message` | string | required | The contents of the reply. |



---

### `realtime.sendTyping(chatId)`

Notify other participants that the current user is typing.

#### TypingPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required | ID of the chat being typed in. |



---

### `realtime.addReaction(chatId, messageId, reaction)`

Add a reaction (emoji) to a message.

#### ReactPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required | ID of the chat the message is in. |
| `messageId` | string (uuid) | required | ID of the message being reacted to. |
| `reaction` | string | required | The emoji reaction. |



---

### `realtime.removeReaction(reactionId)`

Remove a reaction by its id.

#### DeleteReactPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `reactionId` | string (uuid) | required | ID of the reaction to be deleted. |



:::scalar-callout{type="info"}
You can find `reactionId` on message history (`reactions[].reactionId`) or in the realtime `reaction` event (`messageId`).
:::

---

### `realtime.markAsRead(chatId, messageId)`

Mark a message as read. This will also mark all messages before this as read.

#### ReadMsgPayload

| Field | Type | Required | Notes |
|---|---|---|---|
| `chatId` | string (uuid) | required | The chat ID that the message is in. |
| `messageId` | string (uuid) | required | ID of the message to be marked as read. |

