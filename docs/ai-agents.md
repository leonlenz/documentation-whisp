# AI Agent Integration Guide

This guide is for AI coding agents (Claude, Cursor, Copilot, etc.) that need to integrate Whisp chat infrastructure into a product **without requiring manual interaction with the Whisp dashboard**. By the end of this guide you will have two values:

1. **API Key** — a secret token used to authenticate server-side SDK calls
2. **Base URL** — the cluster endpoint your SDK client connects to

These are the only values you need to start building with the Whisp SDK.

---

## Setup Flow

All setup steps use the **Whisp Management API**:

```
https://management.api.whispchat.com
```

The four steps are:

1. **Register** a dashboard account (or **login** if one already exists)
2. **Create** a demo app
3. **Generate** an API key
4. **Retrieve** the base URL for the app's cluster

Each step below includes a `curl` example and the expected response. A complete Python script combining all steps is provided at the end.

:::scalar-callout{type="info"}
The access token returned by register/login is valid for **15 minutes** — more than enough to complete the setup. You do not need to handle token refresh during this flow.
:::

---

## Step 1 — Register a Dashboard Account

```bash
curl -s -X POST https://management.api.whispchat.com/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "companyName": "Acme Corp",
    "email": "dev@acme.com",
    "password": "S3cure!Pass#2026"
  }'
```

**Response** `201 Created`

```json
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs...",
  "email": "dev@acme.com",
  "role": "CLIENT",
  "expiresIn": 900
}
```

Save the `accessToken` — you need it as a `Bearer` token for all subsequent requests.

**If the account already exists** (HTTP `409`), log in instead:

```bash
curl -s -X POST https://management.api.whispchat.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "dev@acme.com",
    "password": "S3cure!Pass#2026"
  }'
```

**Response** `200 OK`

```json
{
  "mfaRequired": false,
  "accessToken": "eyJhbGciOiJSUzI1NiIs...",
  "email": "dev@acme.com",
  "role": "CLIENT"
}
```

---

## Step 2 — Create a Demo App

```bash
curl -s -X POST https://management.api.whispchat.com/api/client/apps \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -d '{
    "name": "my-chat-app"
  }'
```

**Response** `201 Created`

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "clientId": "f0e1d2c3-b4a5-6789-0fed-cba987654321",
  "name": "my-chat-app",
  "status": "ACTIVE",
  "tier": "DEMO",
  "clusterId": "d4c3b2a1-0987-6543-21fe-dcba09876543",
  "createdAt": "2026-04-25T12:00:00Z",
  "updatedAt": "2026-04-25T12:00:00Z"
}
```

Save the `id` — this is your `appId` for the next steps.

:::scalar-callout{type="info"}
Each account can have up to **5 demo apps**. Demo apps are fully functional but limited in concurrent users. To list existing apps, send `GET /api/client/apps` with the same `Authorization` header.
:::

---

## Step 3 — Generate an API Key

```bash
curl -s -X POST https://management.api.whispchat.com/api/client/apps/<APP_ID>/keys \
  -H "Authorization: Bearer <ACCESS_TOKEN>"
```

No request body is required.

**Response** `201 Created`

```json
{
  "id": "b2c3d4e5-f6a7-8901-bcde-f12345678901",
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6Ikp...",
  "keyPrefix": "eyJhbGci",
  "audience": "demo-cluster",
  "tier": "DEMO",
  "createdAt": "2026-04-25T12:00:05Z"
}
```

:::scalar-callout{type="warning"}
**The `token` field is your API key. It is only returned once — on creation. It cannot be retrieved again.** Save it immediately. If lost, revoke the key and generate a new one.
:::

Each app supports up to **5 active API keys**. You cannot generate keys for suspended apps.

---

## Step 4 — Get Your Base URL

```bash
curl -s -X GET https://management.api.whispchat.com/api/client/apps/<APP_ID> \
  -H "Authorization: Bearer <ACCESS_TOKEN>"
```

**Response** `200 OK`

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "clientId": "f0e1d2c3-b4a5-6789-0fed-cba987654321",
  "name": "my-chat-app",
  "status": "ACTIVE",
  "type": "DEMO",
  "tier": "DEMO",
  "clusterId": "d4c3b2a1-0987-6543-21fe-dcba09876543",
  "createdAt": "2026-04-25T12:00:00Z",
  "updatedAt": "2026-04-25T12:00:00Z",
  "clusterEndpoint": "https://demo.api.whispchat.com",
  "clusterName": "demo-cluster",
  "clusterStatus": "ACTIVE"
}
```

The `clusterEndpoint` field is your **base URL**. For demo apps this is always `https://demo.api.whispchat.com`.

---

## Complete Setup Script (Python)

This script runs all four steps end-to-end. It handles the case where an account already exists by falling back to login.

