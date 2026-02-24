# Quickstart (Browser)

This is the recommended production flow for web apps.

:::scalar-callout{type="warning"}
Do **not** call `signIn` or `registerUser` from browser code. Those require an API key.
:::

## Flow

1. Your backend calls Whisp `signIn` using the API key
2. Backend returns `{ jwt, refreshToken, userId }` to the browser
3. Browser initializes the SDK via `setAuth()`

## Example: browser app

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://yourapp.api.whispchat.com",
});

// After your backend authenticates and returns tokens:
whisp.setAuth({
  jwt: "eyJhbG...",
  refreshToken: "dGhpcyBpcyBh...",
  userId: "550e8400-e29b-41d4-a716-446655440000",
});

const { chats } = await whisp.getChats(0, 20);
console.log(chats);

await whisp.realtime.connect();
```

## Example: backend token exchange (Node/Express)

```ts
import express from "express";
import { WhispClient } from "whisp-sdk";

const app = express();
app.use(express.json());

const whisp = new WhispClient({
  baseUrl: "https://yourapp.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY!,
});

app.post("/auth/whisp/sign-in", async (req, res) => {
  const { username, password } = req.body;

  const user = await whisp.signIn({ username, password });

  // Return only what the browser needs:
  const auth = whisp.getAuth();
  return res.json({ user, ...auth });
});
```

:::scalar-callout{type="info"}
Prefer storing tokens in secure, httpOnly cookies via your backend when possible.
:::

## Next

::scalar-page-link{filepath="docs/sdks/js/authentication.md" title="Authentication" description="Token refresh and auth state"}
::scalar-page-link{filepath="docs/sdks/js/realtime.md" title="Realtime" description="Events and actions"}
