# Your First Backtest

This walkthrough runs an RSI mean-reversion strategy on BTC-USD daily data.

## 1. Fetch data

The client accepts any DataFrame with `open`, `high`, `low`, `close` columns (lowercase) and a datetime index.

```python
import yfinance as yf

df = yf.download(
    "BTC-USD", period="3y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False,
)
df.columns = df.columns.str.lower()
print(df.shape)   # (1095, 5)
```

## 2. Pick a strategy

Use a built-in template to get started:

```python
from backtest360 import Strategy

strategy = Strategy.rsi_threshold_long()
# long entry: RSI(14) < 30   (oversold)
# long exit:  RSI(14) > 70   (overbought)
```

See [Strategy Shape](../concepts/strategy-shape.md) for how to build custom strategies.

## 3. Run the backtest

```python
from backtest360 import Client

result = Client(api_key="b360_...").backtest(strategy, df)
```

## 4. Inspect results

```python
# Key stats
print(result.stats["Sharpe"])          # e.g. 1.42
print(result.stats["CAGR"])            # e.g. 0.318
print(result.stats["Max Drawdown"])    # e.g. -0.42

# Equity curve
result.strategy_equity.plot(title="Equity curve")

# Trade log
for trade in result.trades[:5]:
    print(trade["entry_date"], trade["direction"], trade["return_net"])

# All stats
print(result.stats)   # full dict with 120+ metrics
```

## 5. Add transaction costs

```python
from backtest360 import Costs

result = Client(api_key="b360_...").backtest(
    strategy, df,
    costs=Costs(slippage_bps=5.0, fee_pct=0.001),
)
```

## 6. Add a benchmark

Pass a second DataFrame to get Alpha, Beta, Up/Down Capture, and Information Ratio:

```python
spy = yf.download("SPY", period="3y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False)
spy.columns = spy.columns.str.lower()

result = Client(api_key="b360_...").backtest(strategy, df, benchmark=spy)
print(result.stats["Alpha"])
print(result.stats["Beta"])
```

## Next

- [Concepts: Strategy Shape](../concepts/strategy-shape.md)
- [Client Reference](../client-reference/client.md)
