# JavaScript / TypeScript SDK

Package: `whisp-sdk`

The Whisp JS/TS SDK is the fastest way to add chat to a web or Node.js application. It wraps the REST API and the realtime WebSocket layer into one friendly, strongly-typed client so you can focus on building your UI — not on plumbing HTTP requests, JWT refresh, or WebSocket reconnection logic.

:::scalar-callout{type="info"}
**New to Whisp?** Before you write any SDK code, make sure you’ve followed [Getting Started](/getting-started) to create an app and generate an API key in the [client dashboard](https://client.whispchat.com). You’ll need the **base URL** and (for server-side code) the **API key** from that page.
:::

## What you get

The SDK gives you a single `WhispClient` object with three main responsibilities:

- **REST wrapper** — every REST endpoint is exposed as a typed async method (`whisp.getChats()`, `whisp.createChat()`, `whisp.getMessages()`, …). No more manual `fetch` calls, no hand-rolled types.
- **Auth state management** — `setAuth`, `getAuth`, `logout`, `refresh`. The SDK stores your JWT in memory and *automatically refreshes* it when it expires, so your code never has to catch 401s manually.
- **Realtime** — everything live (new messages, typing indicators, reactions, reads, chat membership changes, …) lives under `whisp.realtime.*`. The SDK handles the STOMP handshake, ticket fetching, WebSocket reconnection, and exponential backoff for you.

```ts
// The full mental model in ~5 lines:
const whisp = new WhispClient({ baseUrl: "..." });
whisp.setAuth({ jwt, refreshToken, userId });     // auth is set
await whisp.realtime.connect();                    // realtime is live
whisp.realtime.on("message", (evt) => { /* … */ }); // listening
await whisp.realtime.sendMessage(chatId, "hi!");   // sending
```

## Who this SDK is for

- **Browser frontends** (React, Vue, Svelte, vanilla JS) — use the SDK with `setAuth()` *only*. Never put an API key in browser code.
- **Node.js backends** — use the SDK with an API key to register users, sign them in, and forward tokens to your frontend. You can also run realtime from Node.js for things like bots, moderation tools, or integration tests.
- **Mobile apps** with a JS bridge (React Native, Expo) — same rules as browsers: no API key on the device.

## Quick links

- [Installation](/sdks/js/installation) — installing the package (and `ws` if you need it)
- [Quickstart (Browser)](/sdks/js/quickstart-browser) — the recommended production flow for web apps
- [Quickstart (Node.js)](/sdks/js/quickstart-node) — for use cases where the API key can safely be used
- [Authentication](/sdks/js/authentication) — function-by-function reference
- [Chats](/sdks/js/chats) — create, list, manage chats
- [Messages](/sdks/js/messages) — fetch history, paginate, read receipts
- [Realtime: receiving events](/sdks/js/realtime) — every event the SDK emits
- [Realtime: action methods](/sdks/js/realtime-actions) — sending messages, typing, reactions, …
- [Errors & retries](/sdks/js/errors)
- [Troubleshooting](/sdks/js/troubleshooting)

## Constructing the client

The simplest possible setup (browser app):

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
});
```

For Node.js backends where it’s safe to hold the API key:

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY, // server-only, never in browser bundles
});
```

### When do I need `webSocketImpl`?

This is the most common confusion for new users, so here’s the short rule:

| Environment | Do you need `webSocketImpl`? |
|---|---|
| Browser (any framework) | **No.** The native `window.WebSocket` is used automatically. |
| Modern Node.js (≥22) with `--experimental-websocket` or built-in `WebSocket` | **No** (if `globalThis.WebSocket` exists). |
| Most Node.js setups (LTS, CI, serverless) | **Yes** — install `ws` and pass it in. |
| React Native / Expo | **No.** Use the bundled WebSocket. |

In Node.js, there is historically no built-in WebSocket client. The SDK doesn’t want to force `ws` as a hard dependency (that would bloat browser bundles for no reason), so instead it asks **you** to pass in whichever WebSocket implementation you want to use. `ws` is the de-facto standard and the one we recommend.

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY,
  webSocketImpl: WebSocket, // only needed if you're running realtime from Node.js
});
```

:::scalar-callout{type="info"}
If you’re only making REST calls from Node.js (no realtime), you **don’t** need `webSocketImpl` at all. It’s only consulted when you call `whisp.realtime.connect()`.
:::

## Security reminder

:::scalar-callout{type="warning"}
`apiKey` must **not** be shipped to browsers or mobile clients. The `registerUser` and `signIn` endpoints require the `x-api-key` header and must run in a trusted backend. See the [Browser Quickstart](/sdks/js/quickstart-browser) for the recommended pattern.
:::
