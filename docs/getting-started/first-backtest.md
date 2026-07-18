# Your First Backtest

This walkthrough runs an RSI mean-reversion strategy end to end and reads the result. It uses the engine's bundled sample data, so it runs with nothing more than the client and an API key.

It assumes your key is in the `BACKTEST360_API_KEY` environment variable — see [Authentication](authentication.md).

## 1. Load sample data

The engine serves a small set of ready-made daily OHLCV datasets. `sample_data()` returns one as a `DataFrame` with a datetime index and lowercase `open`/`high`/`low`/`close`/`volume` columns — exactly the shape `backtest()` expects.

```python
from backtest360 import Client

client = Client()                 # reads BACKTEST360_API_KEY
print(client.sample_symbols())    # ['SPY', 'QQQ', 'BTC']

df = client.sample_data("BTC")
print(df.columns.tolist())        # ['open', 'high', 'low', 'close', 'volume']
```

To run against your own market data instead, see [Use your own data](../how-to/use-your-own-data.md); the frame shape is identical.

## 2. Build a strategy

The quickest start is a built-in template. Each `Strategy.*` class method returns a ready-to-run strategy.

```python
from backtest360 import Strategy

strategy = Strategy.rsi_threshold_long()
# long entry when RSI(14) is oversold; long exit when it is overbought
```

See [Strategy shape](../concepts/strategy-shape.md) to build your own from indicators and boolean expressions.

## 3. Validate

`validate_strategy()` checks a strategy against the engine's indicator registry without running a backtest — a cheap way to catch an unknown indicator or a malformed expression before you spend a run on it.

```python
report = client.validate_strategy(strategy)
print(report["valid"])   # True
```

If a strategy is invalid, `report["errors"]` lists each problem with a `code`, `location`, and `message`.

## 4. Run the backtest

`backtest()` takes the strategy and the price frame and returns a `Result`.

```python
result = client.backtest(strategy, df)
```

## 5. Read the result

Statistics are keyed by stable metric id — snake_case strings like `sharpe` and `max_drawdown`. (Pass `stats_keys="labels"` if you would rather key by display label, e.g. `"Sharpe"`; every example here uses ids.)

```python
print(result.stats["sharpe"])         # e.g. 1.42  — Sharpe
print(result.stats["cagr"])           # e.g. 0.318 — CAGR
print(result.stats["max_drawdown"])   # e.g. -0.42 — Max Drawdown
print(result.stats["trade_win_rate"]) # fraction of winning trades
```

The values above are illustrative — your run reports its own numbers. For a compact human-readable overview, call `summary()`:

```python
result.summary()
# Performance Summary
# ─────────────────────────────
# Total Return           8.3%
# Annualized Return     12.1%
# Annualized Std Dev    18.4%
# Sharpe Ratio           1.42
```

The equity curve and the trade log are plain pandas and Python:

```python
result.strategy_equity.plot(title="Equity curve")   # pd.Series, datetime-indexed

for trade in result.trades[:5]:
    print(trade["entry_date"], trade["direction"], trade["return_net"])
```

`result.stats` holds the headline metric set for your plan (roughly two dozen on the free tier). The full catalog of metric ids, labels, and descriptions is served by the engine at `GET /api/sections`. [Result anatomy](../concepts/result-anatomy.md) documents every field of a `Result`.

## 6. Understand the warmup boundary

Indicators need history before they produce a value — an RSI(14) has nothing to say until 14 bars have passed. The engine calls this the warmup period, and it will not open a position inside it. `result.markers` reports where that boundary falls, along with the first and last trade positions:

```python
m = result.markers
print(m["warmup_bars"])       # bars consumed by warmup
print(m["warmup_end_date"])   # first date the strategy could trade
print(m["first_trade_date"])  # when it actually first traded
```

`markers` is for annotating a chart — the warmup boundary and the trade span — not a per-bar event log. Fields are `None` when they do not apply, for example `first_trade_date` on a run that never traded.

## 7. Add transaction costs

A frictionless backtest overstates returns. Model costs with a `Costs` config:

```python
from backtest360 import Costs

result = client.backtest(
    strategy, df,
    costs=Costs(slippage_bps=5.0, fee_pct=0.001),
)
```

Costs are reflected per trade — compare `return_gross` and `return_net`, `fee_cost`, and `slippage_cost` in `result.trades`. To add stop-losses and drawdown circuit-breakers, see [Set stops and risk limits](../how-to/set-stops.md).

## 8. Add a benchmark

Pass a second price frame as `benchmark`, and the engine computes benchmark-relative metrics, surfaced on `result.relative` — a dict keyed the same way as `result.stats`.

```python
spy = client.sample_data("SPY")

result = client.backtest(strategy, df, benchmark=spy)

print(result.relative["alpha"])              # Alpha
print(result.relative["beta"])               # Beta
print(result.relative["information_ratio"])  # Information Ratio
print(result.relative["up_capture"])         # Up Capture
print(result.relative["down_capture"])       # Down Capture
```

`result.benchmark_equity` carries the benchmark's equity curve; it is an empty `Series` when no benchmark was supplied.

## Next steps

- [Signal vs execution](../concepts/signal-vs-execution.md) — how a signal turns into a fill, and the lag-1 no-look-ahead rule.
- [Result anatomy](../concepts/result-anatomy.md) — every field on a `Result` and how the metrics are keyed.
- [Handle errors](../how-to/handle-errors.md) — what the engine returns when a request fails, and how to react.
- [Client Reference](../client-reference/client.md) — the full API.
