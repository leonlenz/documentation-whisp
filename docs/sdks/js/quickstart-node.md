# Quickstart (Node.js)

On servers, it’s safe to use the API key.

## Example

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://yourapp.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY!,
  webSocketImpl: WebSocket,
});

// Register a user (API key required)
await whisp.registerUser({
  username: "johndoe",
  email: "john@example.com",
  password: "SecureP@ss123",
});

// Sign in (stores tokens internally)
const user = await whisp.signIn({
  username: "johndoe",
  password: "SecureP@ss123",
});

console.log(`Signed in as ${user.username} (${user.id})`);

const { chats } = await whisp.getChats(0, 20);
console.log(chats);

await whisp.realtime.connect();
```

:::scalar-callout{type="info"}
In Node.js you must pass `webSocketImpl` (e.g. `ws`) for realtime.
:::
