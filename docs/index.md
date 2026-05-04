# Whisp Developer Documentation

Whisp is a scalable chat infrastructure you can integrate into web, iOS, and Android apps using SDKs and APIs.

:::scalar-card{title="Getting Started" leftIcon="phosphor/regular/rocket-launch"}
Create a Whisp account, set up your first app in the dashboard, and generate an API key.

[Start here →](/getting-started)
:::

:::scalar-card{title="AI Agent Integration" leftIcon="phosphor/regular/robot"}
**Building Whisp into a product with an AI coding agent (Claude, Cursor, Copilot, etc.)?** Start here. This guide walks an agent end-to-end through account setup, app creation, and API key generation — no manual dashboard interaction required.

[Open the AI agent guide →](/ai-agents)
:::

:::scalar-card{title="JavaScript / TypeScript SDK" leftIcon="phosphor/regular/package"}
Production-ready integration for browser and Node.js.

[Open JS/TS SDK docs →](/sdks/js)
:::

:::scalar-card{title="REST API Reference" leftIcon="phosphor/regular/brackets-angle"}
Full endpoint reference generated from OpenAPI (requests, responses, schemas).

[Open API reference →](/api)
:::

:::scalar-callout{type="warning"}
Never expose your Whisp API key in browser/mobile code. Use your backend to authenticate and pass short‑lived tokens to clients.
:::

## What’s in this documentation

- **SDK guides:** installation, quickstarts, authentication, chats, messages, realtime
- **Data models:** each SDK method includes parameter + return shapes (mirrors the REST/WebSocket contracts)
- **API reference:** canonical REST reference (OpenAPI)
