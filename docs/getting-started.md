# Getting Started

## Recommended integration approach

- **SDK-first (recommended):** use an SDK for your platform and refer to the REST API for edge cases.
- **API-first:** integrate directly against REST + WebSocket.

:::scalar-callout{type="info"}
If you’re building a browser app, the SDK is designed for a backend-assisted auth flow (backend uses API key → client uses JWT).
:::

## Base URL

Whisp uses a customer-specific base URL:

`https://<yourapp>.api.whispchat.com`

## Where to go next

::scalar-page-link{filepath="docs/sdks/index.md" title="SDK Overview" description="Pick a platform SDK and start integrating."}
::scalar-page-link{filepath="docs/sdks/js/quickstart-browser.md" title="Quickstart (Browser)" description="Recommended production flow for web apps."}
