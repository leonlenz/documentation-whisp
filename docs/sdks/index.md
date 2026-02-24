# SDKs

Whisp SDKs provide:
- typed request/response models
- auth state helpers (JWT + refresh token)
- realtime lifecycle management (where applicable)

## Available SDKs

::scalar-page-link{filepath="docs/sdks/js/index.md" title="JavaScript / TypeScript" description="Browser + Node.js SDK"}

## Adding more SDKs later

Use one folder per SDK:

`docs/sdks/<sdk>/`
- `index.md` (overview)
- `installation.md`
- `quickstart-*.md`
- `authentication.md`
- `errors.md`
- `troubleshooting.md`

Add a new `group` under `/sdks` in `scalar.config.json`.
