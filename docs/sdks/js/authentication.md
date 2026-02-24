# Authentication

## Auth state

```ts
// Set auth tokens (after your backend authenticates)
whisp.setAuth({ jwt, refreshToken, userId });

// Read current auth state
const auth = whisp.getAuth(); // { jwt, refreshToken, userId } | null

// Check if authenticated
whisp.isAuthenticated; // boolean
```

## Server-only endpoints

These require an API key and should be called from your backend:

- `registerUser`
- `signIn`

```ts
// Sign in (requires API key — backend only)
const user = await whisp.signIn({ username, password });

// Register user (requires API key — backend only)
await whisp.registerUser({ username, email, password, firstName?, surName? });
```

## Refresh + logout

```ts
// Refresh JWT manually (normally happens automatically on 401)
const success = await whisp.refresh();

// Logout (invalidates refresh token)
await whisp.logout();

// Logout all sessions for the user
await whisp.logoutAll();
```

## Automatic token refresh

The SDK automatically refreshes the JWT when any request returns a `401`. This is transparent.  
If the refresh token itself is expired, the SDK clears auth state and throws a `WhispError` with status `401`.

:::scalar-callout{type="warning"}
Treat a failed refresh as “session expired” and re-authenticate.
:::
