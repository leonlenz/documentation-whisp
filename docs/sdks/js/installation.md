# Installation

The SDK ships as a single npm package: **`whisp-sdk`**. It runs anywhere modern JavaScript runs — browsers, Node.js (LTS and newer), Bun, Deno (via npm), React Native, Electron, and serverless platforms.

## 1. Install the package

```bash
npm install whisp-sdk
```

Or with your package manager of choice:

```bash
pnpm add whisp-sdk
yarn add whisp-sdk
bun add whisp-sdk
```

That’s it for browser apps — you can jump straight to the [Browser Quickstart](/sdks/js/quickstart-browser).

---

## 2. (Node.js only) Install a WebSocket library

If you plan to use realtime features (`whisp.realtime.*`) from Node.js, you also need a WebSocket client. The industry standard is [`ws`](https://www.npmjs.com/package/ws):

```bash
npm install ws
```

Then pass it to the `WhispClient` constructor:

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY,
  webSocketImpl: WebSocket, // <-- this is what tells the SDK how to open a socket
});
```

### Why is this step needed?

Browsers have a built-in [`WebSocket` API](https://developer.mozilla.org/docs/Web/API/WebSocket), so the SDK just uses `globalThis.WebSocket` there. Node.js, historically, does **not** have one — and we don’t want to force every browser user to download `ws` just so it works in Node. So instead, the SDK lets you plug in whichever WebSocket implementation fits your runtime.

### Do I *always* need `webSocketImpl` in Node.js?

No. Here’s the rule:

| What you’re doing in Node.js | Do you need `webSocketImpl`? |
|---|---|
| REST-only calls (e.g. a signup endpoint, a webhook handler) | **No.** |
| Anything under `whisp.realtime.*` (sending, receiving, typing, …) | **Yes.** |
| Running on Node.js ≥ 22 with a built-in `globalThis.WebSocket` | No, but passing `ws` is still safe. |

:::scalar-callout{type="info"}
If in doubt, just pass `ws`. It works everywhere, has no breaking changes planned, and adds only a few KB to your server bundle.
:::

---

## 3. Configuration checklist

Here’s everything you might want to pass to `new WhispClient({ … })`:

| Option | When you need it | Where it comes from |
|---|---|---|
| `baseUrl` | **Always.** Every request goes here. | Your app’s overview page in the [dashboard](https://client.whispchat.com). Format: `https://<clientDomain>.api.whispchat.com` |
| `apiKey` | **Backend-only** — required for `registerUser` and `signIn`. | The **API Keys** tab in your dashboard app. Never ship this to the browser. |
| `webSocketImpl` | **Node.js realtime only.** | `import WebSocket from "ws"` |

:::scalar-callout{type="warning"}
Never, ever put your `apiKey` in browser code — not in an env var bundled by Vite/webpack, not in a `<script>` tag, not in a React `useState`. It grants the power to register and sign in any user in your app. If you’re not sure whether your code will end up in a browser bundle, assume it will and keep the key server-side.
:::

## Next

- [Quickstart (Browser)](/sdks/js/quickstart-browser) — the recommended setup for web frontends
- [Quickstart (Node.js)](/sdks/js/quickstart-node) — for servers, bots, and scripts
