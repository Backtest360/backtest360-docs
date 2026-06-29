# Strategy Shape

A strategy tells the engine *when* to enter and exit positions. It contains two parts:

- **Condition expressions** — boolean strings referencing indicator output columns
- **Indicator list** — which indicators to compute, and with what parameters

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

The expression `"rsi < 30"` references the `output` column of the `rsi` indicator (the default ref when only one RSI is declared).

## Indicator refs

When you need the same indicator with different parameters, give each a unique `ref`:

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

## Crossover via transform indicators

Transform indicators take other indicators as input. `cross_above` fires when `upstream[0]` crosses above `upstream[1]`:

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

Add `short_entry` and `short_exit` expressions:

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

```python
Strategy.rsi_threshold_long()   # RSI(14) oversold entry / overbought exit
Strategy.rsi_mean_reversion()   # RSI(14) recovery-zone entry / neutral exit
Strategy.ma_crossover()         # SMA(10) × SMA(50) crossover
Strategy.momentum_6m_long()     # ROC(126) > 0
```

These `Strategy.*` class methods are the available pre-built templates — each returns a ready-to-run `Strategy`.

## Indicator library

Browse available indicators and their parameter schemas:

```python
for ind in client.list_indicators():
    print(ind["name"], ind.get("params", {}))
```

Or see the [API Reference](../api-reference/index.md).
