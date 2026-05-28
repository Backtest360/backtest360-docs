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
| `SDK_NO_API_KEY` | 0 | No API key provided (client-side) |
| `SDK_PATH_FORBIDDEN` | 0 | Attempted to access a non-`/api/` path (client-side) |
