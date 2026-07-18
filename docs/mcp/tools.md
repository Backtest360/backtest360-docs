# Tools

The MCP server exposes the Backtest360 engine as the tools below. Ground every name and
parameter in what the engine actually offers: discover with the catalog tools first,
validate a strategy document next, and only then run it.

## Discovery and reference

| Tool | What it does |
|------|--------------|
| `engine_info` | Engine version, API contract, and health. |
| `get_catalog` | A reference catalog — operators, execution modes, stop types, sizing methods, bar frequencies, or the metric sections. |
| `list_indicators` | List available indicators, or fetch one indicator's full parameter schema. |
| `list_templates` | List the built-in strategy templates, optionally filtered by name. |
| `get_strategy_schema` | The JSON Schema for a strategy document. |

## Strategies and backtests

| Tool | What it does |
|------|--------------|
| `validate_strategy` | Check a strategy document and return any errors to fix. |
| `run_backtest` | Run a historical backtest and return its performance. |
| `get_latest_signal` | Evaluate a strategy's most recent signal. |
| `compare_backtests` | Run several strategies and compare them side by side. |
| `compute_stats` | Compute performance statistics from returns. |

## Account

| Tool | What it does |
|------|--------------|
| `get_me` | The calling key's identity, plan, and limits. |

## Tickers and market data

| Tool | What it does |
|------|--------------|
| `search_tickers` | Search the available tickers. |
| `list_tickers` | List available tickers, optionally by asset class. |
| `get_ticker_info` | Reference detail for a single symbol. |
| `get_data_range` | The available date range for a symbol and frequency. |
| `get_quote` | The latest quote for a symbol. *(Paid plans.)* |
| `get_price_history` | Historical price bars for a symbol. *(Paid plans.)* |

## Macro data

| Tool | What it does |
|------|--------------|
| `list_macro_series` | List the available macroeconomic series. |
| `get_macro_series` | Fetch observations for a macroeconomic series. |

## Prompts

The server ships two prompts that package a multi-step workflow into a single request:

- `build_and_validate` — draft a strategy document and validate it before running.
- `robustness_review` — review a backtest for overfitting and fragility.

## Resources

Two resources expose reference data by URI:

- `backtest360://catalog/{name}` — a named reference catalog.
- `backtest360://schema/strategy` — the strategy document JSON Schema.

## Shaping backtest responses

`run_backtest` and `compare_backtests` accept two arguments that control how much they
return:

- `response_detail` — `summary` (the default), `stats`, or `full`. Start with `summary`
  and go deeper only when you need it.
- `include` — additional sections to attach: `trades`, `equity_curve`, `monthly_returns`,
  `yearly_returns`, `signal_diagnostics`.

Requesting only what you need keeps responses small and fast.

## Recommended flow

1. `engine_info` once to confirm the engine is reachable and which contract it serves.
2. `get_catalog` / `list_indicators` / `list_templates` to ground names and parameters in
   what exists.
3. `validate_strategy` until it passes.
4. `run_backtest` — start with a summary, request more detail only as needed.

Your API key's plan governs permissions, rate limits, and data access.

## Next

[Get an API key →](../getting-started/authentication.md)
