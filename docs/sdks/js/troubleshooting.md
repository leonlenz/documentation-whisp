# Troubleshooting

A running list of the most common things that trip people up. Start at the top — the first two account for probably 80% of the issues we see.

## Realtime fails to connect in Node.js

**Symptom:** `whisp.realtime.connect()` throws, or you see “WebSocket is not defined” in your logs.

**Cause:** You forgot to pass `webSocketImpl` to the `WhispClient` constructor. Node.js has no built-in WebSocket, so the SDK needs you to hand it one.

**Fix:**

```bash
npm install ws
```

```ts
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "…",
  apiKey: process.env.WHISP_API_KEY,
  webSocketImpl: WebSocket, // <-- this line
});
```

See [Installation → Node.js](/sdks/js/installation) for the full rundown.

---

## Realtime fails to connect in the browser

**Checklist:**

1. Confirm the client is authenticated — you called `signIn()` (Node) or `setAuth()` (browser). `whisp.isAuthenticated` should be `true`.
2. Call `await whisp.realtime.connect()` **after** auth is set.
3. If you’re behind a corporate proxy or firewall, verify WebSocket connections are allowed. Some networks block WebSockets entirely.
4. If your app is served over HTTPS, your Whisp base URL must be HTTPS too — browsers block mixed-content WebSockets.

---

## “401 Unauthorized” loops

**Symptom:** Every request fails with 401, even after a successful sign-in.

**Likely cause:** Your refresh token expired or was revoked (e.g. from a `logout()` on another device). The SDK tried to refresh silently, the refresh failed, and now it’s out of options.

**Fix:** Clear your stored auth state and re-authenticate the user (show the login screen again).

```ts
whisp.realtime.disconnect();
// have the user sign in again → whisp.setAuth({...})
```

---

## “API key is required for this endpoint”

**Symptom:** Calling `registerUser()` or `signIn()` throws with this message.

**Cause:** You constructed `WhispClient` without an `apiKey`. Those two endpoints *always* require one.

**Fix:** If you’re in Node.js, pass `apiKey: process.env.WHISP_API_KEY`. If you’re in a browser, you shouldn’t be calling these endpoints at all — move them to your backend and use `setAuth()` on the browser side. See [Browser Quickstart](/sdks/js/quickstart-browser).

---

## Missing messages after reconnecting

Realtime is for *immediate* updates. If the client was offline for a while, you will have missed events during that window. After reconnect, call REST to backfill:

```ts
const { messages } = await whisp.getMessages(chatId, 50);
```

For older history, use cursor-based pagination — see [Messages](/sdks/js/messages).

---

## Reaction deletion confusion

There are two places you might see a “reaction id” and it’s easy to grab the wrong one.

- **In the realtime `reaction` event:** the field `messageId` is actually the **reaction id** — pass it to `removeReaction()`.
- **In REST message history:** it lives at `messages[].reactions[].reactionId`.

---

## Multiple devices / duplicate events

A single user can have multiple simultaneous connections (phone + desktop + another tab). Each connection will receive the same events. Design your UI to dedupe by `messageId` / `reactionId` if you render from the realtime stream directly.

---

## Still stuck?

- Re-read the [Browser Quickstart](/sdks/js/quickstart-browser) or [Node.js Quickstart](/sdks/js/quickstart-node) end-to-end — the issue is often a missing step.
- Check the [Errors & Retries](/sdks/js/errors) page for how `WhispError` surfaces API-level issues.
- File an issue at [github.com/whispchat-com](https://github.com/whispchat-com) with a minimal reproduction.
