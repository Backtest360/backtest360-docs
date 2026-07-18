# Exceptions

The Python client raises a single exception type for every failure: `Backtest360Error`.
It carries structured fields so you can branch on the cause rather than parse messages.

::: backtest360.client.Backtest360Error
    options:
      show_source: false

## Fields

| Field | Type | Meaning |
|------|------|---------|
| `status` | `int` | HTTP status from the engine, or `0` for errors the client raises before a request is sent. |
| `code` | `str \| None` | Stable machine-readable code (see the tables below). |
| `body` | `dict \| str \| None` | The engine's response body, when present — useful for validation details. |
| `request_id` | `str \| None` | Correlates the failure with an engine-side log entry. |
| `retry_after` | `float \| None` | Seconds to wait before retrying, set on throttling responses. |

Branch on `code` for programmatic handling and fall back to `status` for coarse
categories. See [Handle errors](../how-to/handle-errors.md) for retry patterns.

## Engine error codes

Returned by the engine with a non-zero HTTP `status`.

| Code | Status | When |
|------|--------|------|
| `AUTH_MISSING` | 401 | No `X-API-Key` header was sent. |
| `AUTH_INVALID` | 401 | The key is unknown or has been revoked. |
| `AUTH_EXPIRED` | 401 | The key has passed its expiry date. |
| `TENANT_SUSPENDED` | 403 | The account is suspended. |
| `SCOPE_DENIED` | 403 | The key lacks a scope the request requires. |
| `PLAN_UPGRADE_REQUIRED` | 403 | A paid-only feature was requested on a free key. |
| `BAR_LIMIT_EXCEEDED` | 413 | The request exceeds the key's bar cap. |
| `QUOTA_EXCEEDED` | 429 | A per-minute or per-day quota was hit. `retry_after` is set. |
| `CONCURRENCY_EXCEEDED` | 429 | Too many requests are in flight for the key. |
| `RATE_LIMITED` | 429 | An upstream data provider throttled the request (distinct from quota). |
| `INVALID_SYMBOL` | 400 | The requested symbol is unknown. |
| `INVALID_LEG_COUNT` | 400 | The number of legs falls outside the supported 1–20 range. |
| `INVALID_PARAMETERS` | 400 / 422 | A request parameter failed validation. |
| `DATA_ERROR` | 400 | The data provider returned an error for the request. |
| `PROVIDER_UNAVAILABLE` | 502 | A data provider is unavailable. |
| `ENGINE_BUSY` | 503 | Compute capacity is exhausted. |
| `COMPUTE_TIMEOUT` | 504 | The engine timed out while computing. |

## Client-side error codes

Raised by the client itself, before or after the request, always with `status` `0`.

| Code | Status | Meaning |
|------|--------|---------|
| `CLIENT_NO_API_KEY` | 0 | No API key was provided and none was found in the environment. |
| `CLIENT_PATH_FORBIDDEN` | 0 | A request targeted a path outside `/api/`. |
| `CLIENT_TEMPLATE_NOT_FOUND` | 0 | A named strategy template does not exist. |
| `CLIENT_INVALID_PAYLOAD` | 0 | A request payload failed client-side validation. |
| `CLIENT_INVALID_OHLCV` | 0 | The supplied OHLCV frame is missing columns, misindexed, or otherwise invalid. |
| `CLIENT_INVALID_SIGNALS` | 0 | The supplied signal series is invalid. |
| `CLIENT_INVALID_STRATEGY` | 0 | The strategy object failed client-side validation. |
| `CLIENT_MALFORMED_RESPONSE` | 0 | The engine returned a response the client could not parse. |

## Strategy validation errors

Strategy validation does not collapse to a single code. When a strategy document fails
validation, the engine returns a structured report and the client surfaces it in
`body`:

```json
{
  "valid": false,
  "errors": [
    {
      "code": "UNKNOWN_INDICATOR",
      "location": "legs[0].strategy.rules[0]",
      "message": "Indicator 'rsii' is not recognized.",
      "context": {"indicator": "rsii"}
    }
  ]
}
```

Each item names a specific problem — for example `UNKNOWN_INDICATOR`, `UNKNOWN_FIELD`,
or `INVALID_INDICATOR_PARAMS` — with the `location` in the document that caused it. Use
`validate_strategy()` to obtain this report without running a backtest.
