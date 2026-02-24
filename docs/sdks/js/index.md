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

:::scalar-card{title="Installation" leftIcon="phosphor/regular/download-simple"}
Install the SDK for browser or Node.js.

[Go to Installation →](/sdks/js/installation)
:::

:::scalar-card{title="Quickstart (Browser)" leftIcon="phosphor/regular/rocket-launch"}
Recommended production flow for web apps.

[Go to Browser Quickstart →](/sdks/js/quickstart/browser)
:::

:::scalar-card{title="Realtime" leftIcon="phosphor/regular/wifi-high"}
Connect, subscribe to events, and send actions.

[Go to Realtime →](/sdks/js/realtime)
:::
