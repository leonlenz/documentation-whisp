# JavaScript / TypeScript SDK

Package: `whisp-sdk`

## What this SDK covers

- REST API wrapper
- Auth state management (`setAuth`, `getAuth`, `logout`, `refresh`)
- Automatic JWT refresh on `401`
- Realtime WebSocket messaging with reconnect + backoff

## Constructor

```ts
import { WhispClient } from "whisp-sdk";

const whisp = new WhispClient({
  baseUrl: "https://yourapp.api.whispchat.com",
  apiKey: "your-api-key",          // server-side only
  webSocketImpl: undefined,        // Node.js only (e.g. `ws`)
});
```

:::scalar-callout{type="warning"}
`apiKey` must not be shipped to browsers/mobile clients.
:::

## Start here

::scalar-page-link{filepath="docs/sdks/js/installation.md" title="Installation" description="Install for browser or Node.js"}
::scalar-page-link{filepath="docs/sdks/js/quickstart-browser.md" title="Quickstart (Browser)" description="Recommended production flow"}
::scalar-page-link{filepath="docs/sdks/js/realtime.md" title="Realtime" description="Connect, events, and actions"}
