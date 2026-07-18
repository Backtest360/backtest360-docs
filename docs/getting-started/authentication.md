# Authentication

Every request to the engine is authenticated with an `X-API-Key` header. The Python client sets that header for you once you supply the key.

## Get a key

Create a free account at [backtest360.com/sign-up](https://backtest360.com/sign-up). A free-tier API key is provisioned with the account — no separate application step. Keys have the format:

```
b360_<random characters>
```

Signed-in users manage their keys under **account settings → API keys**: view the active key, and re-issue it when needed.

## Configure the client

The client reads its key from the `BACKTEST360_API_KEY` environment variable by default, which keeps the secret out of your source. This is the recommended path for scripts, notebooks, and CI.

```bash
export BACKTEST360_API_KEY="b360_..."
```

```python
from backtest360 import Client

client = Client()  # reads BACKTEST360_API_KEY
```

You can also pass the key explicitly — useful when a process manages several keys — but read it from your own configuration or a secrets manager rather than hardcoding a literal:

```python
import os
from backtest360 import Client

client = Client(api_key=os.environ["BACKTEST360_API_KEY"])
```

To target a non-default engine, set `BACKTEST360_ENGINE_URL` or pass `base_url=`. The default is `https://api.backtest360.com`.

## Keep keys out of source

- Never commit an API key. Read it from an environment variable or a secrets manager at the point of use.
- Never paste a key into a notebook you will share, a log line, or an error report.
- Treat a key like a password: anyone holding it can run backtests billed to your account.

## Rotate a key

Re-issue the key from **account settings → API keys**. Rotation is zero-gap: the engine mints the new key first and then revokes the old one, so a running process is never left without a valid credential mid-rotation. At steady state there is one active key per purpose. Update `BACKTEST360_API_KEY` (or your secrets manager) with the new value, then restart anything holding the old one.

## Authentication errors at a glance

When a key is missing or not accepted, the engine returns a `401` or `403` with a specific code:

| Code | Status | Meaning |
|------|--------|---------|
| `AUTH_MISSING` | 401 | No `X-API-Key` header was sent. |
| `AUTH_INVALID` | 401 | The key is unknown or has been revoked. |
| `AUTH_EXPIRED` | 401 | The key has expired. |
| `TENANT_SUSPENDED` | 403 | The account is suspended. |
| `SCOPE_DENIED` | 403 | The key lacks a scope the request requires. |
| `PLAN_UPGRADE_REQUIRED` | 403 | The request uses a paid-only feature on a free key. |

Rate and quota limits surface separately as `429` responses (`QUOTA_EXCEEDED` for your key's per-minute and per-day request limits, with a `Retry-After` header). The client raises every one of these as a `Backtest360Error` carrying the `code` and HTTP `status`. See [Handle errors](../how-to/handle-errors.md) for the full catalog and handling patterns.

## Next

[Run your first backtest →](first-backtest.md)
