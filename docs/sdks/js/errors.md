# Errors & Retries

The SDK throws a `WhispError` for HTTP-level errors and surfaces WebSocket errors through the `error` event.

## REST error bodies

### Unauthorized (expired/invalid JWT)

### AuthErrorResponse

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | optional |  |
| `title` | string | optional |  |
| `status` | string | optional |  |
| `detail` | string | optional |  |
| `instance` | string | optional |  |
| `code` | string | optional |  |

**Example**

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": "401",
  "detail": "Invalid or expired JWT.",
  "instance": "/api/chat/getChats",
  "code": "AUTH_INVALID_TOKEN"
}
```


### Validation / bad request

### ErrorMessage

| Field | Type | Required | Notes |
|---|---|---|---|
| `message` | string | optional |  |

**Example**

```json
{
  "message": "Invalid request - validation failed."
}
```


## Recommended retry strategy

- `401`: if refresh fails → treat as “session expired” and re-authenticate
- `403`: permissions issue — don’t retry blindly
- `429`: back off and retry later
- `5xx`: retry with exponential backoff + jitter

## WebSocket errors

- Most connection failures are caused by missing/expired auth (JWT not set), network restrictions (WebSockets blocked), or transient connectivity.
- If realtime fails repeatedly, re-authenticate the user and retry `whisp.realtime.connect()` with backoff.
