# Handle Errors

Every failure the client can raise is a `Backtest360Error`. There is one exception type;
you distinguish causes by inspecting its fields rather than catching different classes.

## The exception

`Backtest360Error` carries five fields:

- `status` — the HTTP status from the engine, or `0` for errors the client raises before
  a request is sent.
- `code` — a stable machine-readable string. Branch on this.
- `body` — the engine's response body, when present. For strategy validation it holds the
  structured error report.
- `request_id` — correlates the failure with an engine-side log entry. Include it when
  reporting a problem.
- `retry_after` — seconds to wait before retrying, set on throttling responses.

The full code tables are in the [Exceptions reference](../client-reference/exceptions.md).

## Branch on the code

```python
from backtest360 import Client, Backtest360Error

client = Client()

try:
    result = client.backtest(strategy, df, risk=risk)
except Backtest360Error as e:
    if e.code in ("AUTH_MISSING", "AUTH_INVALID", "AUTH_EXPIRED"):
        print("Check your API key:", e.code)
    elif e.status == 422:
        # Strategy validation — e.body holds the structured error list.
        for err in e.body["errors"]:
            print(f"{err['location']}: {err['message']}")
    else:
        raise
```

Prefer `code` over `status` where you can. A `429`, for example, covers three distinct
conditions — a quota (`QUOTA_EXCEEDED`), too many concurrent requests
(`CONCURRENCY_EXCEEDED`), and an upstream provider throttle (`RATE_LIMITED`) — and only
the first carries a `retry_after`.

## What is worth retrying

Retry only transient, server-side conditions. Client and validation errors will not
succeed on a second identical attempt.

| Code | Retry? | Notes |
|------|--------|-------|
| `QUOTA_EXCEEDED` | Yes | Wait for `retry_after` before retrying. |
| `ENGINE_BUSY` | Yes | Compute capacity was exhausted; back off and retry. |
| `COMPUTE_TIMEOUT` | Yes | The run exceeded the engine's compute window; retry, and consider a smaller request. |
| `PROVIDER_UNAVAILABLE` | Yes | A data provider was unavailable; back off and retry. |
| `AUTH_MISSING`, `AUTH_INVALID`, `AUTH_EXPIRED` | No | Fix the key. |
| `INVALID_PARAMETERS`, `INVALID_SYMBOL`, `INVALID_LEG_COUNT` | No | Fix the request. |
| `PLAN_UPGRADE_REQUIRED`, `SCOPE_DENIED` | No | The key is not entitled to the operation. |
| `CLIENT_*` | No | The client rejected the input before sending it. |

## A retry helper

Honor `retry_after` when the engine sets it, and fall back to exponential backoff
otherwise.

```python
import time
from backtest360 import Backtest360Error

RETRYABLE = {"QUOTA_EXCEEDED", "ENGINE_BUSY", "COMPUTE_TIMEOUT", "PROVIDER_UNAVAILABLE"}

def with_retry(call, *, attempts=3):
    for attempt in range(attempts):
        try:
            return call()
        except Backtest360Error as e:
            if e.code not in RETRYABLE or attempt == attempts - 1:
                raise
            delay = e.retry_after if e.retry_after is not None else 2 ** attempt
            time.sleep(delay)

result = with_retry(lambda: client.backtest(strategy, df, risk=risk))
```

!!! note
    Retries multiply your quota usage. Cap the attempt count and always respect
    `retry_after` so you do not compound a throttle.
