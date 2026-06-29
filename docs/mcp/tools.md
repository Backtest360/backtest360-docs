# Tools

The MCP server exposes the Backtest360 engine as the tools below. Ground every name and
parameter in what the engine actually offers — discover with the catalog tools first,
then validate before you run.

| Tool | What it does |
|------|--------------|
| `engine_info` | Engine version, API contract, and health. |
| `get_catalog` | A reference catalog: operators, execution modes, stop types, sizing methods, bar frequencies, or the metric sections. |
| `list_indicators` | List available indicators, or fetch one indicator's full parameter schema. |
| `get_strategy_schema` | The JSON Schema for a strategy document. |
| `validate_strategy` | Check a strategy document and return any errors to fix. |
| `run_backtest` | Run a historical backtest and return its performance. |
| `get_latest_signal` | Evaluate a strategy's most recent signal. |
| `compare_backtests` | Run several strategies and compare them. |
| `compute_stats` | Compute performance statistics from a result. |
| `search_tickers` | Search the available tickers. |
| `list_tickers` | List available tickers, optionally by asset class. |
| `get_data_range` | The available date range for a symbol and frequency. |

## Recommended flow

1. `engine_info` once to confirm the engine is reachable and which contract it serves.
2. `get_catalog` / `list_indicators` to ground names and parameters in what exists.
3. `validate_strategy` until it passes.
4. `run_backtest` — start with a summary, go deeper only as needed.

Your API key's plan governs permissions, rate limits, and data access.

## Next

[Get an API key →](../getting-started/authentication.md)
