# Strategy Shape

A strategy tells the engine *when* to enter and exit positions. A strategy document has two parts:

- **Condition expressions** — boolean strings that reference indicator output columns, for example `"rsi < 30"`.
- **Indicator list** — which indicators to compute, and with what parameters.

The engine evaluates the expressions against the computed indicators on each bar to produce the signal series. How that signal becomes a fill is a separate concern — see [Signal vs execution](signal-vs-execution.md).

## Minimal example

```python
from backtest360 import Strategy

strat = Strategy(
    name="rsi_mean_reversion",
    long_entry="rsi < 30",
    long_exit="rsi > 70",
    indicators=[Strategy.indicator("rsi", period=14)],
)
```

The expression `"rsi < 30"` references the output column of the `rsi` indicator — `rsi` is the default column name when only one RSI is declared.

## Indicator refs

When you need the same indicator with different parameters, give each a unique `ref` and reference it by that name in your expressions:

```python
strat = Strategy(
    name="rsi_dual",
    long_entry="rsi_fast < 20",
    long_exit="rsi_slow > 80",
    indicators=[
        Strategy.indicator("rsi", ref="rsi_fast", period=5),
        Strategy.indicator("rsi", ref="rsi_slow", period=21),
    ],
)
```

## Crossovers via transform indicators

A transform indicator takes other indicators as input. `cross_above` fires when `upstream[0]` crosses above `upstream[1]`; `cross_below` is its mirror. Declare the two source indicators, then a transform that consumes them:

```python
strat = Strategy(
    name="sma_crossover",
    long_entry="x_above",
    long_exit="x_below",
    indicators=[
        Strategy.indicator("sma", ref="sma_10", period=10),
        Strategy.indicator("sma", ref="sma_50", period=50),
        Strategy.indicator(
            "cross_above", ref="x_above",
            kind="transform", upstream=["sma_10", "sma_50"],
        ),
        Strategy.indicator(
            "cross_below", ref="x_below",
            kind="transform", upstream=["sma_10", "sma_50"],
        ),
    ],
)
```

## Short selling

Add `short_entry` and `short_exit` expressions to trade both directions:

```python
strat = Strategy(
    name="rsi_long_short",
    long_entry="rsi < 30",
    long_exit="rsi > 50",
    short_entry="rsi > 70",
    short_exit="rsi < 50",
    indicators=[Strategy.indicator("rsi", period=14)],
)
```

## Pre-built templates

Each of these class methods returns a ready-to-run `Strategy`:

```python
Strategy.rsi_threshold_long()   # RSI(14) oversold entry / overbought exit
Strategy.rsi_mean_reversion()   # RSI(14) recovery-zone entry / neutral exit
Strategy.ma_crossover()         # SMA(10) x SMA(50) crossover
Strategy.momentum_6m_long()     # 6-month momentum, long when positive
```

They are a good starting point and a template for your own strategies.

## Browse the indicator library

`list_indicators()` returns every available indicator and its parameter schema:

```python
for ind in client.list_indicators():
    print(ind["name"], ind.get("params", {}))
```

Validate a strategy against the registry before running it with `client.validate_strategy(strat)`; see [Your first backtest](../getting-started/first-backtest.md). The full indicator and metric catalogs are also in the [API Reference](../api-reference/index.md).
