# Installation

## Install

```bash
npm install whisp-sdk
```

## Node.js realtime support

If you use realtime WebSocket features in Node.js, install `ws`:

```bash
npm install ws
```

:::scalar-callout{type="info"}
In browsers, the SDK uses the native WebSocket stack (internally) and you typically don’t need extra dependencies.
:::

## Configuration checklist

- `baseUrl`: `https://<clientDomain>.api.whispchat.com`
- `apiKey`: **backend only**
- `webSocketImpl`: Node.js only (e.g. `ws`)
