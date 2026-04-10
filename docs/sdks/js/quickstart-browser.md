# Quickstart (Browser)

This is the **recommended production flow for any web app** — React, Vue, Svelte, Next.js, or plain HTML. By the end of this guide you’ll have a browser page that can list chats, receive messages in real time, and send a message back — without ever exposing your API key.

:::scalar-callout{type="warning"}
Do **not** call `signIn()` or `registerUser()` from browser code. Those endpoints require the `x-api-key` header, and shipping your API key in a browser bundle is equivalent to publishing it on GitHub. A bad actor could then register/sign in as anyone in your app.
:::

## The big picture

The recommended flow has three actors: your **frontend** (browser), your **backend** (Node, PHP, Go, whatever), and the **Whisp API**.

```
┌──────────┐   1. username/password    ┌──────────┐   2. x-api-key +        ┌──────────┐
│          │ ────────────────────────► │          │ ──── credentials ─────► │          │
│  Browser │                           │ Your API │                         │ Whisp API│
│          │ ◄──────────────────────── │          │ ◄── jwt + refreshToken ─│          │
└──────────┘   4. jwt + refreshToken   └──────────┘   3. sign-in response   └──────────┘
      │
      │  5. whisp.setAuth({ jwt, refreshToken, userId })
      ▼
┌──────────┐
│ whisp-sdk│   6. whisp.realtime.connect()
│ (browser)│   7. whisp.getChats(), whisp.realtime.on("message", …), …
└──────────┘
```

1. User enters username/password in your frontend
2. Your backend forwards the credentials to Whisp, adding the `x-api-key` header
3. Whisp returns a JWT (in the `Authorization` response header) and a refresh token (in the JSON body)
4. Your backend forwards those tokens to the browser
5. The browser hands them to the SDK via `whisp.setAuth(...)`
6. From here on, the browser talks to Whisp **directly** using the JWT — your backend is out of the hot path

:::scalar-callout{type="info"}
Why not proxy every request through your backend? Because the realtime WebSocket needs a direct browser-to-Whisp connection — and once the JWT is set, REST calls work just as well directly. Your backend only sits in front of the sign-in/sign-up step, which is exactly where you want it.
:::

---

## Step 1 — Add a sign-in endpoint to your backend

This endpoint takes username/password from the browser, forwards them to Whisp with your API key, and sends the resulting tokens back to the browser. This is the **only** Whisp call your backend has to make.

```ts
// Node.js + Express example — same idea applies to any backend
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

  // Whisp returns the JWT in the Authorization header, not the body.
  const body = await r.json(); // { id, username, email, refreshToken, roles }
  const authHeader = r.headers.get("authorization"); // "Bearer <JWT>"
  const jwt = authHeader?.startsWith("Bearer ") ? authHeader.slice(7) : authHeader;

  return res.json({
    userId: body.id,
    refreshToken: body.refreshToken,
    jwt,
  });
});
```

:::scalar-callout{type="info"}
**Tip:** you can also set these tokens as `httpOnly` cookies from your backend instead of returning them in the JSON body — that’s a bit safer against XSS. The SDK doesn’t care where they come from; you just need to end up calling `whisp.setAuth(...)` with the values.
:::

---

## Step 2 — Initialize the SDK in the browser

```ts
// src/whisp.ts — create this once, import it everywhere
import { WhispClient } from "whisp-sdk";

export const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
});
```

Notice there’s **no `apiKey`** here. That’s the entire point — the browser should never see it.

---

## Step 3 — Sign the user in

When the user submits your login form, call the endpoint you added to **your own backend** in Step 1 (not Whisp directly — that would require the API key). Your backend talks to Whisp, gets the tokens, and returns them here. Then you hand those tokens to the SDK via `setAuth()`:

```ts
import { whisp } from "./whisp";

async function handleLogin(username: string, password: string) {
  // `/auth/whisp/sign-in` is a route on *your* backend from Step 1 —
  // it's the one that knows the API key. The browser never calls
  // Whisp's /api/user/signin directly.
  const r = await fetch("/auth/whisp/sign-in", {
    method: "POST",
    headers: { "content-type": "application/json" },
    body: JSON.stringify({ username, password }),
  });

  if (!r.ok) throw new Error("Login failed");

  const { jwt, refreshToken, userId } = await r.json();

  whisp.setAuth({ jwt, refreshToken, userId });
}
```

From this point on, `whisp.isAuthenticated === true` and every SDK call will include the JWT automatically. If the JWT expires, the SDK refreshes it in the background using the refresh token — **you never need to handle 401s manually**.

---

## Step 4 — Load the user’s chats

```ts
const { chats } = await whisp.getChats(0, 20);
console.log(`You're in ${chats.length} chats`);
for (const chat of chats) {
  console.log(`- ${chat.chatName} (last: ${chat.lastMessage ?? "∅"})`);
}
```

---

## Step 5 — Connect realtime and listen for messages

```ts
await whisp.realtime.connect();

whisp.realtime.on("connected", () => console.log("realtime live"));
whisp.realtime.on("disconnected", ({ willReconnect }) => {
  console.log("realtime dropped", willReconnect ? "(will retry)" : "");
});

whisp.realtime.on("message", (event) => {
  console.log(`[${event.chatId}] ${event.senderId}: ${event.message}`);
  // update your UI state here
});

whisp.realtime.on("typing", (event) => {
  console.log(`user ${event.senderId} is typing in ${event.chatId}`);
});
```

The SDK handles the STOMP handshake, ticket fetching, and reconnect-with-backoff for you. If the connection drops, you’ll get a `disconnected` event followed (once it recovers) by a `connected` event.

---

## Step 6 — Send a message

```ts
whisp.realtime.sendMessage(chatId, "Hello, world!");
```

Whisp echoes every message back to the sender over the realtime channel, so your UI doesn’t need to optimistically insert the message — just wait for the `message` event and you’ll see it. (This also means you get a free “delivered” confirmation from the server.)

---

## Step 7 — Clean up on sign-out

```ts
async function handleLogout() {
  await whisp.logout();           // invalidates the refresh token on the server
  whisp.realtime.disconnect();    // closes the WebSocket (logout also does this)
}
```

---

## Putting it all together — a minimal React example

```tsx
import { useEffect, useState } from "react";
import { WhispClient, type SendMsgEvent } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
});

export function ChatPage({ chatId }: { chatId: string }) {
  const [messages, setMessages] = useState<SendMsgEvent[]>([]);
  const [input, setInput] = useState("");

  useEffect(() => {
    // assume setAuth was called right after login somewhere else
    whisp.realtime.connect();
    const off = whisp.realtime.on("message", (evt) => {
      if (evt.chatId === chatId) setMessages((m) => [...m, evt]);
    });
    return () => {
      off();
      whisp.realtime.disconnect();
    };
  }, [chatId]);

  return (
    <div>
      <ul>
        {messages.map((m) => (
          <li key={m.messageId}>{m.senderId}: {m.message}</li>
        ))}
      </ul>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      <button
        onClick={() => {
          whisp.realtime.sendMessage(chatId, input);
          setInput("");
        }}
      >
        Send
      </button>
    </div>
  );
}
```

## Next

- [Authentication](/sdks/js/authentication) — full reference for `setAuth`, `refresh`, `logout`, …
- [Chats](/sdks/js/chats) — creating, listing, and managing chats
- [Realtime](/sdks/js/realtime) — every event the SDK can emit
- [Errors & Retries](/sdks/js/errors)
