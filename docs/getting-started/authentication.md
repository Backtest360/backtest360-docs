# Authentication

Every request to the Backtest360 engine requires an `X-API-Key` header. The client handles this automatically once you supply the key.

## Get a key

Sign up at [backtest360.com](https://backtest360.com) to receive an API key. Keys have the format:

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
- Rotate keys from the [account dashboard](https://backtest360.com/account).

## Rate limits

Each key carries per-minute (rpm) and per-day (rpd) request limits visible on the account dashboard. The engine returns `429 RATE_LIMIT_EXCEEDED` when a limit is hit.

## Next

[Run your first backtest →](first-backtest.md)
