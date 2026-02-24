# JavaScript / TypeScript SDK

Package: `whisp-sdk`

:::scalar-callout{type="warning"}
`apiKey` must not be shipped to browsers/mobile clients. Server-only endpoints require an API key (`x-api-key`) and must run in a trusted backend.
:::

## What you get

- REST API wrapper (same request/response models as the REST API)
- auth state management (`setAuth`, `getAuth`, `logout`, `refresh`)
- realtime (STOMP over WebSocket) wrapped behind `whisp.realtime.*`

## Quick links

- [Installation](/sdks/js/installation)
- [Quickstart (Browser)](/sdks/js/quickstart/browser)
- [Quickstart (Node.js)](/sdks/js/quickstart/node)
- [Authentication (function-by-function)](/sdks/js/authentication)
- [Chats (function-by-function)](/sdks/js/chats)
- [Messages (function-by-function)](/sdks/js/messages)
- [Realtime (function-by-function)](/sdks/js/realtime)
- [Event Types & payloads](/sdks/js/events)
- [Errors & retries](/sdks/js/errors)

## Constructor

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY, // server-only
});
```

In Node.js realtime scenarios, also pass a WebSocket implementation (e.g. `ws`):

```ts
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY,
  webSocketImpl: WebSocket,
});
```
