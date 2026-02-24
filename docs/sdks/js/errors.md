# Errors & Retries

## HTTP errors

The SDK throws `WhispError` for HTTP errors:

```ts
import { WhispError } from "whisp-sdk";

try {
  await whisp.createChat("Test", ["nonexistent_user"]);
} catch (err) {
  if (err instanceof WhispError) {
    console.log(err.status);  // HTTP status code
    console.log(err.message); // Error message
    console.log(err.body);    // Raw error response body
  }
}
```

## Recommended handling

- `401`: treat as “unauthenticated”. If refresh fails, force re-login.
- `403`: permissions/role issue (don’t retry blindly).
- `429`: back off and retry later.
- `5xx`: retry with exponential backoff + jitter.
