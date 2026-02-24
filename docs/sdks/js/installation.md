# Installation

## Install the SDK

```bash
npm install whisp-sdk
```

## Node.js realtime support

If you use realtime WebSocket features in Node.js, also install `ws`:

```bash
npm install ws
```

:::scalar-callout{type="info"}
In browsers, the SDK uses the native `WebSocket` automatically.
:::

## TypeScript support

The SDK is written in TypeScript and ships with full type declarations.

```ts
import { WhispClient, SendMsgEvent } from "whisp-sdk";

whisp.realtime.on("message", (event: SendMsgEvent) => {
  console.log(event.messageId, event.message, event.senderId);
});
```
