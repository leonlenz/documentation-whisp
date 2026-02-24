# Getting Started

## Recommended integration approach

- **SDK-first (recommended):** use an SDK for your platform and refer to the REST API for edge cases.
- **API-first:** integrate directly against REST + WebSocket.

:::scalar-callout{type="info"}
For browser apps, the SDK is designed for a backend-assisted auth flow (backend uses API key → client uses JWT).
:::

## Base URL

Whisp uses a customer-specific base URL:

`https://<yourapp>.api.whispchat.com`

## Where to go next

- [SDK Overview](/sdks)
- [JS/TS Quickstart (Browser)](/sdks/js/quickstart/browser)
- [REST API Reference](/api)
