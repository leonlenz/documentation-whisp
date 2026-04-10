# Authentication

This page documents authentication-related SDK functions **individually**, including parameters, return objects, and when to reach for them.

:::scalar-callout{type="info"}
If you’re still getting your bearings, read the [Browser Quickstart](/sdks/js/quickstart-browser) or [Node.js Quickstart](/sdks/js/quickstart-node) first — they show these functions in the context of a complete app.
:::

## Client-side vs server-side

Whisp splits its API into two groups based on whether they need your API key:

- **Server-side only** — `registerUser`, `signIn`. These require the `x-api-key` header and should only run in a trusted backend (or a Node.js script where the key is safe).
- **Client-side (JWT)** — every other method. These run anywhere: browsers, mobile apps, Node.js scripts. They authenticate with a short-lived JWT that the SDK stores and refreshes for you automatically.

**Rule of thumb:** if your code is going to end up in a browser bundle, you should never call `registerUser` or `signIn` directly. Proxy them through your backend and forward the resulting tokens to the browser via `setAuth()`.

---

## `setAuth(auth)`

Store auth tokens in the SDK. This is the **primary entry point for browser apps** — your backend calls `signIn` against Whisp (with the API key), forwards the tokens to the browser, and the browser passes them here.

**Signature**

```ts
whisp.setAuth({
  jwt: string;
  refreshToken: string;
  userId: string; // uuid
});
```

**Example**

```ts
// Call *your own* backend — this is a route on your server
// (e.g. /auth/whisp/sign-in), not a Whisp endpoint. Your backend
// is the one that talks to Whisp with the API key and forwards
// the resulting tokens back here.
const { jwt, refreshToken, userId } = await fetch("/auth/whisp/sign-in", {
  method: "POST",
  body: JSON.stringify({ username, password }),
}).then((r) => r.json());

whisp.setAuth({ jwt, refreshToken, userId });
// whisp.isAuthenticated === true from now on
```

**Notes**
- `jwt` is the JWT **without** the `Bearer ` prefix.
- `refreshToken` is the long-lived refresh token string.
- `userId` is the authenticated user’s UUID.

## `getAuth()`

Return the currently stored auth state (or `null`).

```ts
const auth = whisp.getAuth();
```

## `isAuthenticated`

```ts
if (!whisp.isAuthenticated) {
  // prompt login
}
```

---

## `registerUser(request)` (server-only)

Registers a new user account.

**Signature**

```ts
await whisp.registerUser({
  username: string;
  email: string;
  password: string;
  firstName?: string;
  surName?: string;
});
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `username` | string | required |  |
| `firstName` | string | optional | Optional |
| `surName` | string | optional | Optional |
| `email` | string (email) | required |  |
| `password` | string (password) | required |  |

**Returns**
- `void` (HTTP `201` on success)

---

## `signIn(request)` (server-only)

Authenticates a user. On success the SDK stores the JWT, refresh token, and user ID internally — you don’t need to call `setAuth()` after this.

**Signature**

```ts
const user = await whisp.signIn({
  username: string;
  password: string;
});
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `username` | string | required |  |
| `password` | string (password) | required |  |

**Returns**

### SignInResponse

| Field | Type | Notes |
|---|---|---|
| `id` | string (uuid) |  |
| `username` | string |  |
| `email` | string (email) |  |
| `refreshToken` | string |  |
| `roles` | array<string> |  |

**Example**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "username": "johndoe",
  "email": "john.doe@example.com",
  "refreshToken": "dGhpcyBpcyBhIHJlZnJlc2ggdG9rZW4...",
  "roles": [
    "USER"
  ]
}
```

---

## `refresh()` (client-side)

Refresh an expired JWT using the refresh token. **You usually don’t need to call this yourself** — the SDK calls it automatically whenever a request gets a 401 response, so your application code can just keep calling methods and trust the SDK to keep the session alive.

Call it manually only if you’re doing something exotic — for example, proactively refreshing before a long-running operation, or debugging auth state.

**Returns**
- `boolean` — `true` if refresh succeeded, `false` otherwise. If the refresh token itself is expired or revoked, the user needs to sign in again.

---

## `logout()` (client-side)

Invalidates the current refresh token.

**Returns:** `void`

---

## `logoutAll()` (client-side)

Invalidates **all** refresh tokens for the authenticated user.

**Returns:** `void`

---

## `getUser()` (client-side)

Fetch the authenticated user’s profile.

```ts
const me = await whisp.getUser();
```

**Returns**

### UserInfo

| Field | Type | Notes |
|---|---|---|
| `id` | string (uuid) |  |
| `username` | string |  |
| `email` | string (email) |  |
| `role` | array<string> |  |

**Example**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "username": "string",
  "email": "user@example.com",
  "role": [
    "USER"
  ]
}
```


---

## `changeUsername(newUsername)` (client-side)

Change the authenticated user’s username. The API returns a **new JWT** in the `Authorization` response header.

```ts
await whisp.changeUsername("johndoe_updated");
```

**Parameters**

| Field | Type | Required | Notes |
|---|---|---|---|
| `newUsername` | string | required |  |

**Returns**
- `void` (SDK updates stored JWT if it reads the header)

---

## `deleteUser()` (client-side)

Delete the authenticated user, including all their data/messages.

**Returns:** `void`

:::scalar-callout{type="info"}
Realtime connections are handled by `whisp.realtime.connect()`. The SDK takes care of any low-level handshake steps.
:::
