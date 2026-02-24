# Quickstart (Node.js)

On servers, it’s safe to use the API key. This is useful for background jobs, admin tools, or integration tests.

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY!,
  webSocketImpl: WebSocket,
});

await whisp.registerUser({
  username: "johndoe",
  email: "john.doe@example.com",
  password: "SecureP@ss123",
});

const user = await whisp.signIn({
  username: "johndoe",
  password: "SecureP@ss123",
});

const chats = await whisp.getChats(0, 20);
await whisp.realtime.connect();
```

:::scalar-callout{type="info"}
`getTicket()` returns a ticket valid for ~20 seconds. The SDK fetches a fresh ticket when connecting and reconnecting.
:::
