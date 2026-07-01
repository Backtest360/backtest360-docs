# Authentication

Every request to the Backtest360 engine requires an `X-API-Key` header. The client handles this automatically once you supply the key.

## Get a key

Get a free key instantly at [backtest360.com/api-access](https://backtest360.com/api-access) — submit your email and a key is issued on the spot and emailed to you; no approval needed. Keys have the format:

```
b360_<43 random characters>
```

## Pass the key to the client

**Option 1 — kwarg (simplest):**

```python
from backtest360 import Client
client = Client(api_key="b360_...")
```

**Option 2 — environment variable (recommended for scripts and CI):**

```bash
export BACKTEST360_API_KEY="b360_..."
```

```python
client = Client()  # reads BACKTEST360_API_KEY automatically
```

## Key security

- Never commit API keys to source control.
- Store production keys in an environment variable or secrets manager.
- Rotate a key by re-submitting the same email at [backtest360.com/api-access](https://backtest360.com/api-access) — a new key is issued and the previous one is revoked.

## Rate limits

Each key carries per-minute (rpm) and per-day (rpd) request limits. The engine returns `429 RATE_LIMIT_EXCEEDED` when a limit is hit.

## Next

[Run your first backtest →](first-backtest.md)
