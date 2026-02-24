# Quickstart (Browser)

This is the recommended production flow for web apps.

:::scalar-callout{type="warning"}
Do **not** call `signIn` or `registerUser` from browser code. Those require an API key (`x-api-key`).
:::

## Flow

1. Your backend signs the user in (`POST /api/user/signin`) using `x-api-key`
2. Backend returns `{jwt, refreshToken, userId}` to the browser
3. Browser initializes the SDK via `setAuth()`

## Browser code

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
});

whisp.setAuth({
  jwt: "eyJhbGciOiJIUzI1NiJ9...",
  refreshToken: "dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...",
  userId: "550e8400-e29b-41d4-a716-446655440000",
});

const chats = await whisp.getChats(0, 20);
console.log(chats.chats);
```

## Backend example (Node/Express)

The sign-in endpoint returns the JWT in the **`Authorization` response header** and the refresh token in the **JSON body**.

```ts
import express from "express";
import fetch from "node-fetch";

const app = express();
app.use(express.json());

app.post("/auth/whisp/sign-in", async (req, res) => {
  const { username, password } = req.body;

  const r = await fetch("https://demo.api.whispchat.com/api/user/signin", {
    method: "POST",
    headers: {
      "content-type": "application/json",
      "x-api-key": process.env.WHISP_API_KEY!,
    },
    body: JSON.stringify({ username, password }),
  });

  if (!r.ok) {
    return res.status(r.status).json(await r.json().catch(() => ({})));
  }

  const body = await r.json(); // SignInResponse (contains refreshToken, roles, etc.)
  const authHeader = r.headers.get("authorization"); // "Bearer <JWT>"
  const jwt = authHeader?.startsWith("Bearer ") ? authHeader.slice("Bearer ".length) : authHeader;

  return res.json({
    userId: body.id,
    refreshToken: body.refreshToken,
    jwt,
  });
});
```

## Next

- [Authentication](/sdks/js/authentication)
- [Chats](/sdks/js/chats)
- [Realtime](/sdks/js/realtime)
