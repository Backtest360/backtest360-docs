# Set Stops

Use the `Risk` configuration to add a stop-loss or a drawdown limit to a backtest. Build
a `Risk` object and pass it to `backtest()` with the `risk=` keyword.

## Fixed percentage stop

Exit the position if it moves against you by more than `value` percent:

```python
from backtest360 import Risk

risk = Risk(stop="fixed", value=0.05)   # 5% stop
```

## Trailing ATR stop

Trail the stop at `value` × ATR(14) below the running high-water mark:

```python
risk = Risk(stop="trailing_atr", value=2.5, atr_period=14)
```

## Circuit-breaker drawdown limit

Flatten the position if the running drawdown exceeds 20%:

```python
risk = Risk(max_drawdown=0.20)
```

## Combine stops and drawdown limit

```python
risk = Risk(stop="trailing", value=0.08, max_drawdown=0.25)
```

## Pass to backtest

Pass the `Risk` object to `backtest()` and read the realized drawdown back from the
statistics. Metrics are keyed by their stable id, so `max_drawdown` is the drawdown value:

```python
result = client.backtest(strategy, df, risk=risk)
print(result.stats["max_drawdown"])   # e.g. -0.18
```

## Stop types reference

| `stop` value | Description |
|---|---|
| `"fixed"` | Fixed percentage below entry |
| `"trailing"` | Trails the high-water mark by a fixed percent |
| `"atr"` | Fixed ATR multiple below entry |
| `"trailing_atr"` | Trails the high-water mark by ATR multiple |
| `None` | No stop (default) |
