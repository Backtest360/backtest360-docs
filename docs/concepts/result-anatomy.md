# Result Anatomy

`client.backtest()` and `client.backtest_signals()` return a `Result` — a thin wrapper over the engine response that exposes the pieces you need as pandas and Python objects.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| `result.stats` | `dict` | Performance metrics, keyed by metric id (see below). |
| `result.trades` | `list[dict]` | The full trade log. |
| `result.strategy_equity` | `pd.Series` | Strategy equity curve, datetime-indexed. |
| `result.benchmark_equity` | `pd.Series` | Benchmark equity curve; empty `Series` when no benchmark was supplied. |
| `result.returns` | `pd.Series` | Net-of-cost log-return series. |
| `result.signals` | `pd.Series` | Signal series with values in `{-1, 0, 1}`. |
| `result.markers` | `dict` | Warmup boundary and first/last trade positions (see below). |
| `result.data_quality` | `dict` | The engine's assessment of the input data. |
| `result.raw` | `dict` | The full engine response, for any field not exposed above. |

`result.summary()` is a **method**, not a property. It prints a compact overview — Total Return, Annualized Return, Annualized Std Dev, and Sharpe — to stdout and returns `None`:

```python
result.summary()
# Performance Summary
# ─────────────────────────────
# Total Return           8.3%
# Annualized Return     12.1%
# Annualized Std Dev    18.4%
# Sharpe Ratio           1.42
```

## How stats are keyed

By default `result.stats` is keyed by stable **metric id** — snake_case strings that do not change between releases. This is what every example in these docs uses:

```python
result.stats["sharpe"]         # 1.42
result.stats["max_drawdown"]   # -0.42
```

Pass `stats_keys="labels"` to the request method to key by human-readable display label instead (`result.stats["Sharpe"]`). Prefer ids in code — labels can change; ids are the contract.

### Common metrics

| id | label | Meaning |
|----|-------|---------|
| `total_return` | Total Return | Cumulative return over the period. |
| `cagr` | CAGR | Compound annual growth rate. |
| `vol_ann` | Vol (Ann) | Annualized return volatility. |
| `sharpe` | Sharpe | Annualized Sharpe ratio. |
| `sortino` | Sortino | Sortino ratio (downside deviation). |
| `calmar` | Calmar | CAGR divided by max drawdown. |
| `max_drawdown` | Max Drawdown | Worst peak-to-trough drawdown. |
| `win_rate` | Win Rate | Fraction of **positive days**. |
| `profit_factor` | Profit Factor | Gross profit divided by gross loss (trade-based). |
| `avg_trade_pnl` | Avg Trade P&L | Average net trade return. |

`total_return` is a real metric id in `result.stats`, distinct from the Total Return line that `summary()` derives from the equity curve.

!!! warning "`win_rate` is not the trade win rate"
    `win_rate` measures the fraction of **positive days**. For the fraction of **winning trades**, use `trade_win_rate`. (A related `signal_win_rate` exists for signal-level analysis.) Reach for the one your question actually means — they routinely differ.

### Benchmark metrics

When you pass a `benchmark`, the engine computes benchmark-relative metrics and the client surfaces them on `result.relative` — a dict keyed the same way as `result.stats` (they are deliberately not mixed into `result.stats`).

| id | label |
|----|-------|
| `alpha` | Alpha |
| `beta` | Beta |
| `information_ratio` | Information Ratio |
| `tracking_error` | Tracking Error |
| `up_capture` | Up Capture |
| `down_capture` | Down Capture |
| `capture_ratio` | Capture Ratio |

```python
result = client.backtest(strategy, df, benchmark=spy)
print(result.relative["alpha"], result.relative["beta"])
```

`result.relative` is an empty dict when no benchmark was supplied.

### The full catalog

`result.stats` carries the headline metric set available to your plan (roughly two dozen on the free tier). The engine's complete catalog — every id, its label, and a description — is served by `GET /api/sections`. Do not assume a fixed count in code; iterate over what the run returned:

```python
for metric_id, value in sorted(result.stats.items()):
    print(f"{metric_id:24s} {value}")
```

## Trade log fields

Each entry in `result.trades` is a dict:

```python
{
    "entry_date":    "2023-01-15",
    "exit_date":     "2023-02-03",
    "direction":     1,             # 1 = long, -1 = short
    "entry_price":   16800.0,
    "exit_price":    23400.0,
    "return_gross":  0.0392,
    "return_net":    0.0387,        # after costs
    "fee_cost":     -0.0002,
    "slippage_cost": -0.0003,
    "bars_held":     19,
    "stop_hit":      False,
}
```

## Markers

`result.markers` marks the run's boundaries for chart annotation. It is **not** a per-bar event stream — it reports the warmup boundary and the first/last trade positions:

| Field | Meaning |
|-------|---------|
| `warmup_bars` | Bars consumed before the strategy could trade. |
| `warmup_end_index` / `warmup_end_date` | Where the warmup period ends. |
| `first_trade_index` / `first_trade_date` | The first trade's entry. |
| `last_trade_exit_index` / `last_trade_exit_date` | The last trade's exit. |

Individual fields are `None` when they do not apply (for example, the trade fields on a run that never traded), and `markers` is an empty dict when the engine response carries none.

## Data quality

`result.data_quality` reports issues the engine found while preparing the run — bad prices, missing bars, and warnings. It is an empty dict when the response carries no data-quality block. Inspect it when results look surprising; a gap in the input often explains a gap in the output.

## See also

- [Client Reference](../client-reference/client.md) — the full `Result` API.
- [Your first backtest](../getting-started/first-backtest.md) — a `Result` read end to end.