```python
import requests
import json
import sys

MANAGEMENT_API = "https://management.api.whispchat.com"


def setup_whisp(company_name: str, email: str, password: str, app_name: str) -> dict:
    session = requests.Session()

    # ── Step 1: Register (or login if account exists) ──
    resp = session.post(
        f"{MANAGEMENT_API}/api/auth/register",
        json={
            "companyName": company_name,
            "email": email,
            "password": password,
        },
    )

    if resp.status_code == 201:
        access_token = resp.json()["accessToken"]
    elif resp.status_code == 409:
        # Account already exists — login instead
        resp = session.post(
            f"{MANAGEMENT_API}/api/auth/login",
            json={"email": email, "password": password},
        )
        resp.raise_for_status()
        access_token = resp.json()["accessToken"]
    else:
        resp.raise_for_status()

    session.headers["Authorization"] = f"Bearer {access_token}"

    # ── Step 2: Create a demo app ──
    resp = session.post(
        f"{MANAGEMENT_API}/api/client/apps",
        json={"name": app_name},
    )
    resp.raise_for_status()
    app = resp.json()
    app_id = app["id"]

    # ── Step 3: Generate an API key ──
    resp = session.post(f"{MANAGEMENT_API}/api/client/apps/{app_id}/keys")
    resp.raise_for_status()
    key_data = resp.json()
    api_key = key_data["token"]  # Only returned once — save immediately

    # ── Step 4: Get the base URL ──
    resp = session.get(f"{MANAGEMENT_API}/api/client/apps/{app_id}")
    resp.raise_for_status()
    app_detail = resp.json()
    base_url = app_detail["clusterEndpoint"]

    return {
        "api_key": api_key,
        "base_url": base_url,
        "app_id": app_id,
        "app_name": app_detail["name"],
    }


if __name__ == "__main__":
    result = setup_whisp(
        company_name="Acme Corp",
        email="dev@acme.com",
        password="S3cure!Pass#2026",
        app_name="my-chat-app",
    )

    print(json.dumps(result, indent=2))
    print(f"\nSave the api_key — it cannot be retrieved again.")
```

**Output**

```json
{
  "api_key": "eyJhbGciOiJSUzI1NiIsInR5cCI6Ikp...",
  "base_url": "https://demo.api.whispchat.com",
  "app_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "app_name": "my-chat-app"
}
```

---

## What You Now Have

| Value | Where it goes | Example |
|---|---|---|
| **API Key** (`api_key`) | `WhispClient({ apiKey: "..." })` | `eyJhbGciOiJS...` |
| **Base URL** (`base_url`) | `WhispClient({ baseUrl: "..." })` | `https://demo.api.whispchat.com` |

These two values are everything you need to initialize the Whisp SDK and start building chat features.

:::scalar-callout{type="warning"}
**The API key is a server-side secret.** Never expose it in frontend/browser code. Use it only in backend environments (Node.js servers, scripts, CLI tools). See [Authentication](/sdks/js/authentication) for the recommended browser pattern.
:::

---

## Build with the SDK

With your API key and base URL ready, use the JavaScript / TypeScript SDK to add chat to your application. The SDK handles REST calls, JWT management, and realtime WebSocket connections.

### Getting started

- [Installation](/sdks/js/installation) — install `whisp-sdk` (and `ws` for Node.js)
- [Quickstart (Node.js)](/sdks/js/quickstart-node) — register users, sign in, create chats, send messages from a backend or script
- [Quickstart (Browser)](/sdks/js/quickstart-browser) — the recommended pattern for web apps (backend proxies the API key, browser uses JWTs only)

### Core features

- [Authentication](/sdks/js/authentication) — `registerUser`, `signIn`, `setAuth`, token refresh
- [Chats](/sdks/js/chats) — create, list, and manage chats
- [Messages](/sdks/js/messages) — fetch message history, pagination, read receipts

### Realtime

- [Realtime events](/sdks/js/realtime) — listen for messages, typing indicators, reactions, presence
- [Realtime actions](/sdks/js/realtime-actions) — send messages, edit, react, mark as read

### Reference

- [Errors & Retries](/sdks/js/errors)
- [Troubleshooting](/sdks/js/troubleshooting)
- [REST API Reference](/api) — full OpenAPI specification

---

## Quick Reference: SDK Initialization

Once you have the API key and base URL, initializing the SDK looks like this:

**Node.js (backend, script, bot)**

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",   // base_url from setup
  apiKey: process.env.WHISP_API_KEY,            // api_key from setup
  webSocketImpl: WebSocket,                      // required for realtime in Node.js
});
```

**Browser (frontend)**

```ts
import { WhispClient } from "whisp-sdk";

// Browser apps must NOT use the API key directly.
// Your backend calls registerUser/signIn with the API key,
// then forwards the JWT + refreshToken + userId to the browser.
const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
});

// Tokens received from your backend's auth proxy endpoint
whisp.setAuth({ jwt, refreshToken, userId });
```

See the [Browser Quickstart](/sdks/js/quickstart-browser) for the full backend-proxy pattern.

---

## Management API Reference

For ongoing account management (listing apps, revoking keys, checking profile), use these endpoints with a valid access token.

| Action | Method | Endpoint |
|---|---|---|
| Get profile | `GET` | `/api/client/profile` |
| Update profile | `PUT` | `/api/client/profile` |
| List apps | `GET` | `/api/client/apps` |
| Get app details | `GET` | `/api/client/apps/{appId}` |
| Update app | `PUT` | `/api/client/apps/{appId}` |
| Delete app | `DELETE` | `/api/client/apps/{appId}` |
| List API keys | `GET` | `/api/client/apps/{appId}/keys` |
| Revoke API key | `DELETE` | `/api/client/apps/{appId}/keys/{keyId}` |
| Rotate API key | `POST` | `/api/client/apps/{appId}/keys/{keyId}/rotate` |
| Refresh access token | `POST` | `/api/auth/refresh` |
| Logout | `POST` | `/api/auth/logout` |

All endpoints use base URL `https://management.api.whispchat.com` and require the `Authorization: Bearer <accessToken>` header.
