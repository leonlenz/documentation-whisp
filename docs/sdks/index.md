# SDKs

Whisp SDKs provide:
- typed request/response models
- auth state helpers (JWT + refresh token)
- realtime lifecycle management (where applicable)

## Available SDKs

:::scalar-card{title="JavaScript / TypeScript" leftIcon="phosphor/regular/code"}
Browser + Node.js SDK.

[Open JS/TS SDK →](/sdks/js)
:::

## Adding more SDKs later

Use one folder per SDK:

`docs/sdks/<sdk>/`
- `index.md` (overview)
- `installation.md`
- `quickstart/*`
- `authentication.md`
- `errors.md`
- `troubleshooting.md`

Then add a new `group` under `/sdks` in `scalar.config.json`.
