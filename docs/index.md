# Backtest360

**Backtest your trading strategy from every angle, in minutes.**

Backtest360 provides a cloud-based backtesting engine with a clean Python SDK and REST API. Plug in your OHLCV data, define a strategy with plain boolean expressions, and get a full performance report in seconds.

## Quick example

```python
import yfinance as yf
from backtest360 import Client, Strategy

df = yf.download("BTC-USD", period="3y", interval="1d",
    auto_adjust=False, multi_level_index=False, progress=False)
df.columns = df.columns.str.lower()

result = Client(api_key="b360_...").backtest(
    Strategy.rsi_threshold_long(), df
)
print(result.stats["Sharpe"])
result.equity.plot(title="Equity curve")
```

## Where to start

- **[Installation](getting-started/install.md)** — `pip install backtest360-client`
- **[Authentication](getting-started/authentication.md)** — get and use your API key
- **[Your first backtest](getting-started/first-backtest.md)** — end-to-end walkthrough
- **[SDK Reference](sdk-reference/client.md)** — full `Client`, `Strategy`, and helper class docs
- **[API Reference](api-reference/index.md)** — interactive REST API browser
