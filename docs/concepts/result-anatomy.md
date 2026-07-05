# Result Anatomy

`client.backtest()` returns a `Result` object wrapping the full engine response.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| `result.stats` | `dict` | 120+ performance metrics |
| `result.trades` | `list[dict]` | Full trade log |
| `result.strategy_equity` | `pd.Series` | Strategy equity curve, datetime-indexed |
| `result.benchmark_equity` | `pd.Series` | Benchmark equity curve (empty when no benchmark supplied) |
| `result.returns` | `pd.Series` | Net-of-cost log-return series |
| `result.signals` | `pd.Series` | Signal series `{-1, 0, 1}` |
| `result.markers` | `dict` | Per-bar event markers (entries, exits, stop hits); empty when not applicable |
| `result.data_quality` | `dict` | The engine's assessment of the input data quality |
| `result.raw` | `dict` | Full engine response dict |

`result.summary()` is a convenience **method** (not a property) that prints a compact, human-readable performance summary to stdout.

## Key stats

```python
# Return metrics
result.stats["CAGR"]              # Compound annual growth rate
result.stats["Total Return"]      # Gross return over the period
result.stats["Sharpe"]            # Annualised Sharpe ratio
result.stats["Sortino"]           # Sortino ratio (downside deviation)
result.stats["Calmar"]            # CAGR / Max Drawdown

# Risk metrics
result.stats["Max Drawdown"]      # Worst peak-to-trough drawdown
result.stats["Vol (Ann)"]         # Annualised return volatility

# Trade metrics
result.stats["Trade Win Rate"]    # Fraction of winning trades
result.stats["Profit Factor"]     # Gross profit / gross loss
result.stats["Avg Trade P&L"]     # Average net trade return

# Benchmark metrics (when benchmark supplied)
result.stats["Alpha"]             # Jensen's Alpha (log-space)
result.stats["Beta"]              # Market beta
result.stats["Up Capture"]        # Up-market capture ratio (Morningstar)
result.stats["Down Capture"]      # Down-market capture ratio
result.stats["Information Ratio"] # Active return / tracking error
```

## Trade log fields

Each entry in `result.trades`:

```python
{
    "entry_date":    "2023-01-15",
    "exit_date":     "2023-02-03",
    "direction":     1,             # 1=long, -1=short
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

## Accessing all metrics

```python
for name, value in sorted(result.stats.items()):
    print(f"{name:30s} {value}")
```

See [Client Reference](../client-reference/client.md) for the full `Result` API.
