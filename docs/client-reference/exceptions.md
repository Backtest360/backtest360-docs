# Exceptions

::: backtest360.client.Backtest360Error
    options:
      show_source: false

## Common error codes

| Code | Status | Meaning |
|------|--------|---------|
| `AUTH_INVALID` | 401 | API key not found or malformed |
| `AUTH_REVOKED` | 401 | Key has been revoked |
| `AUTH_EXPIRED` | 401 | Key has passed its expiry date |
| `RATE_LIMIT_EXCEEDED` | 429 | Per-minute or per-day limit hit |
| `CONCURRENCY_LIMIT` | 429 | Per-key concurrency cap reached |
| `VALIDATION_ERROR` | 422 | Strategy or request payload invalid |
| `CLIENT_NO_API_KEY` | 0 | No API key provided (client-side) |
| `CLIENT_PATH_FORBIDDEN` | 0 | Attempted to access a non-`/api/` path (client-side) |

## Input-validation error codes

Raised by the client itself (status `0`) — either when the data or arguments you pass fail its pre-flight checks, or when the engine's response cannot be parsed:

| Code | Status | Meaning |
|------|--------|---------|
| `CLIENT_MALFORMED_RESPONSE` | 0 | The engine returned a response the client could not parse |
| `CLIENT_INVALID_PAYLOAD` | 0 | Request payload failed client-side validation |
| `CLIENT_INVALID_OHLCV` | 0 | The supplied OHLCV DataFrame is missing columns, misindexed, or otherwise invalid |
| `CLIENT_INVALID_SIGNALS` | 0 | The supplied signal series is invalid |
| `CLIENT_INVALID_STRATEGY` | 0 | The strategy object failed client-side validation |
