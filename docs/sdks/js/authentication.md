# Authentication

This page documents authentication-related SDK functions **individually**, including parameters and return objects.

## Client-side vs server-side

- **Server-side only:** `registerUser`, `signIn` (require `x-api-key`)
- **Client-side:** all other methods use JWT auth (`Authorization: Bearer <JWT>`)

---

## `setAuth(auth)`

Store auth tokens in the client.

**Signature**

```ts
whisp.setAuth({
  jwt: string;
  refreshToken: string;
  userId: string; // uuid
});
```

**Notes**
- `jwt` is the JWT without the `Bearer ` prefix.
- `refreshToken` is the refresh token string.
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

Authenticates a user. The server returns:
- JWT in **`Authorization` response header** (`Bearer <JWT>`)
- refresh token in response body

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


**JWT header example**

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

---

## `refresh()` (client-side)

Refresh an expired JWT using the refresh token.

**What the API expects**
- `Authorization: Bearer <refreshToken>` header
- body: `{ "expiredJwt": "<expired_jwt>" }`

**Returns**
- `boolean` — `true` if refresh succeeded; otherwise throws `WhispError` (typically `401`).

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
