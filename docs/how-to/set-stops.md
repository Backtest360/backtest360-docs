# Set Stops

Use the `Risk` dataclass to add stop-loss or drawdown protection.

## Fixed percentage stop

Exit the position if it moves against you by more than `value` percent:

```python
from backtest360 import Risk

risk = Risk(stop="fixed_pct", value=0.05)   # 5% stop
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
risk = Risk(stop="trailing_pct", value=0.08, max_drawdown=0.25)
```

## Pass to backtest

```python
result = client.backtest(strategy, df, risk=risk)
print(result.stats["Max Drawdown"])
```

## Stop types reference

| `stop` value | Description |
|---|---|
| `"fixed_pct"` | Fixed percentage below entry |
| `"trailing_pct"` | Trails the high-water mark by a fixed percent |
| `"fixed_atr"` | Fixed ATR multiple below entry |
| `"trailing_atr"` | Trails the high-water mark by ATR multiple |
| `None` | No stop (default) |
